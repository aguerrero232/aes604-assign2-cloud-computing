
# **Programatically creating an infrastructure**

## **Resources**

* Most important part of the .yml file
* Order is not important
  * order in a way you think is most logical

![example resources section in cloudformation .yaml](img/rec_example.jpg)
___

## **VPC**

lay out a vpc to start out our network
(this will create another vpc along side aws's default vpc)

![](img/vpc_example.jpg)

### **CIDR**

* For a vpc you need a cidr
  * the cidr tells you the allocation of the ip , basically the size of your infrastructure
    * laymens: list of ips available on your infrastructure

![](img/cidr_example.jpg)

___

## **InternetGateway**

Since we are replacing the default vpc, for the newly created vpc to have severs that can access the internet and vise versa it is necessary to create an internet gateway.

***Without an internet gateway access to the internet is not possible.***

It is a gateway to connect your VPC to the outside.

![](img/internet_gateway_example.jpg)

### **InternetGatewayAttachment**

Attaches the InternetGateway to the VPC.

* Parameters:
  * InternetGateway
  * VPC

![](img/ig_attachment_example.jpg)
___

## **Subnets**

***A subnet means a switch, so every switch you should ask how many ip addresses can it hold. How many maximum servers can connect to this switch?***

***make sure to select different zones if using multiple subnets***

### **Public Subnets**

![](img/public_subnets_examples.jpg)

### Value for public Subnets

![](img/params_pub_subnet_example.jpg)

The number of ip available for public subnet 1 is:

    10.0.0.0/24 = 2<sup>8</sup> = 256 max servers on the subnet

The number of ip available for public subnet 2 is:

    10.0.1.0/24 = 2<sup>8</sup> = 256 max servers on the subnet

nothing changes since we are just using a mask on the cidr

### **Private Subnets**

![](img/private_subnets_esamples.jpg)

***MapPublicIpOnLaunch* set to false because it is best practice for private subnets!**

#### Value for private Subnets

![](img/params_priv_subnet_example.jpg)

___

## **NAT**

**First create a NAT and then an association.**

### ***NAT With Elastic IP***

![](img/nat_gateway_eip_example.jpg)

Defined in type to have an ***EIP (elastic ip)*** for the domain of the **VPC** using the **InternetGatewayAttachment**.

Associate the **EIP** with the **NAT** and then create an association to the **Subnet**

![](img/nat_gateway_example.jpg)

Get **AllocationId** attribute from **NatGateway1EIP** then associate with **PublicSubnet1**.

___

## ***RoutingTable***

Creating A routing table resource for private and public use.

### ***Public Routing***

![](img/routing_table_init_example.jpg)

    Properties state that it belongs to the provided VPC and adding a tag to it

### ***Default***

* ***Most important part for the routing.***
* ***Defines the rules***

![](img/default_routes_example.jpg)

Anyone can connect to you and you can connect to anyone using **InternetGateway**.

    0.0.0.0/0 means anyone, but they go through the InternetGateway

### ***Private Routing***

![](img/private_rt_and_default.jpg)

**PrivateDefaultRoute** states that to go out of network you have to use **NatGateway1**

## ***Security Groups***

![](img/sec-group.jpg)

* AWS security group **acts as a virtual firewall** for your EC2 instances to control incoming and outgoing traffic.
* Both inbound and outbound rules control the flow of traffic to and traffic from your instance, respectively.

## ***Public Server Instances***

![](img/public_instances.jpg)

EC2 instances added to public subnets used to connect to the internet, and jump into private instances (since they are located in the same VPC).

## ***Private Server Instances***

![](img/private_instances.jpg)

EC2 Instances added to private subnets, **only accessible from within the network**.

## ***Web Server Group***

![web server group](img/web_server_group.jpg)

Security group that allows http to the hosts and ***ssh from local only***.

___

# ***Accessing the Private Servers***

![](img/access_by_jump_box.png)

To access the Private Servers you need to first be on the same VPC.
So you need to have what is called a jump box, basically a public server on the same VPC.

Simply ssh into the public server and then ssh into the private server to gain access.

___

# ***Finished Infrastructure***

![](img/final_infrastructure.png)
