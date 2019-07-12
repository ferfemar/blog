+++
categories = ["junos", "cli"]
date = "2019-06-16"
description = "Junos CLI"
title = "Junos CLI"
head = "Junos CLI"
+++

Junos CLI tricks

## Show only certain prefix length routes 

The "match-prefix" command allows for regex filtering

```
root@vsrx> show route protocol ospf match-prefix */32                
                                                                    
inet.0: 42 destinations, 42 routes (42 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both                         
                                                                    
172.30.5.2/32      *[OSPF/10] 01:31:06, metric 1                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.3/32      *[OSPF/10] 01:31:06, metric 2                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.4/32      *[OSPF/10] 00:43:54, metric 3                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.5/32      *[OSPF/10] 01:31:06, metric 4                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.6/32      *[OSPF/10] 01:31:06, metric 3                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.7/32      *[OSPF/10] 01:31:06, metric 2                    
                    > to 172.30.0.2 via ae0.0                       
172.30.5.8/32      *[OSPF/10] 03:34:28, metric 1                    
                    > to 172.30.0.10 via ge-0/0/4.118               
224.0.0.5/32       *[OSPF/10] 03:50:19, metric 1                    
                      MultiRecv                                     
                                                                    
inet6.0: 5 destinations, 6 routes (5 active, 0 holddown, 0 hidden)  
                                                                    
root@vsrx>                                                           
```