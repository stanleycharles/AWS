# Advanced Dynamic VPN-BGP Site-to-Site Project

## How to design an Advanced Dynamic VPN-BGP connection between site-to-site infrastructure and the cloud

![This is an image](https://github.com/stanleycharles/AWS/blob/main/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Project/Advanced%20Dynamic%20VPN-BGP%20Site-to-Site%20Diagram.png)

### Scope 

In this diagram, we are going to create a hybrid architecture. You will create a Dynamic, Highly-Available, Accelerated, Site-to-Site VPN between an AWS location and simulated on-premises environment. We're going to create both sides of this architecture using ``Cloud Formation``. The AWS side on the left will be created with one cloud formation template. And the on-premises side on the right will use another.

This demo use AWS to simulate the on-premises environment, so we will be configuring 2 Linux-Based VPN servers which use strongSwan to provide the IPsec connectivity and a system known as FRR to do the BGP.



  ---
  
  ## Ressources
   - https://cantrill.io/
   
