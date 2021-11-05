# cautious-fortnight-docs
- VPC
  - cidr ranges IPV4 can be expanded upto 5 address ranges
  - VPC can use IPV6 in dual-stack mode. your resources can communicate over IPv4, or IPv6, or both. IPv4 and IPv6 communication are independent of each other. 
  - You cannot disable IPv4 support for your VPC and subnets; this is the default IP addressing system for Amazon VPC and Amazon EC2.  
- avaliablity Zones:
  - Groupings of fault domains
-  Route table
    -  A route table contains a set of rules, called routes, that are used to determine where network traffic from your subnet or gateway is directed.
    - **Main route table—** The route table that automatically comes with your VPC. It controls the routing for all subnets that are not explicitly associated with any other route table.
    - **Custom route table—** A route table that you create for your VPC.
    - **Destination—** The range of IP addresses where you want traffic to go (destination CIDR). For example, an external corporate network with the CIDR 172.16.0.0/12.
    - **Target—** The gateway, network interface, or connection through which to send the destination traffic; for example, an internet gateway.
    - **Route table association—** The association between a route table and a subnet, internet gateway, or virtual private gateway.
    - **Subnet route table—** A route table that's associated with a subnet.
    - **Local route—** A default route for communication within the VPC.
    - **Propagation—** Route propagation allows a virtual private gateway to automatically propagate routes to the route tables. This means that you don't need to manually enter VPN routes to your route tables. For more information about VPN routing options, see Site-to-Site VPN routing options in the Site-to-Site VPN User Guide.
    - **Gateway route table—** A route table that's associated with an internet gateway or virtual private gateway.
    - **Edge association—** A route table that you use to route inbound VPC traffic to an appliance. You associate a route table with the internet gateway or virtual private gateway, and specify the network interface of your appliance as the target for VPC traffic.
    - **Transit gateway route table—** A route table that's associated with a transit gateway. For more information, see Transit gateway route tables in Amazon VPC Transit Gateways.
    - **Local gateway route table—** A route table that's associated with an Outposts local gateway. For more information, see Local gateways in the AWS Outposts User Guide.
    - Example of route table on public subnet
      Destination           | Target               | Comments
      ------------          | -------------        | ----------------
      10.0.0.0/16           | local                | Route across all subnets in the VPC
      0.0.0.0/0             | internet-gateway-id  | Route to internet gateway
      S3.prefix.list        | gateway-endpoint-id  | if you are using gateway endpoint, gateway endpoint can route only to S3 and dynamo DB via public ip. This route can be on public/private subnet
      DynamoDB.prefix.list  | gateway-endpoint-id  | if you are using gateway endpoint, gateway endpoint can route only to S3 and dynamo DB via public ip. This route can be on public/private subnet
      on-premises           | VPN-gateway-id       | To connect to on prem via VPN gateway
      to-other-VPC          | other VPC-id         | if you are peering VPC's in a point-to-point fashion
      other-routes-via-TGW  | transit-gateway-id   | Transit gateway to connect upt 5000 VPCs in a region, other transit gateways in X region, VPN, AWS services via private link
    - Example of route tablle of private subnet
      Destination           | Target               | Comments
      ------------          | -------------        | ----------------
      10.0.0.0/16           | local                | Route across all subnets in the VPC
      0.0.0.0/0             | nat-gateway-id       | Route to NAT gateway on public subnet. This is required to for instances on private subnet to make outbound calls to internet
      S3.prefix.list        | gateway-endpoint-id  | if you are using gateway endpoint, gateway endpoint can route only to S3 and dynamo DB via public ip. This route can be on public/private subnet
      DynamoDB.prefix.list  | gateway-endpoint-id  | if you are using gateway endpoint, gateway endpoint can route only to S3 and dynamo DB via public ip. This route can be on public/private subnet
      on-premises           | VPN-gateway-id       | To connect to on prem via VPN gateway
      to-other-VPC          | other VPC-id         | if you are peering VPC's in a point-to-point fashion
      other-routes-via-TGW  | transit-gateway-id   | Transit gateway to connect upt 5000 VPCs in a region, other transit gateways in X region, VPN, AWS services via private link
- Subnets
  -  a segmented piece of a range of IP addresses in VPC
  -  To add a new subnet to your VPC, you must specify an IPv4 CIDR block for the subnet from the range of your VPC.
  -  You can optionally specify an IPv6 CIDR block for your subnet if an IPv6 CIDR block is associated with your VPC. 
  -  You can specify the Availability Zone in which you want the subnet to reside. You can have multiple subnets in the same Availability Zone.
  -  Public subnet
     - The instances in the public subnet can send outbound traffic directly to the internet.
  - Private subnet
    - The instances in the private subnet can't send outbound traffic directly to the internet. Instead, the instances in the private subnet can access the internet by using a network address translation (NAT) gateway that resides in the public subnet.
    - The instances can connect to the internet using the NAT gateway, but the internet cannot establish connections to the instances in private subnet.
    - The instances in the private subnet with IPV6 CIDR blocks can use an egress-only internet gateway to connect to the internet over IPv6, but the internet cannot establish connections to the private instances over IPv6.
  - Instances with private IPv4 addresses in all subnets (private and public) in a VPC can communicate with each other. 
- Internet Gateway
  - An internet gateway connects the VPC to the internet and to other AWS services.
- NAT Gateway
  - a NAT gateway is used so that instances in a private subnet can connect to services outside your VPC but external services cannot initiate a connection with those instances.
  - You create a public NAT gateway in a public subnet and must associate an elastic IP address with the NAT gateway at creation. You cannot associate an elastic IP address with a private NAT gateway.
  - You route traffic from the NAT gateway to the internet gateway for the VPC.
  - Alternatively, you can use a (public or private) NAT gateway to connect to other VPCs or your on-premises network. In this case, you route traffic from the NAT gateway through a transit gateway or a virtual private gateway.
  - You can attach an internet gateway to a VPC with a private NAT gateway, **but if you route traffic from the private NAT gateway to the internet gateway, the internet gateway drops the traffic**. 
  - The NAT gateway replaces the source IP address of the instances with the IP address of the NAT gateway. For a public NAT gateway, this is the elastic IP address of the NAT gateway. For a private NAT gateway, this is the private IP address of the NAT gateway. When sending response traffic to the instances, the NAT device translates the addresses back to the original source IPv4 addresses. 
  - You are charged for each hour that your NAT gateway is available and each Gigabyte of data that it processes.
  - If your AWS resources send or receive a significant volume of traffic across Availability Zones, ensure that the resources are in the same Availability Zone as the NAT gateway, or create a NAT gateway in the same Availability Zone as the resources. 
  - If most traffic through your NAT gateway is to AWS services that support interface endpoints or gateway endpoints, consider creating an interface endpoint or gateway endpoint for these services. 
  - A NAT gateway supports 5 Gbps of bandwidth and automatically scales up to 45 Gbps. If you require more bandwidth, you can split your resources into multiple subnets and create a NAT gateway in each subnet. 
  - A NAT gateway can process one million packets per second and automatically scales up to four million packets per second. Beyond this limit, a NAT gateway will drop packets. To prevent packet loss, split your resources into multiple subnets and create a separate NAT gateway for each subnet. 
  - A NAT gateway can support up to 55,000 simultaneous connections to each unique destination. This limit also applies if you create approximately 900 connections per second to a single destination (about 55,000 connections per minute). For more than 55,000 connections, there is an increased chance of connection errors due to port allocation errors.
  - You can associate exactly one Elastic IP address to public NAT gateway and one available private IP address from the subnet in which it is configured to private NAT gateway.
  - You cannot disassociate an Elastic IP address from a public NAT gateway and private IP address for private NAT gateway.
  - NAT gateways are not supported for IPv6 traffic—use an outbound-only (egress-only) internet gateway instead.
- Access the internet from a private subnet
  - You can use a public NAT gateway in public subnet to enable instances in a private subnet to send outbound traffic to the internet, but the internet cannot establish connections to the instances.
  - The following is the route table associated with the public subnet. The first entry is the default entry for local routing in the VPC; it enables the instances in the VPC to communicate with each other. The second entry sends all other subnet traffic to the internet gateway; this enables the NAT gateway to access the internet.
      Destination  | Target
      ------------ | -------------
      10.0.0.0/16  | local
      0.0.0.0/0    | internet-gateway-id
  - The following is the route table associated with the private subnet. The first entry is the default entry for local routing in the VPC; it enables the instances in the VPC to communicate with each other. The second entry sends all other subnet traffic, such as internet bound traffic, to the NAT gateway. 
      Destination  | Target
      ------------ | -------------
      10.0.0.0/16  | local
      0.0.0.0/0    | nat-gateway-id

- VPN Gateway
  - To establish secure connections between your on-premises networks and the AWS global network
- Transit Gateway
  - AWS Transit Gateway connects VPCs,This simplifies your network and puts an end to complex peering relationships. 
  - It can also extend to connect VPCs and on-premises networks through a central hub. 
  - It acts as a cloud router – each new connection is only made once.
  - AWS Transit Gateway inter-Region peering encrypts all traffic, with no single point of failure or bandwidth bottleneck.
  - Data is automatically encrypted, and never travels over the public internet.
    ![plot](img/tgw-after.d85d3e2cb67fd2ed1a3be645d443e9f5910409fd.png)
