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

(image)

Then, Let's create a 












  ---
  
  ## Ressources
   - https://aws.amazon.com/dms
   - https://cantrill.io
   
