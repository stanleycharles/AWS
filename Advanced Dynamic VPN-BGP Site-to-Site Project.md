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

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Create%20Customer%20Gateway.png)

Do the same process with ``ONPREM-ROUTER2`` with the same ASN number ``65016`` .

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Customers%20Gateways%20Created.png)

Now, We've got two customer gateway objects created and these represent the routers within your simulated on-premises environment.

To recap at this stage, we have ``6`` EC2 instances, ``4`` of which are inside the simulated on-premises environment. Ans ``2`` of which are within AWS. 

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20EC2%20Enumeration.png)

At this stage, there is no connectivity between the AWS environment and the simulated on-premises environment.

Now we're going to create the AWS side of the VPN architecture. We're going to create 2 VPN attachements for the ``transit gateway`` which already exists within the AWS environment. We will be selecting the accelerated VPN option fot these VPN attachements. And this means that AWS will create ``Accelerated VPN Endpoints`` 2 per connections. These accelerated endpoints provide transit back to the transit gateway over the AWS global network. The Process also creates VPN connections to connect the accelerated VPN endpoints to the on premises routers using IPsec tunnels, and this is 2 per connections.
The tunnels won't be activen at this moment because that will require additional configuration.

Let's create the ``VPN attachements`` for the ``transit gateway``. 

One of these attachements will connect the AWS environment to the on-premises ``Router1`` and the other one will connect to the AWS environment to on-premises ``Router2``. And each one of those connections will includes a pair of IPsec tunnels, so we're going to have a total of ``4`` tunnels 2 each customer router.

Click and Create on ``Transit Gateway Attachments``. Select the existing ``transit gateway ID`` of the AWS VPC environment already created via Cloud Formation. Select ``VPN`` on attachment type, the customer we also just created ``ONPREM-ROUTER1``, and Make sure the dynamic option which requires BGP is selected. And because we're creating an accelerated VPN which uses the ``AWS Global Accelerator`` and the ``AWS Global Network``, we'll need to pick ``enable acceleration``. Then ``Create`` it.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Create%20Transit%20Gateway%20Attachment%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Create%20Transit%20Gateway%20Attachment%20p.2.png)

Do the same process with ``ONPREM-ROUTER2``.

To see the VPN creations, Click on ``Site-to-Site VPN Connections``.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Transit%20Gateway%20Attachment%20Created%20p.1.png)

If we're scroll to the right, we will note that one of the connections relates to ``ONPREM-ROUTER1`` and the another one relates to ``ONPREM-ROUTER2``. And that's as intended because each of these connections will connect from one specific customer gateway into a AWS

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Transit%20Gateway%20Attachment%20Created%20p.2.png)

Now at this point, what we need to do is to download the configuration for each of these connections because we need to extract some ``important configuration informations`` to set up the on-premises side of this architecture.

For that, click on ``Download Configuration`` and select these options.

**Do the same process with the 2nd ``Site-To-Site VPN connection``**

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Download%20Customer%20Gateway%20Config.png)

And Save et rename ``CONNECTION1ROUTER`` & ``CONNECTION2ROUTER``, these 2 txt files on your disk.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Save%20Config%20Files.png)

At this point, we will need to extract some important configuration items from the VPN configuration documents which just downloaded to configure the on-premises infratructure.

Here's an exemple. 
 - On the left, this is a random template just to extract the infomations necessary to configure these VPNs
 - On the right, this is the extract of ``CONNECTION1ROUTER`` or ``CONNECTION2ROUTER`` that we've just downloaded to get these important connections.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Extract%20Connection%20Infos.png)

Fill out all the informations necessary.

Then we're going to establish the IPsec tunnels by configuring the on-premises side of the architecture and get started ``activating`` the 4 IPsec tunnels that we're going to be using. So that's two tunnels from each customer router, one to each of the AWS VPN endpoints. So this is how we archieve the high availability. We've got two customer premises routers and 2 VPN connections, each using 2 VPN endpoints so this ensures that we've got high availability at both the AWS side and the on-premises side. 

In this part, There's going to be a lot of command line work that we need to do to configure the on-premises routers to be albe to establish IPsec tunnels between the on-premises side and the AWS side.

Now, there's going to be some configuration that we need to do on both of the ``on-premises routers`` in order to establish the IPsec tunnels between that router and AWS.

Let's start to configure the EC2 ``ONPREMS-ROUTER1`` and select ``Session Manager``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Sessions%20Manager%20Connect%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Sessions%20Manager%20Connect%20p.2.png)

Find and modify the ``ipsec.conf`` file in order the add the values that we've extracted before.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec%20Files.png)

Start to fill out the informations for the 2 IPsec tunnels and save the file.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec.conf%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec.conf%20p.2.png)

Then we're going to edit the second file ``ipsec.secrets``. This time, we're going to change a total of ``six`` placeholder values.
 - ``CONN1_TUNNEL1_ONPREM_OUTSIDE_IP``
 - ``CONN1_TUNNEL2_ONPREM_OUTSIDE_IP``
 - ``CONN1_TUNNEL1_AWS_OUTSIDE_IP``
 - ``CONN1_TUNNEL2_AWS_OUTSIDE_IP``
 - ``CONN1_TUNNEL1_PresharedKey``
 - ``CONN1_TUNNEL2_PresharedKey``
 
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec.secrets%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec.secrets%20p.2.png)

The next file we need to edit is the script ``ipsec-vti.sh`` that brings up the IPsec tunnels whenever required.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec-vti.sh%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Config%20Ipsec-vti.sh%20p.2.png)

Now, at this point we're going to copy all of these 3 IPsec configuration files ``ipsec.conf``, ``ipsec.secrets``, ``ipsec-vti.sh`` into the ``etc`` directory and make the script file executable with a ``chmod``. Then Restart the ``ONPREMS-ROUTER1`` EC2.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Restart%20EC2.png)

To verify these modifications, we can run an ``ifconfig`` and we should see 2 virtual tunnel interfaces, ``vti1`` & ``vti2``. That means these tunnels interfaces are ``active`` and connected to AWS.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20vti1-vti2%20Activated.png)

**Do exactly the same process with the ``ONPREMS-ROUTER2`` EC2**

Now, after complete the congiguration of these 2 EC2 ``ONPREMS-ROUTER1`` & ``ONPREMS-ROUTER2``, click on ``Site-To-Site VPN Connection``. We will see the 2 connections that we configured. Select the 1st one and click on ``Tunnel Details``. And we can see that the tunnels are down because we don't yet have ``BGP`` connectivity. But what we should see under details is that the ``IPsec is Up``. That indicates that the IPsec tunnels between the on-premises ``router 1`` and AWS are up and functionnal. It should be the same with the second connection.

**In the final stage**, we're going to configure the Border Gateway Protocol ``BGP``to run over the top of these 4 IPsec tunnels and in doing so, the VPN will be fully functional, it will be exchanging routes between the customer side and the AWS side and we will be able to test this by ``pinging``instances on the AWS side from on-premises and vice-versa. (see ``Routes Exchanged using BGP`` in the diagram).

Now, there are 2 mains tasks that we need to do:
 - The first task is that we will need to install the component which provides BGP functionally on both ``router1`` and ``router2``, and we will do that using a ``script`` that the Cloud Formation pre-installed on both of those Ubuntu Instances. And once, we've installed that service, we will need to configure it so that it communicates with AWS and establishes BGP sessions to exchange routing information and that needs to be performed on both of the on-premises routers.

In the AWS management console, we're going to utilize the IPsec tunnels and configure BGP to work across those tunnels and exchange routing information with AWS.
Now we need to do this process on both routers.

Let's start again to configure the EC2 ``ONPREMS-ROUTER1`` and select ``Session Manager``

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Sessions%20Manager%20Connect%20p.1.png)
![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/AWS%20VPNBGP%20-%20Sessions%20Manager%20Connect%20p.2.png)

Find and modify the script ``ffrouting-install.sh``which is known as ``FFR``. We need to make that file executable with ``chmod``. Then execute the script ``./ffrouting-install.sh``

(image)
(image)

**Do the same process with the ``ONPREMS-ROUTER2`` EC2**

At this point, both of our on-premises routers have finished installing FRR which provides BGP capability. At this stage, the AWS network knows nothing about our on-premises network and this network doesn't have any routing information for AWS. 

So we're going to change that, we're going to configure BGP to run across all of these IPsec tunnels.

Go back to the ``ONPREMS-ROUTER1`` and select ``Session Manager``.
In order to configure FR routing's BGP capability, we need to go into the shell that's used to configure that product. Follow these command line.

(image)

**Do the same process with the ``ONPREMS-ROUTER2`` EC2**

Now, we've configured ``BGP`` on both of the on-premises routers, so ``router1`` and ``router2``. And we've restarted them so they should now have established a BGP session with AWS.

To verify these BGP connections, click on ``Site-to-Site VPN Connection`` and click on ``Tunnel Details``. We should see both of the tunnels are in the Upstate and see a number of BGP routes that are being exchanged as part of this connection.

(image)

Type command line ``show ip route`` to see more details of the BGP connections.

(image)

The ``C`` symbol next to these means these are connected routes so things that are connected directly to this particular operating system. The line at the top with B means that this is a BGP learned route and we will be able to see the 10.16.0.0/16 has been learned via both ``vti1`` & ``vti2``. So we have high availability using this VPN because we can get to this AWS side network through either of the IPsec tunnels and the same configuration is true on the on-premises ``router2``, on the on-premises side now has 4 independent paths to get from the on-premises networks through to AWS and the same is true in reverse.

We have a highly available VPN architecture that can survive the failure of an availability zone at the AWS, or the failure of a customer premises router at the on-premises side.
















  ---
  
  ## Ressources
   - https://aws.amazon.com/fr/global-accelerator/
   - https://cantrill.io/
   
