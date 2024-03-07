## Router Configuration in 3 Network (PC <---> R1 <---> R2 <---> PC) Setup

1. Enable
   
       Router>enable
       Router#
3. configure terminal
   
       Router#configure terminal
       Router(config)#
4. Unicast Routing

        Router(config)#ipv6 unicast-routing 
        Router(config)#
5. Select interface

        Router(config)#interface gigabitEthernet 0/0
        Router(config-if)#
6. Assign IPv6 Address

        Router(config-if)#ipv6 address <2001:a::1/64>
7. Enable Routing Information Protocol (RIP)

         Router(config-if)#ipv6 rip <abc> enable
8. Enable IPv6

         Router(config-if)#ipv6 enable
9. Up the Interface

         Router(config-if)#no shutdown

10. Exit to repeat Step-4 to Step-8

         Router(config-if)#exit
         Router(config)#
11. End

         Router(config-if)#end
12. To view the interface detail

         Router#show ipv6 interface brief
