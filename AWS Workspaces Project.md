# AWS Workspaces Project

## How to create a cloud Active Directory workspace for an entreprise on AWS

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20Diagram.png)

`Amazon Workspaces` is a Desktop As A Service (DAAS) - Home Working / Office from anywhere. 
It delivers managed `Linux` and `Windows` desktops of various sizes and capabilities and integrates fully with AWS products and Services. 

We can also use the ``AD Connector`` mode of ``Directory Service`` if you want to integrate with your existing on premises ``Active Directory`` without having any directory infrastructure directly running within AWS. 
Or we can use the ``Managed Microsoft Active Directory`` mode of ``Directory Service`` if you need to utilize a **native** ``Microsoft Active Directory`` and want to integrate it with Workspaces.

Each Workspace uses a ``network interface`` ``(ENI)`` which is injected into a VPC. And for almost all the networking that a workspace does, it uses this VPC and any associated network infrastructure.

Workspaces are accessed using ``client software`` installed on a normal desktop or laptop.
Workspaces can access on-premises networks over a ``VPN`` or a ``Direct Connect``.

### Scope

After creating automatically The VPC Infrastructure in the diagram via ``Cloudformation`` (yaml)
 - A VPC
 - 2 Public subnets ``(WEB)`` with 2 NAT Gateway interconnected with a Internet Gateway 
 - 2 Private subnets ``(APP)`` with 2 ENI (Elastic Network interfaces) interconnected with Amazon Workspaces & Directory Service.

Let's create First, a Simple ``Active Directory`` via ``Directory Service``.

Select **Simple AD**

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Directory%20Service%20-%20Select%20The%20AD.png)

Then Select the ``Small`` AD size (because cheaper)
Fill out The ``Organization name``, The ``Directory DNS name``, ``NetBIOS name`` and ``Password``.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Directory%20Service%20-%20Fill%20out%20pt.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Directory%20Service%20-%20Fill%20out%20pt.2.png)

In the next step, we should select the VPC just created and 2 subnets ``2 APP subnets - A,B`` to deploy the ``Directory Service``. (see the Diagram).
By setting those, we select which subnet will have a network interface into them for the Directory Service.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Directory%20Service%20-%20Select%20VPC-SN.png)

Now, the ``Active Directory`` is created, Let's create a desktop or laptop environnment via ``Amazon Workspaces``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Directory%20Service%20-%20AD%20Created.png)

Now that the Directory Service is in the active state, we can proceed and actually create ``a Workspace``. After clicking on Amazon Workspaces and on Launch Workspaces, we will need to select the Directory just created. 
Next, we will need to provide ``2 different subnets`` within the VPC that will be used to launch Workspaces. Pick those that are availiable.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Create%20Workspaces.png)

Then, we will need to create our ``1st Workspace user`` with a Username, First name, Last name and email. This ``email address`` will be used to send ``Setup Instructions`` for this workspace.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Add%20AD%20User.png)

Scroll down and we should see that we've now got ``a user created`` in that directory.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Add%20AD%20Verif.png)

Next. we have to select the ``bundle`` that your workspace will use. Depends on what you need. So we will take a ``Performance Windows 10 version Free tier eligible``.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Select%20Bundles.png)

Now the Workspace is finally created (take some time, 20-30min)

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Workspace%20Created.png)

We will recieve an email ``to complete`` the setup instruction with an web browser ``URL``. Click on that URL and ``fill out`` the infos just created + create a new password.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Web%20Setup%20Portail.png)

We will be taken to the ``Amazon Workspaces client download page`` and we will need ``to download`` the client for the operating system that you're using.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Install%20client.png)

Now ``install`` the program on your desktop or laptop.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Client%20installation.png)

Then, we will be placed in a ``login prompt``. Enter the username and the password that we created before.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Web%20Workspaces%20Portail.png)

After a few moments, we will be logged into your workspace. This is a ``Windows desktop`` that we selected ealier.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20Windows.png)

``To finish``, we are going ``to test`` the Internet connexion that we configured. The ``APP subnets`` are interconnected to the ``WEB subnets`` (``see diagrams``). These ``WEB subnets`` have ``NAT Gateways`` interconnected with a ``Internet Gateway`` who have access to the web.
Open a browser and see if the internet connexion ``works``.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Workspaces%20Project/AWS%20Workspaces%20-%20internet.png)

  ---
  
  ## Ressources
   - https://docs.aws.amazon.com/workspaces/latest/adminguide/amazon-workspaces.html
   - https://aws.amazon.com/fr/directoryservice/
   - https://cantrill.io/
   

