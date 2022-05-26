# VPC Project on AWS

## How to create a VPC cloud infrastructure for an entreprise on AWS

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20VPC%20Project/AWS%20VPC%20Project%20Diagram.png)

### Reminder

Steps through the design choices around VPC design and IP planning.

When creating a VPC, we'll need first, to decide on is the IP range that the VPC will use: The ``VPC Cider``. 
Deciding on an IP plan and VPC structure in advance is one of the most critically important things you will do as the solutions Architect because it's not easy to change later and it will cause you a world of pain if you don't get it right.

Now when your start this design process, there are a few things that you need to keep in mind:

 - What size should the VPC be
 - Are there any Networks we can't use...
 - VPC's Cloud, On-premises. Partners & Vendors
 - VPC Structure - Tiers & Resiliency (Availability) Zones
 
 IP Ranges to Avoid when creating a Business VPC:
 
  - 192.168.10.0/24 (192.168.10.0 -> 192.168.10.255)
  - 10.0.0.0/16 ``(AWS)`` (10.0.0.0 -> 10.0.255.255)
  - 10.128.0.0/9 ``(Google)`` (10.128.0.0 -> 10.255.255.255)
  - 172.31.0.0/16 ``(Azure)`` (172.31.0.0 -> 172.31.255.255)
  - 192.168.15.0/24 ``(London)`` (192.168.15.0 -> 192.168.15.255)
  - 192.168.20.0/24 ``(NYC)`` (192.168.20.0 ->192.168.20.255
  - 192.168.25.0/24 ``(Seattle)`` (192.168.25.0 -> 192.168.25.255)

More Considerations
 - VPC minimum /28 (16 IPs), maximum /16 (65456 IPs)
 - Personal preference for the 10.x.y.z range
 - Avoid commom ranges to avoid future issues
 - 

### Scope

Step through how to implement the multi-tier subnet design for an entreprise including IPv6 configuration for subnets

We're going to be implementing the diagram structure which actually 12 subnets on 3 availability zones. This subnet calculator site will give us all the ip address structure that we will need to configure the subnets on the VPC.

(image)

 - So the 1st subnet will be 10.16.0.0 and the last 10.16.176.0
 - Implemented in 3 availability zones A, B, C
 - Spared in 4 subnets sections: Reserved, database (db), application (app), web
 - With IPv6 Activated

```
NAME CIDR AZ CustomIPv6Value

sn-reserved-A 10.16.0.0/20 AZA IPv6 00
sn-db-A 10.16.16.0/20 AZA IPv6 01
sn-app-A 10.16.32.0/20 AZA IPv6 02
sn-web-A 10.16.48.0/20 AZA IPv6 03

sn-reserved-B 10.16.64.0/20 AZB IPv6 04
sn-db-B 10.16.80.0/20 AZB IPv6 05
sn-app-B 10.16.96.0/20 AZB IPv6 06
sn-web-B 10.16.112.0/20 AZB IPv6 07

sn-reserved-C 10.16.128.0/20 AZC IPv6 08
sn-db-C 10.16.144.0/20 AZC IPv6 09
sn-app-C 10.16.160.0/20 AZC IPv6 0A
sn-web-C 10.16.176.0/20 AZC IPv6 0B

Remember to enable auto assign ipv6 on every subnet you create.
```






  ---
  
  ## Ressources
   - https://aws.amazon.com/vpc/
   - https://www.site24x7.com/tools/ipv4-subnetcalculator.html
