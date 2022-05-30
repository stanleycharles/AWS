# Advanced Dynamic VPN-BGP Site-to-Site Project

## How to design an Advanced Dynamic VPN-BGP connection between site-to-site infrastructure and the cloud

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Diagram.png)

### Scope 

In this diagram, we are going to create a hybrid architecture. You will create a Dynamic, Highly-Available, Accelerated, Site-to-Site VPN between an AWS location and simulated on-premises environment. We're going to create both sides of this architecture using ``Cloud Formation``. The AWS side on the left will be created with one cloud formation template. And the on-premises side on the right will use another.

This demo use AWS to simulate the on-premises environment, so we will be configuring 2 Linux-Based VPN servers which use strongSwan to provide the IPsec connectivity and a system known as FRR to do the BGP.

After The On-premises & AWS environment created via ``Cloud Formation``, we're going to create 2 ``customer gateway`` objects on the VPC section: ``Router1`` & ``Router2``.

For that we need some important informations, specifically ``the private and public IP addresses`` of both of the customer routers that are running on-premises.

 - Router1Private: ``192.168.12.38``
 - Router1Public: ``3.86.70.61``
 - Router2Private: ``192.168.12.214``
 - Router2Public: ``3.87.35.101``

First, We are going to create ``ONPREM-ROUTER1``. This representes Router1 running within the simulated on-premises envrionment. And we need the IP address of this router, the public IPv4 address ``3.86.70.61``. We are the intent to create a dynamic VPN so we need to select the dynamic option. we will need to enter a ``BGP ASN``. This type of VPN connection needs a ``unique ASN`` at the AWS side and the customer side.
Most ASNs are publicly allocated but there are a number of private ASNs that we're able to use. So these start from ``64512`` and end at ``65534``. Now, these ASNs that we're entering represent the ASN that's used on the simulated on-premises environment. AWS will user their own, but we have the option of picking our own ASN.

We're going to use ``65016``. We could use **any number** in the range. End then ``create the gateway``.

(image)

Do the same process with ``ONPREM-ROUTER2`` with the same ASN number ``65016`` .

Now, We've got two customer gateway objects created and these represent the routers within your simulated on-premises environment.

To recap at this stage, we have ``6`` EC2 instances, ``4`` of which are inside the simulated on-premises environment. Ans ``2`` of which are within AWS. 

(image)

At this stage, there is no connectivity between the AWS environment and the simulated on-premises environment.

Now we're going to create the AWS side of the VPN architecture. We're going to create 2 VPN attachements for the ``transit gateway`` which already exists within the AWS environment. We will be selecting the accelerated VPN option fot these VPN attachements. And this means that AWS will create ``Accelerated VPN Endpoints`` 2 per connections. These accelerated endpoints provide transit back to the transit gateway over the AWS global network. The Process also creates VPN connections to connect the accelerated VPN endpoints to the on premises routers using IPsec tunnels, and this is 2 per connections.
The tunnels won't be activen at this moment because that will require additional configuration.

Let's create the ``VPN attachements`` for the ``transit gateway``. 

One of these attachements will connect the AWS environment to the on-premises ``Router1`` and the other one will connect to the AWS environment to on-premises ``Router2``. And each one of those connections will includes a pair of IPsec tunnels, so we're going to have a total of ``4`` tunnels 2 each customer router.

Click and Create on ``Transit Gateway Attachments``. Select the existing ``transit gateway ID`` of the AWS VPC environment already created via Cloud Formation. Select ``VPN`` on attachment type, the customer we also just created ``ONPREM-ROUTER1``, and Make sure the dynamic option which requires BGP is selected. And because we're creating an accelerated VPN which uses the ``AWS Global Accelerator`` and the ``AWS Global Network``, we'll need to pick ``enable acceleration``. Then ``Create`` it.

(image)
(image)

Do the same process with ``ONPREM-ROUTER2``.

To see the VPN creations, Click on ``Site-to-Site VPN Connections``.

(image)

If we're scroll to the right, we will note that one of the connections relates to ``ONPREM-ROUTER1`` and the another one relates to ``ONPREM-ROUTER2``. And that's as intended because each of these connections will connect from one specific customer gateway into a AWS

(image)

Now at this point, what we need to do is to download the configuration for each of these connections because we need to extract some ``important configuration informations`` to set up the on-premises side of this architecture.

For that, click on ``Download Configuration`` and select these options.

Do the same process with the 2nd ``Site-To-Site VPN connection``

(image)

And Save et rename ``CONNECTION1ROUTER`` & ``CONNECTION2ROUTER``, these 2 txt files on your disk.

(image)

At this point, we will need to extract some important configuration items from the VPN configuration documents which just downloaded to configure the on-premises infratructure.

Here's an exemple. 
 - On the left, this is a random template just to extract the infomations necessary to configure these VPNs
 - On the right, this is the extract of ``CONNECTION1ROUTER`` or ``CONNECTION2ROUTER`` that we've just downloaded to get these important connections.

(image)

Fill out all the informations necessary.

Then we're going to establish the IPsec tunnels by configuring the on-premises side of the architecture and get started ``activating`` the 4 IPsec tunnels that we're going to be using. So that's two tunnels from each customer router, one to each of the AWS VPN endpoints. So this is how we archieve the high availability. We've got two customer premises routers and 2 VPN connections, each using 2 VPN endpoints so this ensures that we've got high availability at both the AWS side and the on-premises side. 

In this part, There's going to be a lot of command line work that we need to do to configure the on-premises routers to be albe to establish IPsec tunnels between the on-premises side and the AWS side.

Now, there's going to be some configuration that we need to do on both of the ``on-premises routers`` in order to establish the IPsec tunnels between that router and AWS.

Let's start to configure the EC2 ``ONPREMS-ROUTER1`` and select ``Session Manager``

(image)
(image)

Find and modify the ``ipsec.conf`` file in order the add the values that we've extracted before.

(image)

Start to fill out the informations for the 2 IPsec tunnels and save the file.

(image)
(image)

Then we're going to edit the second file ``ipsec.secrets``. This time, we're going to change a total of ``six`` placeholder values.
 - ``CONN1_TUNNEL1_ONPREM_OUTSIDE_IP``
 - ``CONN1_TUNNEL2_ONPREM_OUTSIDE_IP``
 - ``CONN1_TUNNEL1_AWS_OUTSIDE_IP``
 - ``CONN1_TUNNEL2_AWS_OUTSIDE_IP``
 - ``CONN1_TUNNEL1_PresharedKey``
 - ``CONN1_TUNNEL2_PresharedKey``
 
(image)
(image)




































  ---
  
  ## Ressources
   - https://cantrill.io/
   
