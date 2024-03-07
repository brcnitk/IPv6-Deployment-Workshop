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
6. 
