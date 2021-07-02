
# How to create Lamp ec2 instance 
Before doing all of the work in aws-CLI, First we need to configure the AWS-CLI from command prompt we need to give access to our account with using "access key id" and "secret access key"
For configure the aws cli account we need to execute 

 # aws configure 

'''C:\Users\admin>aws configure

AWS Access Key ID [****************PSPV]: AKIA********************

AWS Secret Access Key [****************B9qp]: *****************************************

Default region name [us-east-1]: region

Default output format [JSON]: 

in command prompt and after executing this we can give the access key id, secret access key, format of file, region these credentials we need to put in that after configuration our command prompt is ready to run another commands of aws cli.
# Diagram
![Diagram](https://github.com/sayanVampire60231023/Lamp-Server-/blob/master/Screenshot%20(284).png)
# Create a Vpc 

Amazon VPC allows us to create a virtual network in the AWS cloud without the need for VPNs, hardware, or physical data centers. We have the ability to design our own network space and regulate how your network and Amazon EC2 resources within network are exposed to the Internet. For creating vpc 

Syntax:
- ```aws  ec2 create-vpc --cidr-block 10.0.0.0/16  ```

This command is going to create a vpc in our console but without their name so we need to give the name of vpc, for naming purpose we use tagging so,The below command is going to create the tag
- "aws ec2 create-tags --resources <VPC_ID> --tags Key=Name,Value=sweta-vpc"

after executing this command we can see the aws console, here we can see that the vpc with name 'saya_vpc' will be created

# Create Subnet 

If you create a VPC or subnet using a command line tool or the Amazon EC2 API, the CIDR block is automatically modified to its canonical form. For example, if you specify 100.68.0.18/18 for the CIDR block, we create a CIDR block of 100.68.0.0/18.

This command will create subnet in console with using the vpc_ID which we create earlier

- ``` aws ec2 create-subnet --vpc-id <vpc id> --cidr-block 10.0.1.0/24  ```

The above command will create public subnet but without naming here we can see the subnet ID  

-  ``` aws ec2 create-tags --resources <subnet_ID> --tags  Key=Name,Value=publiv-subnetSaya ```

This above command will create public subnet with name publiv-subnetSaya. 

If a subnet is associated with a route table that has a route to an internet gateway, it's known as a public subnet. If a subnet is associated with a route table that does not have a route to an internet gateway, it's known as a private subnet.

# Internet gateway

An internet gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet. An internet gateway supports IPv4 and IPv6 traffic. It does not cause availability risks or bandwidth constraints on your network traffic.

We can create internet gateway by using the syntax:
-  ``` aws ec2 create-internet-gateway ```
 
It will give the output of Internet gateway ID

This command will create tags for the internet gateway
-  ``` aws ec2 create-tags --resources  <internet-gateway-id> --tags  Key=Name,Value=saya-InternetGateway ```
  
  Attach or detach:
  After you create an internet gateway, attach it to your VPC. For attach, we can use the command
  
-  ``` aws ec2 attach-internet-gateway --internet-gateway-id <Internet gateway ID> --vpc-id <Your vpc id> ```
  
# Allocation of an elastic IP address
-  ``` aws ec2 allocate-address --domain vpc ```
  
It will Allocates an Elastic IP address to your account. After you allocate the Elastic IP address you can associate it with an instance or network interface. After you release an Elastic IP address, it is released to the IP address pool and can be allocated to a different account.We are creating this beacuse the allocation id is going to use in  creating a nat-gateway.
  
# Nat gateway 
  A NAT gateway is a service that provides Network Address Translation (NAT). A NAT gateway can be used to allow instances on a private subnet to access to services outside your   VPC, but external services cannot initiate a connection to those instances.
  
 Following are the syntax for creating a nat-gateway
-   ``` aws ec2 create-nat-gateway --subnet-id <your subnet id> --allocation-id <allocation id> ```
 
 Each NAT gateway is created in a different Availability Zone and is implemented with redundancy in that zone. Each Availability Zone has a limit on the number of NAT gateways that can be created.  
  
-  ``` aws ec2 create-tags --resources  <nat-gateway id> --tags  Key=Name,Value=saya-NatGateway ```
  This command is going to create tag for nat-gateway. tags are basically for the naming purpose.
  
# create route table
  A route table contains a set of rules, called routes, that are used to determine where network traffic from your subnet or gateway is directed.
  
  syntax for creating:
-  ``` aws ec2 create-route-table --vpc-id <vpc id> ```
  
Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet. Every subnet that you create is automatically    associated with the main route table for the VPC. You can change the association, and you can change the contents of the main route table.

- ``` aws ec2 create-tags --resources (route-table id) --tags Key=Name,Value=saya-RoutePublic ```
  This will create the tags for public route table.

-  ``` aws ec2 create-tags --resources  <route table id>  --tags Key=Name,Value=saya-RoutePrivate ``` 
  This will create tags for public route table.
  
-  ``` aws ec2 create-route  --route-table-id <route table id> --destination-cidr-block 0.0.0.0/0 --gateway-id <internet gateway id> ```
  
 Creates a route in a route table within a VPC. You must specify one of the following targets: internet gateway or virtual private gateway, NAT instance, NAT gateway, VPC peering connection, network interface, egress-only internet gateway, or transit gateway.
When determining how to route traffic, we use the route with the most specific match.
  
  
Now, we are going to associate route table with aur subnet .
  
 A route table in your VPC is associated with a subnet in your VPC or an internet gateway or virtual private gateway related to your VPC. Traffic from the subnet or gateway is routed according to the routes in the route table as a result of this association. The action returns an association ID, which you'll need later to unlink the route table. A route table can be linked to several subnets.
  
For association we can use the command  
- ``` aws ec2   associate-route-table --route-table-id  <route table id>  --subnet-id <subnet-id> ```
 
# Security group:
 
A security group controls incoming and outgoing traffic for your EC2 instances by acting as a virtual firewall. Inbound rules govern incoming traffic to your instance, whereas outbound rules govern incoming traffic from your instance. You can define one or more security groups when you begin an instance. If no security group is specified, Amazon EC2 utilizes the default security group. 
  
 syntax for creating a security group
- ``` aws ec2 create-security-group  --group-name Saya-security-grp --description "My security grp" --vpc-id <vpc id> ```
  
  This gonna give you the security group id
 after creating security group we need to give them sone name so we will execute this command for creating tags  
- "aws ec2 create-tags --resources <Security group id>  --tags Key=Name,Value=Saya-CLI-SecurityGrp"

The provided ingress rules are added to a security group.

An inbound rule allows instances to receive traffic from IPv4 or IPv6 CIDR address ranges, or from instances associated with the specified destination security groups.
- "aws ec2 authorize-security-group-ingress --group-id <security group id> --protocol tcp --port 22 --cidr 0.0.0.0/0"
  
# Creating Key-pair   
  ``` aws ec2 create-key-pair --key-name SayaKEY123 --query 'KeyMaterial' --output text > MyClisaya123  ```

 This keypair is going to use for executing out lamp server
 
# Creating a Lamp stack

### LAMP stands for
- Linux 
- Apache  
- MySQL  
- PHP 
  Together, they provide a proven set of software for delivering high-performance web applications. Each component contributes essential capabilities to the stack. LAMP has a classic layered architecture, with Linux at the lowest level. The next layer is Apache and MySQL, followed by PHP. Although PHP is nominally at the top or presentation layer, the PHP component sits inside Apache.

- ``` sudo apt-get update -y && sudo apt-get upgrade -y ```

Above command will update and upgrade apt package. This package contains all the commands of linux

- ``` sudo apt-get install apache2 apache2-doc apache2-mpm-prefork apache2-utils libexpat1 ssl-cert -y ```

Above command will install apache web server. Web servers are used to serve Web pages requested by client computers. Clients commonly request and view Web sites using Web browser software such as Firefox, Opera, Chromium, or Internet Explorer.

- ``` sudo apt-get install libapache2-mod-php7.0 php7.0 php7.0-common php7.0-curl php7.0-dev php7.0-gd php-pear php7.0-mcrypt php7.0-mysql -y"

This command will install php and the requirements for Lamp server. PHP is a server scripting language, and a powerful tool for making dynamic and interactive Web pages. PHP is easy and powerful language. It is powerful enough to be at the core of the biggest blogging system on the web (WordPress)!. It is deep to run any big plateform and easy also.

- ``` sudo apt-get install mysql-server mysql-client -y ```

Above command is going to install mysql server client. MySQL is the world's most popular open-source database. Despite its powerful features, MySQL is simple to set up and easy to use.

we need to change the ownership to access the file
- ```sudo chown -R www-data:www-data /var/www ```

this below commands is going to enable the  apache2 service and php . If you look at the a2enmod command it’s rather simple. a2enmod = Apache2 Enabled ModThe a2enmod command actually moves the Apache module files from /etc/apache2/mods-available to /etc/apache2/mods-enabled. And if you look at phpenmod it is going to enable the php for encrypt the files.
- ``` sudo a2enmod rewrite ```
-  ``` sudo phpenmod mcrypt ```

  Restarting the apache2 server

- ``` sudo service apache2 restart ```

after that the Lamp server is getting install in your system. 
