## Router Configuration in 3 Network (PC <---> R1 <---> R2 <---> PC) Setup

1. Enable for privilage interface
   
       Router>enable
       Router#
3. To enter into command interface
   
       Router#configure terminal
       Router(config)#
4. Enable unicast uouting (only once on a router)

        Router(config)#ipv6 unicast-routing 
        Router(config)#
5. Select interface

        Router(config)#interface gigabitEthernet 0/0
        Router(config-if)#
6. Assign public/global IPv6 address

        Router(config-if)#ipv6 address <2001:a::1/64>
7. To assign link-local address (optional)

         Router(config-if)#ipv6 address <fe80::1> link-local
9. Enable Routing Information Protocol (RIP)

         Router(config-if)#ipv6 rip <abc> enable
10. Enable IPv6

         Router(config-if)#ipv6 enable
11. Up the Interface

         Router(config-if)#no shutdown

12. Exit to repeat Step-4 to Step-8

         Router(config-if)#exit
         Router(config)#
13. End

         Router(config-if)#end
14. To view the interface detail

         Router#show ipv6 interface brief
