1. Enable
   
       Router>enable
       Router#
2. configure terminal
   
       Router#configure terminal
       Router(config)#
3. Unicast Routing

        Router(config)#ipv6 unicast-routing 
        Router(config)#
4. Select interface

        Router(config)#interface gigabitEthernet 0/0
        Router(config-if)#
5. Assign IPv6 Address

        Router(config-if)#ipv6 address <2001:a::1/64>
6. Enable Routing Information Protocol (RIP)

         Router(config-if)#ipv6 rip <abc> enable
7. Enable IPv6

         Router(config-if)#ipv6 enable
8. Up the Interface

         Router(config-if)#no shutdown

9. Exit to repeat Step-4 to Step-8

         Router(config-if)#exit
         Router(config)#
10. End

         Router(config-if)#end 
