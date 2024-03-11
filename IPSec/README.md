# Setting up a basic IPv6 testbed to demonstrate IPsec
## Introduction
Before we begin with the demonstration, let us first look at the topology (diagramatic representation) of the network we are going to be working with:

![Network Topology Image](/IPSec/images/topology.png)

In this topology, there are 2 Clients *comp1* and *comp2* who wish to communicate with each other using IPsec. The two clients are seperated from each other by 2 gateways (i.e., each client is connected to a gateway and the gateways are connected to each other)

Today, our goal is to enable and explore communication between the 2 clients using IPsec in the transport mode and tunnel mode. 


In the Oracle VM VirtualBox Manager, you should be able to see 4 Virtual Machines already set up in your systems
   - comp1
   - gateway-1
   - gateway-2
   - comp2





## Check Status of radvd on Gateway1 and Gateway2
- Start Gateway1 and Gateway2 Virtual Machines.    
- Run this command to make sure radvd is active(running) and enabled:  

On Gateway1:    

     systemctl status radvd  
     systemctl stop firewalld  
     systemctl disable firewalld  

 
On Gateway2:  
   
    systemctl status radvd  
    systemctl stop firewalld 
    systemctl disable firewalld


- Now, Minimise Gateway1 and Gateway2.Do not turn them off. Then,  

- Start Host1 and Host2 Virtual Machines.  

On Host1:
   
    ping <ipv6_addr_of_host2>    
    systemctl stop firewalld  
    systemctl disable firewalld  

On Host2:   

    ping <ipv6_addr_of_host1>  
    systemctl stop firewalld  
    systemctl disable firewalld  

## Install strongswan and Configure ipsec connection between Host1 and Host2

- Installing strongswan

      dnf install strongswan  

- Open and Update config file in strongswan library  

    nano /etc/strongswan/ipsec.conf  

  Include the following lines in the config file:  

      conn host-host  
         authby=secret  
         auto=route  
         keyexchange=ikev2  
         ike=aes256-sha2_256-modp1024!  
         left=< youripaddr>  
         right=<receiveripaddr>  
         type=transport  
         esp=aes256-sha2_256!  
  
  - Open and Update config file in strongswan library  

        nano /etc/strongswan/ipsec.secrets  

    Include the following line in secrets file:  

        <receiveripaddr> : PSK "presharedkey"  

  - Restart strongswan and setup a connection  
  
        sudo strongswan restart  
        sudo strongswan up host-host  
             
## Host WebServer on Host1 and access it from Host2
  On Host1:   

dnf install httpd -y  
systemctl start httpd  
systemctl enable httpd  
systemctl status httpd  

- After typing the next command, the configuration file will open.  

          nano /etc/httpd/conf/httpd.conf  

Here, go to the 'Listen 80' and change it to 'Listen [<youripaddr>]:80'   
Also, go to a section having <Directory></Directory> and inside it change the 'Require all denied' to 'Require all granted'.  
Save the file and exit.(i.e. 'cntrl+X' then 'Y' then 'Enter')  
          systemctl restart httpd  
Then, go to browser and type:  

          http://[host1 ip addr>]  

  On Host2: Go to browser and type:  
          
          http://[host1 ip addr>]  

Capture and analyse the packets using wireshark.   
 
  On Host2: 
  
      sudo wireshark  
  
  On Host1: 
  
      sudo wireshark  

Now, refresh the browser on Host2 and you should be able to see HTTP and TCP packets and being travelled from Host1 to Host2.  

Right click on any TCP packet and select "Follow">"TCP Stream". Here, you can see the text of the page is completely visible.   


  ## Capturing traffic and analysing the packets using wireshark
   sudo wireshark  

  











