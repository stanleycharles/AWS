# AWS Workspaces Project

## How to create a cloud Active Directory workspace for an entreprise on AWS

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20Diagram.png)

`Amazon Workspaces` is a Desktop As A Service (DAAS) - Home Working / Office
It delivers managed `Linux` and `Windows` Desktops of various sizes and capabilities and integrates fully with AWS products and Services. It delivers consistent desktop from anywhere (apps & state).

We can also use the ``AD Connector`` mode of ``Directory Service`` if you want to integrate with your existing on premises ``Active Directory`` without having any directory infrastructure directly running within AWS. Or we can use the ``Managed Microsoft Active Directory`` mode of ``Directory Service`` if you need to utilize a **native** ``Microsoft Active Directory`` and we want to integrate it with Workspaces.

Each Workspace uses a **network interface** ``(ENI)`` with is injected into a VPC. And so for almost all the networking that a workspace does it uses this VPC and any associated network infrastructure.

Workspaces are accessed using client software installed on a normal desktop or laptop.

Workspaces can access on-premises networks over a ``VPN`` or a ``Direct Connect``.

### Scope

After creating automatically The VPC Infrastructure in the diagram via ``Cloudformation`` (yaml)
 - A VPC
 - 2 Public subnets ``(WEB)`` with 2 NAT Gateway interconnected with a Internet Gateway 
 - 2 Private subnets ``(APP)`` with 2 ENI (Elastic Network interfaces) interconnected with Amazon Workspaces & Directory Service.

Let's create First, a Simple ``Active Directory`` via ``Directory Service``.

Select **Simple AD**

(image)

Then Select the **Small** AD size (because cheaper)
Fill out The Organization name, The Directory DNS name, NetBIOS name and Password

(image)
(Image)

In the next step, we should select the VPC just created and 2 subnets (2 APP subnets - A,B) to deploy the Directory Service. (see the Diagram).
By setting those we're selecting which subnet will have a network interface into them for the Directory Service.

(Image)

Now, the Active Directory is created, Let's create a desktop or laptop environnment via Amazon Worksapces

(Image)


Now, Select










  ---
  
  ## Ressources
   - https://docs.aws.amazon.com/workspaces/latest/adminguide/amazon-workspaces.html
   

