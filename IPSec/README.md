# IPsec Demonstration and Packet Analysis at Network Layer
## Introduction
Before we begin with the demonstration, let us first look at the topology (diagramatic representation) of the network we are going to be working with:

![Network Topology Image](/IPSec/images/Topologyv2.png)

In this topology, there are 2 Clients *Host1* and *Host2* who wish to communicate with each other using IPsec. The two clients are seperated from each other by 2 gateways (i.e., each client is connected to a gateway and the gateways are connected to each other)

Our goals for today are:
- Setting up the network topology shown above with the help of **radvd** tool. 
- Establishing IPsec in two modes using **Strongswan** library.    
    (i)Transport Mode : IPsec protocol is configured at Host1 and Host2.    
    (ii)Tunnel Mode : IPsec protocol is configured at Gateway1 and Gateway2.    
- Hosting a website at Host1 without any security mechanisms at layers above Network layer and accessing it from Host2. This will generate packets which we will analyse in the next section.
- Analysing packets using **Wireshark** tool. We are going to analyse packets in the following cases:    
     case(i): Without enabling IPsec connection between Host1 and Host2. This will show us the website content in plain text.
     case(ii): By enabling IPsec in Transport Mode. This will show the website content in cipher text(encrypted) at Host1,Gateway1,Gateway2 and Host2.      
     case(iii): By enabling IPsec in Tunnel Mode. This will show the website content in cipher text(encrypted) at Gateway1 and Gateway2. Whereas, in plain text at Host1 and Host2.
- Prerequisites:      
  (i) Oracle VirtualBox: Download and install Oracle VirtualBox from [Oracle's official website](https://www.virtualbox.org/).      
(ii) Fedora OS: Download the Fedora ISO image from [Fedora's official website](https://fedoraproject.org/workstation/download).
  
  
  
## Setting up the network topology
-  

###    
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

- Here, go to the 'Listen 80' and change it to 'Listen [<youripaddr>]:80'

  
- Also, go to a section having <Directory></Directory> and inside it change the 'Require all denied' to 'Require all granted'.  
- Save the file and exit.(i.e. 'cntrl+X' then 'Y' then 'Enter')  

          systemctl restart httpd  

- Then, go to browser and type:  

          http://[host1 ip addr>]  

On Host2:   
- Go to browser and type:  
          
          http://[host1 ip addr>]  



  ## Capturing traffic and analysing the packets using wireshark
  ### case(i):Without enabling IPsec:

  On Host1:
  - Disable IPsec connection

         sudo strongswan stop
    
  On Host2:
  - Disable IPsec connection

         sudo strongswan stop

  On Host2:   
- Go to browser and type:  
          
          http://[host1 ip addr>]

- Wireshark Packet capture:

        sudo dnf install wireshark
        sudo wireshark

- Refresh the website on browser.
- On Wireshark you can see that the website is being accessed through HTTP protocol and the text of server website can be clearly seen.

### case(ii):With IPsec enabled:

On Host1 and Host2: 
- Start Wireshark capturing:
 
       sudo dnf install wireshark
       sudo wireshark
  
- Enabling IPsec connection between Host1 and Host2

       sudo strongswan restart
       sudo strongswan up host-host

- After the above commands, ISAKMP packets will be generated on wireshark. This will create a security association(Handshaking) between Host1 and Host2. Once this is done, The actual data packets will be exchanged.
- Refresh the website on the browser. This will generate ESP packets on wireshark. Here the content of the website is completely in unreadable form.









