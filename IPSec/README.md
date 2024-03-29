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


Prerequisites:      
  (i) Oracle VirtualBox: Download and install Oracle VirtualBox from [Oracle's official website](https://www.virtualbox.org/).      
(ii) Fedora OS: Download the Fedora ISO image from [Fedora's official website](https://fedoraproject.org/workstation/download).
  
  
  
## Setting up the network topology
Virtual Machine Creation:    

Create four virtual machines in Oracle VirtualBox and name them accordingly:      
(i) Host1    
(ii) Host2    
(iii) Gateway1    
(iv) Gateway2

Network Interface Setup:

1. Set up the network interfaces of the virtual machines to use an internal network type within VirtualBox. This is done to ensure isolation and control over the network environment, which is crucial for testing purposes.

2. Name the network interface as Network-1, Network-2 and Network-3.

3. Attach the networks to the following Virtual Machines:      
   (i)Gateway1 : Network-1, Network-2 (2 Adapters)      
   (ii)Gateway2 : Network-3, Network-2 (2 Adapters)     
   (iii)Host2 : Network-3 (1 Adapter)       
    (iv)Host1 : Network-1 (1 Adapter)

### On Gateway1:

1.Use the following commands to configure the network interface:

     nmcli c
     nmcli c edit $uuid

The `nmcli` command is used to manage network connections in Linux. It is used to create, delete, activate and deactivate the network interface. 
    
2. Replace `$uuid` with the UUID of the network connection you wish to configure. This will open up the NetworkManager editor for the selected connection, allowing you to modify its settings. Then the `nmcli>` interface will appear.

3. To save it use:
    ```
    save persistent
    ```
    in the `nmcli>` interface.

4. To quit from the `nmcli>` interface use
    ```
    quit
    ```

5. On saving, a file is formed at `/etc/NetworkManager/system-connections/` named as `Wired connection 1.nmconnection`.
Note: The connection number may differ depending upon your socket.


6. Open the file in sudo mode using the following command and make the following changes:

        sudo nano /etc/NetworkManager/system-connections/'Wired connection 1.nmconnection'
   
-   **Gateway1 Interface 1:**
    ```
    address1=2001:db8:1::1/64
    method=manual
    ```
Repeat the above step from 2 to 3 to create another file for network interface 2. Here the file name will be `Wired connection 2.nmconnection` and you will have to make changes in this file.

-   **Gateway1 Interface 2:**
    ```
    address1=2001:db8:2::1/64
    method=manual
    route1=2001:db8:3::/64,2001:db8:2::2
    ```
### On Gateway2:
Repeat the above step 1 onwards in Router as per the folowing changes.

-   **Gateway2 Interface 1:**
    ```
    address1=2001:db8:3::1/64
    method=manual
    ```
-   **Gateway2 Interface 2:**
    ```
    address1=2001:db8:2::2/64
    method=manual
    route1=2001:db8:1::/64,2001:db8:2::1
    ```

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









