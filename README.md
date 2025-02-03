# Setting Up and Securing VPC ☁️🛡️

## Objective
This project demonstrates how to set up and secure a Virtual Private Cloud (VPC) in AWS. It covers the entire process of creating a VPC, configuring subnets, setting up route tables, creating and attaching an Internet Gateway (IGW), configuring a NAT Gateway, implementing security groups and network ACLs (Access Control Lists), and launching EC2 instances within the VPC. The goal is to establish a secure and well-architected AWS network environment that allows controlled access while maintaining security best practices.

## Tasks Covered
- **Creating a VPC** – Defining a new VPC with a custom CIDR block.

- **Creating Public and Private Subnets** – Segmenting the VPC into public and private subnets.
- **Creating and Attaching an Internet Gateway (IGW)** – Enabling internet access for public subnets.
- **Setting Up a NAT Gateway** – Allowing private subnet instances to access the internet securely.
- **Configuring Route Tables** – Associating route tables with subnets to manage traffic flow, including internet access via IGW and private subnet access via NAT Gateway.
- **Configuring Security Groups** – Defining inbound and outbound rules for database servers and web servers.
- **Implementing Network ACLs (Access Control Lists)** – Adding an additional layer of security at the subnet level to control traffic permissions.
- **Launching and Connecting to EC2 Instances** – Deploying instances within the configured VPC and testing connectivity via SSH and HTTP.
- **Verifying Access and Testing Connectivity** – Confirming network configurations by accessing the web server from a browser and testing database access through the web server.
____________________________________________________________________________________________
### Creating a VPC
![vpc creation configurations](https://github.com/user-attachments/assets/b8576a82-7e84-46b4-b748-5f6b39a6314d)

- Created a VPC and named it "hashim-vpc"
- Manually specified an IPv4 CIDR block
- The CIDR block defines the range of available IP addresses in the VPC
- The slash notation (e.g., /16, /24) indicates how many bits are allocated for the network address. The remaining bits are used for host addresses
- Opted not to use an IPv6 CIDR block for this project, focusing solely on IPv4

____________________________________________________________________________________________
### Creating Subnets
#### Public Subnet
![PublicSubnet1](https://github.com/user-attachments/assets/af1172dd-fdd6-45f0-be2a-840fb7267443)

- Created a public subnet named "PublicSubnet-1"
- The subnet is located in the **us-east-2a** Availability Zone
- Assigned a CIDR block of 10.0.5.0/24 to the subnet
#### Private Subnet
![PrivateSubnet1](https://github.com/user-attachments/assets/a4f8e46f-a357-49da-bdbe-da69ef9dcf02)
- Created a private subnet named "PrivateSubnet-1"
- The subnet is located in the **us-east-2a** Availability Zone
- Assigned a CIDR block of 10.0.6.0/24 to the subnet
___________________________________________________________________________________________________
### Creating and Attaching an Internet Gateway
![igw](https://github.com/user-attachments/assets/4d591932-fe09-40ed-bf95-42dd68f325d1)

- Created an Internet Gateway named "hashim-igw"

![attaching igw to vpc](https://github.com/user-attachments/assets/49e597dc-7a2c-4ab3-a8e5-6521080668e4)
![attaching igw to vpc 2](https://github.com/user-attachments/assets/9e523b70-4512-4837-a93b-a5f96babd54f)

- After successfully creating an Internet Gateway I attached it to my VPC (hashim-vpc)
__________________________________________________________________________________________________
### Setting Up a NAT Gateway
![creating NAT gateway](https://github.com/user-attachments/assets/3dcf32f8-0c9a-4e0e-a03a-14db9294e00e)

- Created a NAT gateway named hashim-NAT-gateway
- Deployed the NAT gateway in the PublicSubnet-1 subnet
- Selected a public connectivity type to enable internet access for the NAT gateway
- Allowed AWS to automatically allocate an Elastic IP to the NAT gateway

_____________________________________________________________________________________________________
### Setting Up Route Tables
#### Public Route Table
![PublicRouteTable](https://github.com/user-attachments/assets/552747fe-8eda-4d71-8eb2-e188f6d16811)

- Created a public route table named PublicRouteTable.
- Associated the route table with hashim-vpc to manage routing within the VPC.

![route table allows internet access through igw](https://github.com/user-attachments/assets/561a1b29-44bb-4dce-bf63-81da35e9f778)

- Configured a destination 0.0.0.0/0 (default route) to forward all outbound traffic.
- Set the target as hashim-igw to route traffic outside the VPC.

![publicroutetable officially associated with public subnets](https://github.com/user-attachments/assets/1e95f5ed-012e-49ac-bdc0-bdc808f1c6c4)

- Successfully associated PublicRouteTable with PublicSubnet-1 and PublicSubnet-2.
#### Private Route Table
![PrivateRouteTable](https://github.com/user-attachments/assets/023003a7-cb82-4eee-8a57-923b8626a731)

- Created a public route table named PublicRouteTable.
- Associated the route table with hashim-vpc to manage routing within the VPC.
  
![privateroutetable target is now NAT gateway](https://github.com/user-attachments/assets/3c0c665e-7770-4c72-a1ed-806d5799d69a)

- Set the target of the route to hashim-NAT-gateway, enabling private subnet instances to access the internet securely through the NAT Gateway.

![PrivateRouteTable officially associated with private subnets](https://github.com/user-attachments/assets/9fc2a28e-0006-475f-b71d-8e989fc4a068)

- Successfully associated PrivateRouteTable with PrivateSubnet-1 and PrivateSubnet-2.

________________________________________________________________________________________________________________
### Setting Up Security Groups
#### Web Server Security Group (Publicly Accessible) 
![security group for web server (public access)](https://github.com/user-attachments/assets/2e36d197-7e5e-4e8b-bbce-a692dee8657d)

- Created a security group named WebServerSG for my web server instance.
- Made the security group publicly accessible to allow internet traffic.
- Configured inbound rules:
  - Allowed HTTP (port 80) and HTTPS (port 443) traffic from anywhere.
  - Restricted SSH (port 22) traffic to only allow connections from my IP address for secure access.
#### Database Server Security Group (Not Publicly Accessible)
![security group for database server](https://github.com/user-attachments/assets/fac34bef-d91f-4d71-b308-048bb8fade1a)

- Created a security group named DatabaseServerSH for database server instances.
- Configured inbound rules to:
  - Allow MySQL/Aurora (port 3306) traffic **only** from the webserversg security group.
_____________________________________________________________________________________________________________________
### Configuring ACLs
#### Public Subnet ACL
![public subnet acl](https://github.com/user-attachments/assets/54702b1e-2843-4bdb-977c-f96aea803514)

- Created a Network ACL named PublicSubnetACL for the public subnet.

![public subnet acl inbound rules 1](https://github.com/user-attachments/assets/2bc6d571-d5e1-4c18-a05a-04c98f949757)

- Configured inbound rules to:
  - Allow SSH (port 22), HTTP (port 80), and HTTPS (port 443) traffic from anywhere.
  - Any other type of traffic will be denied by default
#### Private Subnet ACL 
![Private Subnet ACL](https://github.com/user-attachments/assets/ae79a957-45cb-4dfb-9488-8fc1590782c2)

- Created a Network ACL named PrivateSubnetACL for the prviate subnet.

![Private Subnet ACL inbound rules](https://github.com/user-attachments/assets/e4fa6dbc-fc8c-4df3-9d0f-61da9029cf87)

- Configured inbound rules to:
  - Allow MySQL/Aurora (port 3306) traffic only from the web server security group.
  - Explicitly denied all other traffic, ensuring the private subnet is restricted to the required database communication only.
________________________________________________________________________________________________________________________
### Verifying Access and Testing Connectivity
![ssh access to webserver and downloaded apache http webserver](https://github.com/user-attachments/assets/f88e77f7-5ea7-48fd-9f02-e0108e1e639e)

- Successfully established an SSH connection to the web server instance from my local machine.
- Installed Apache on the web server to serve web pages.

***For the sake of this project, I temporarily allowed all SSH traffic to ensure a smoother experience while setting up the web server.*** 
 
![can access webserver via the web](https://github.com/user-attachments/assets/0a8643a9-1ae9-4a91-84f5-defc41c12f63)

- Successfully accessed the web server via my web browser using the instance's public IPv4 address.
- Verified that Apache was running and serving the default web page.

![access to database server thru webserver ](https://github.com/user-attachments/assets/3e5da64f-c11e-41ed-9b62-c7755df9d289)

- Successfully established an SSH connection from the web server to the database server.
- Verified that the web server can securely communicate with the database server.
____________________________________________________________________________________________________________________________
## Lessons Learned
- **Network Segmentation Enhances Security** – Using public and private subnets helps isolate critical resources from direct internet exposure.

- **Internet Gateway and NAT Gateway Serve Different Purposes** – The IGW allows public instances to communicate with the internet, while the NAT Gateway enables private instances to initiate outbound traffic securely.
- **Security Groups and ACLs Work Together** – Security groups control traffic at the instance level, while network ACLs provide additional subnet-level filtering for better security enforcement.
- **Proper Route Table Configuration is Crucial** – Ensuring that subnets are correctly associated with the right route tables determines whether instances have access to external or internal resources.
- **Testing is Key** – Verifying SSH access, web connectivity, and database reachability ensures that the network is configured as expected.

