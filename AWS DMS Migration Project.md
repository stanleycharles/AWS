# AWS DMS Migration Project

## How to transfer a web application or database securely on-premises into the AWS Cloud with Database Migration Services

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20Migration%20Diagram.png)

``AWS Database Migration Service`` helps you migrate databases to AWS quickly and securely. The source database remains **fully operational** during the migration, **minimizing downtime** to applications that rely on the database. The AWS Database Migration Service can migrate your data to and from most widely used commercial and open-source databases.

### Scope

We will be migrating a simple web application (wordpress) from an on-premises environment into AWS.

The on-premises environment is a virtual web server (simulated using EC2) and a self-managed mariaDB database server (also simulated via EC2)

We will be migrating this into AWS and running the architecture on an EC2 webserver and RDS managed SQL database.

In the diagram
 - On the left, this is the type of environment which we might have on-premises, and we're looking to migrate into AWS
 - On the right, we have the AWS environment and we're going to be provisioning an EC2 Web server (in green) and an RDS instance to store the database (in blue).
 - And We will be copying the content over using SSH (in orange) and using the Database Migration Service (DMS).
 - And then we will be copying the content over using SSH and using the Database Migration Service to perform a synchronization of the data from the source to the destination

### Provision the environment

After creating automatically The VPC & the On-Premises Infrastructures in the diagram via Cloudformation (yaml)
- A VPC with New/Empty RDS database. The aim is to transfer the on-premises datas. 
- 2 EC2: ``Web application`` is the simulated virtual machine web server , ``Database`` is the simulated vitual self-managed database.

In theory, the users (in the diagram) can access to the web application on-premises with their browser.

###  Establish Private Connectivity Between the environments (VPC Peer)

We're going to be provisioning private connectivity between the simulated on-premises environment on the left and the AWS environment on the right.
In production, we would be using a VPN or a Direct connect but to simulate that, we're going to be configuring a ``VPC Peering Connection`` between the On-Premises and the AWS environment (see the orange tunnel in the diagram). This will allow us to connect over this secure connection between these 2 VPCs.

On the AWS Management console, we can see these 2 VPCS, created via ``Cloud Formation``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20VPCs%20Emumeration.png)

Let's create a VPC Peering to connect these 2 VPCs. We should go on ``Peering Connections`` then click on ``Create Peering Connection`` and fill out the necessary informations. 
 - Name your peering connection
 - Select the local VPC: ``onpremVPC`` 192.168.10.0/24 (VPC requester)
 - Select the AWS VPC: ``awsVPC`` 10.16.0.0/24 (VPC accepter)

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Create%20Peering%20Connection%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Create%20Peering%20Connection%20p.2.png)

Your VPC Peer connection is created. Now we have to accepct the request. Make sure that we've got the peering connecttion selected and click on ``Action`` and then ``Accept Request``.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Accept%20VPC%20Peering%20Request.png)

Now, we've got the peering connection betweenthe two VPCs witch creates a secure channel and a gateway object in both VPCs.
The next step is we need to configure **Routing**.
We need to configure the VPC routers in each VPC to know how to send traffic to the other side of the VPC peer. 

To do this, we should click on ``Route Tables``. We can see 3 Routers that we have to edit. 
 - onpremsPublicRT
 - awsPrivateRT
 - awsPublicRT

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Route%20Tables%20Enumeration.png)

First, let's configure the ``onpremsPublicRT``. Logically this route table should be redirect towards the AWS environment.
Select onpremsPublicRT, click on the Route page and click on ``Edit routes``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20On-Prems%20Public%20Edit%20RT.png)

Now, we will need the IP CIDR of the AWS VPC environment: 10.16.0.0/16 to fill out the destination case. Then in the target case, click on ``Peering Connection`` and select the peering connection that we configure ealier between the 2 VPCs: ``onpremVPC`` & ``awsVPC``. To finish click on ``save routes``.
Once we've set that, this will mean that the On-premises environment knows how to route traffic through to the AWS environment.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Configure%20On-Prems%20Public%20RT%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Configure%20On-Prems%20Public%20RT%20p.2.png)

Next, we need to edit both of the AWS route tables. AWS has two routes tables: the Private and the Public. But in this demo, we're going to configure just the Public one because the procedure are the same. 
Select awsPublicRT, click on the Route page and click on ``Edit routes``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20AWS%20Public%20Edit%20RT.png)

Now, we will need the IP CIDR of the On-premises VPC environment: 192.168.10.0/24 to fill out the destination case. Then in the target case, click on ``Peering Connection`` and select the peering connection that we configure ealier between the 2 VPCs: ``onpremVPC`` & ``awsVPC``. To finish click on ``save routes``.

(By the way, don't forget to configure the awsPrivateRT).

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Configure%20AWS%20Public%20RT%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20DMS%20Migration%20Project/AWS%20DMS%20-%20Configure%20AWS%20Public%20RT%20p.2.png)

Now we have the secure connection between the simulated On-premises environment. On the AWS environment, we have gateway objects configured in both of the VPCs and then routes configured at both sides so they can communicate.

### Create & Configure the AWS Side infrastructure (App and DB)








  ---
  
  ## Ressources
   - https://aws.amazon.com/dms
   - https://cantrill.io
   
