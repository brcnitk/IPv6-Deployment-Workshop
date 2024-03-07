# Setting up a basic IPv6 testbed to demonstrate IPsec
## Introduction
Before we begin with the demonstration, let us first look at the topology (diagramatic representation) of the network we are going to be working with:

![Network Topology Image](/IPSec/images/topology.png)

In this topology, there are 2 Clients *comp1* and *comp2* who wish to communicate with each other using IPsec. The two clients are seperated from each other by 2 gateways (i.e., each client is connected to a gateway and the gateways are connected to each other)

Today, our goal is to enable and explore communication between the 2 clients using IPsec in the transport mode and tunnel mode. 

### Getting to know your Virtual Machines (VMs)
1. We will be using 4 VMs (2 to act as Clients and 2 to act as Gateways)
2. To run and manage the Virtual Machines, we will be using a tool called Oracle Virtual Box.
3. To run Oracle Virtualbox, Select the "Show Applications" icon present at the bottom left of your screen, then scroll through the applications and select "Oracle VM Virtualbox"

![Application Menu Image](/IPSec/images/Application-Menu.png)

![Virtualbox Image](/IPSec/images/Virtualbox.png)

5. In the Oracle VM VirtualBox Manager, you should be able to see 4 Virtual Machines already set up in your systems
   - comp1
   - gateway-1
   - gateway-2
   - comp2

![Virtualbox Manager Image](/IPSec/images/vmMenu.png)

### Setting up the basic network connections in your VMs
Any networking device will have an interface using which it connects to other devices. 
- An interface is a boundary or a surface where two different systems or components meet and interact.
- In networking, an interface represents the connection point between a device and a network medium (such as an Ethernet cable or a wireless channel).
So, the Ethernet port to which you connect your LAN cable is also an interface. These interfaces that we can physically see are called physical interfaces.

To simulate the topology shown above, we will today create our own logical interfaces and then make connections in the Oracle VM Virtualbox Manager.

Since there are 3 different networks in our topology 
   - gateway1 -> gateway2 is a network with addresses starting with prefix **fec0::**
   - gateway1 -> comp1 is another network with addresses starting with prefix **fec1::**
     
     > Note that there might be other hosts (comp2, comp3, etc.) present in this subnetwork with IP addresses **fec1::**2, **fec1::**3, etc. However, for the sake of simplicity, we will only consider 1 host (comp1) in our architecture.
     
   - gateway2 -> comp2 is another network with addresses starting with prefix **fec2::**

     > Note that there might be other hosts (comp2, comp3, etc.) present in this subnetwork with IP addresses **fec2::**2, **fec2::**3, etc. However, for the sake of simplicity, we will only consider 1 host (comp2) in our architecture.

Now, let us make these networks in the VM Manager

   ![Virtualbox Manager Image](/IPSec/images/network2.png)

   1. Select "Tools" on the left side panel
   2. Select the "Network" option
   
   ![Virtualbox Manager Image](/IPSec/images/network3.png)

   3. Select "Create" thrice to create 3 different networks. (vboxnet0, vboxnet1, vboxnet2)

1. In the topology, diagram we see that each device (Clients have 1 interface - enp0s3 and Gateways have 2 interfaces each - *enp0s3* and *enp0s8*"
   - *enp0s3* interface of comp1 is connected to *enp0s3* interface of gateway-1. To do this, follow these steps:

      ![Virtualbox Manager Image](/IPSec/images/network1.png)
     
      1. Select *comp1* on the left pane of the Virtualbox VM Manager.
      2. On the top pane, select Settings as shown in the figure.
      3.  
       
      - 
   - *enp0s8* interface of gateway-1 is connected to *enp0s8* interface of gateway-2. To do this, follow these steps:
  
   - *enp0s3* interface of gateway-2 is connected to *enp0s3* interface of comp2. To do this, follow these steps:

