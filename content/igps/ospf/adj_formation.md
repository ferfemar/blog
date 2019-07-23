+++
categories = ["igps", "ospf"]
date = "2019-06-16"
description = "OSPF ddj. formation notes"
title = "OSPF adj. formation notes"
head = "OSPF adj. formation notes"
+++

## Adjacency formation theory

DBD Packets bits:

 - I = Initial bit
 - M = More bit
 - MS = Master/Slave bit

## Adjacency formation practise

- PCAP of simple OSPF adj. formation: [here](/pcaps/ospf_adj.pcapng)
- Traceoptions: [R1](/traceoptions/ospf_simple_adj_R1_debug.txt) and [R2](/traceoptions/ospf_simple_adj_R2_debug.txt)

My notes on the packet captures and traceoptions of simple adjacency formation follows:

- **OSPF speakers send out hello packets**

R1 sends hello packet to 224.0.0.5:
```
Frame 5: 90 bytes on wire (720 bits), 90 bytes captured (720 bits) on interface 0
Ethernet II, Src: 50:00:00:0c:00:00 (50:00:00:0c:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.0, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
        Version: 2
        Message Type: Hello Packet (1)
        Packet Length: 44
        Source OSPF Router: 1.1.1.1
        Area ID: 0.0.0.0 (Backbone)
        Checksum: 0xe91f [correct]
        Auth Type: Null (0)
        Auth Data (none): 0000000000000000
    OSPF Hello Packet
        Network Mask: 255.255.255.254
        Hello Interval [sec]: 10
        Options: 0x12, (L) LLS Data block, (E) External Routing
        Router Priority: 128
        Router Dead Interval [sec]: 40
        Designated Router: 0.0.0.0
        Backup Designated Router: 0.0.0.0
    OSPF LLS Data Block
```

R2 sends analogous packet. Not shown.

- **OSPF speakers establish two-way adjacency by listing each other in the hello packet**

R1 hears the R2's hello packet and next hello packet it advertises contains the R2s RID in 'Active Neighbor' field:
```
Frame 9: 94 bytes on wire (752 bits), 94 bytes captured (752 bits) on interface 0
Ethernet II, Src: 50:00:00:0c:00:00 (50:00:00:0c:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.0, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    OSPF Hello Packet
        Network Mask: 255.255.255.254
        Hello Interval [sec]: 10
        Options: 0x12, (L) LLS Data block, (E) External Routing
        Router Priority: 128
        Router Dead Interval [sec]: 40
        Designated Router: 0.0.0.0
        Backup Designated Router: 0.0.0.0
        Active Neighbor: 2.2.2.2
    OSPF LLS Data Block
```

```
# R1 Traceoptions
Jul 22 16:17:05.608796 OSPF rcvd Hello 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.608842   Version 2, length 44, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:05.608882   checksum 0x0, authtype 0
Jul 22 16:17:05.608922   mask 255.255.255.254, hello_ivl 10, opts 0x12, prio 128
Jul 22 16:17:05.608961   dead_ivl 40, DR 0.0.0.0, BDR 0.0.0.0
Jul 22 16:17:05.609013 OSPF restart signaling: Received hello with LLS data from nbr ip=192.168.0.1 id=2.2.2.2.
Jul 22 16:17:05.609281 OSPF neighbor 192.168.0.1 (ge-0/0/0.0 area 0.0.0.0) state changed from Down to Init due to HelloRcvd (event reason: Hello message received) (nbr helped: 0)
Jul 22 16:17:05.609322 OSPF restart signaling: Received hello with LR bit set from nbr ip=192.168.0.1 id=2.2.2.2. Set oob-resync capabilty 1.
Jul 22 16:17:05.609872 OSPF restart signaling: set L bit in hellos sent on interface ge-0/0/0.0.
Jul 22 16:17:05.609914 OSPF sent Hello 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.609954   Version 2, length 48, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:05.609994   mask 255.255.255.254, hello_ivl 10, opts 0x12, prio 128
Jul 22 16:17:05.610046   dead_ivl 40, DR 0.0.0.0, BDR 0.0.0.0
Jul 22 16:17:05.610126   neighbor 2.2.2.2
```

- **ExStart stage. Neighbors exchange (empty) DBD packets. Both assert masterhip over the dtbs exchange process by setting bits I,M,MS to 1 and setting the sequence number to one they like.**

R2 packet to R1:
```
Frame 11: 78 bytes on wire (624 bits), 78 bytes captured (624 bits) on interface 0
Ethernet II, Src: 50:00:00:0d:00:00 (50:00:00:0d:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.1, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
        Version: 2
        Message Type: DB Description (2)
        Packet Length: 32
        Source OSPF Router: 2.2.2.2
        Area ID: 0.0.0.0 (Backbone)
        Checksum: 0xf084 [correct]
        Auth Type: Null (0)
        Auth Data (none): 0000000000000000
    OSPF DB Description
        Interface MTU: 1500
        Options: 0x52, O, (L) LLS Data block, (E) External Routing
        DB Description: 0x07, (I) Init, (M) More, (MS) Master
            .... 0... = (R) OOBResync: Not set
            .... .1.. = (I) Init: Set
            .... ..1. = (M) More: Set
            .... ...1 = (MS) Master: Yes
        DD Sequence: 33992555
    OSPF LLS Data Block
```

```
# R2 traceoptions
Jul 22 16:17:06.951908 OSPF sent DbD 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:06.951952   Version 2, length 32, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:06.952011   options 0x52, i 1, m 1, ms 1, r 0, seq 0x206af6b, mtu 1500
Jul 22 16:17:06.982916 OSPF rcvd DbD 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:06.982956   Version 2, length 32, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:06.982996   checksum 0x0, authtype 0
Jul 22 16:17:06.983036   options 0x52, i 1, m 1, ms 1, r 0, seq 0x10c1ca2, mtu 1500
Jul 22 16:17:06.983538 ospf_process_dbd: dbd from 192.168.0.0 ignored
```

- **Router with LOWER RID becomes the SLAVE and sends new (POPULATED) DBD with MS set to 0 and seq. to what the master set in the previous exchange.**
- **Master replies also with (POPULATED) DBD with sequence number incresed by one.**

R1 has lower rid, so it sends the "I am the slave, you are the master" message
```
Frame 15: 98 bytes on wire (784 bits), 98 bytes captured (784 bits) on interface 0
Ethernet II, Src: 50:00:00:0c:00:00 (50:00:00:0c:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.0, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    OSPF DB Description
        Interface MTU: 1500
        Options: 0x52, O, (L) LLS Data block, (E) External Routing
        DB Description: 0x00
            .... 0... = (R) OOBResync: Not set
            .... .0.. = (I) Init: Not set
            .... ..0. = (M) More: Not set
            .... ...0 = (MS) Master: No
        DD Sequence: 33992555
    LSA-type 1 (Router-LSA), len 36
    OSPF LLS Data Block
```

```
#R1 traceoptions
Jul 22 16:17:05.733316 OSPF rcvd DbD 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.733355   Version 2, length 32, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:05.733395   checksum 0x0, authtype 0
Jul 22 16:17:05.733434   options 0x52, i 1, m 1, ms 1, r 0, seq 0x206af6b, mtu 1500
Jul 22 16:17:05.733476 task_job_create_background: create prio 1 job OSPF process rcvd dbd packets for task OSPF
Jul 22 16:17:05.733789 background dispatch running job OSPF process rcvd dbd packets for task OSPF
Jul 22 16:17:05.733865 ospf_process_dbd: processing dbd from 192.168.0.1
Jul 22 16:17:05.733905 OSPF restart signaling: Received DBD with LLS data from nbr ip=192.168.0.1 id=2.2.2.2.
Jul 22 16:17:05.733945 OSPF now slave for nbr 192.168.0.1
Jul 22 16:17:05.734027 OSPF neighbor 192.168.0.1 (ge-0/0/0.0 area 0.0.0.0) state changed from ExStart to Exchange due to NegotiationDone (event reason: master/slave negotiation completed) (nbr helped: 0)
Jul 22 16:17:05.734068   In sequence
```

Master reply
```
Frame 16: 98 bytes on wire (784 bits), 98 bytes captured (784 bits) on interface 0
Ethernet II, Src: 50:00:00:0d:00:00 (50:00:00:0d:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.1, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    OSPF DB Description
        Interface MTU: 1500
        Options: 0x52, O, (L) LLS Data block, (E) External Routing
        DB Description: 0x01, (MS) Master
        DD Sequence: 33992556
    LSA-type 1 (Router-LSA), len 36
    OSPF LLS Data Block
```

```
#R2 traceoptions
Jul 22 16:17:07.026463 OSPF rcvd DbD 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:07.026894   Version 2, length 52, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:07.026938   checksum 0x0, authtype 0
Jul 22 16:17:07.026978   options 0x52, i 0, m 0, ms 0, r 0, seq 0x206af6b, mtu 1500
Jul 22 16:17:07.027035   id 1.1.1.1, type Router (0x1), age 0x1
Jul 22 16:17:07.027074   options 0x22
Jul 22 16:17:07.027114   adv rtr 1.1.1.1, seq 0x80000001, cksum 0x2b96, len 36
Jul 22 16:17:07.027642 OSPF now master for nbr 192.168.0.0
Jul 22 16:17:07.027726 OSPF neighbor 192.168.0.0 (ge-0/0/0.0 area 0.0.0.0) state changed from ExStart to Exchange due to NegotiationDone (event reason: master/slave negotiation completed) (nbr helped: 0)
Jul 22 16:17:07.028608 background dispatch completed job OSPF process rcvd dbd packets for task OSPF
Jul 22 16:17:07.029619 OSPF sent DbD 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:07.029663   Version 2, length 52, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:07.029704   options 0x52, i 0, m 0, ms 1, r 0, seq 0x206af6c, mtu 1500
Jul 22 16:17:07.029744   id 2.2.2.2, type Router (0x1), age 0x0
Jul 22 16:17:07.029783   options 0x22
Jul 22 16:17:07.029823   adv rtr 2.2.2.2, seq 0x80000001, cksum 0xdeda, len 36
```

- **In the EXCHANGE state the routers synchronize databases.**
    - Master controls the sync process. Only one DBD can be outstanding at a time. 
    - Each dd packet slave receives is acknowledged with dd packet w/ the same sequence number.
    - Slave sends DD packets only in response to Master DD packets.

Master requests the LSA using LSR

```
Frame 17: 70 bytes on wire (560 bits), 70 bytes captured (560 bits) on interface 0
Ethernet II, Src: 50:00:00:0d:00:00 (50:00:00:0d:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.1, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    Link State Request
        LS Type: Router-LSA (1)
        Link State ID: 1.1.1.1
        Advertising Router: 1.1.1.1
```

```
#R2 traceoptions
Jul 22 16:17:07.030062 OSPF sent LSReq 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:07.030102   Version 2, length 36, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:07.030154   type Router (1), id 1.1.1.1, adv rtr 1.1.1.1
```

Slave requests the LSA using LSR
```
Frame 19: 70 bytes on wire (560 bits), 70 bytes captured (560 bits) on interface 0
Ethernet II, Src: 50:00:00:0c:00:00 (50:00:00:0c:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.0, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
        Version: 2
        Message Type: LS Request (3)
        Packet Length: 36
        Source OSPF Router: 1.1.1.1
        Area ID: 0.0.0.0 (Backbone)
        Checksum: 0xf3cd [correct]
        Auth Type: Null (0)
        Auth Data (none): 0000000000000000
    Link State Request
        LS Type: Router-LSA (1)
        Link State ID: 2.2.2.2
        Advertising Router: 2.2.2.2
```

```
# R1 traceoptions - receives LSReq
Jul 22 16:17:05.801405 OSPF rcvd LSReq 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.801444   Version 2, length 36, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:05.801493   checksum 0x0, authtype 0
Jul 22 16:17:05.801534   type Router (1), id 1.1.1.1, adv rtr 1.1.1.1
# sends LSReq
Jul 22 16:17:05.822003 OSPF sent LSReq 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.822043   Version 2, length 36, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:05.822084   type Router (1), id 2.2.2.2, adv rtr 2.2.2.2
```

Slave sends and receives the LSA in LSU

LSU from Slave to Master
```
Frame 20: 98 bytes on wire (784 bits), 98 bytes captured (784 bits) on interface 0
Ethernet II, Src: 50:00:00:0c:00:00 (50:00:00:0c:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.0, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    LS Update Packet
        Number of LSAs: 1
        LSA-type 1 (Router-LSA), len 36
            .000 0000 0000 0010 = LS Age (seconds): 2
            0... .... .... .... = Do Not Age Flag: 0
            Options: 0x22, (DC) Demand Circuits, (E) External Routing
            LS Type: Router-LSA (1)
            Link State ID: 1.1.1.1
            Advertising Router: 1.1.1.1
            Sequence Number: 0x80000001
            Checksum: 0x2b96
            Length: 36
            Flags: 0x00
            Number of Links: 1
            Type: Stub     ID: 192.168.0.0     Data: 255.255.255.254 Metric: 1
                Link ID: 192.168.0.0 - IP network/subnet number
                Link Data: 255.255.255.254
                Link Type: 3 - Connection to a stub network
                Number of Metrics: 0 - TOS
                0 Metric: 1
```

```
# R1 traceoptions - Sends LSU
Jul 22 16:17:05.822565 OSPF sent LSUpdate 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.822605   Version 2, length 64, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:05.822665   adv count 1
Jul 22 16:17:05.822706   id 1.1.1.1, type Router (0x1), age 0x2
Jul 22 16:17:05.822745   options 0x22
Jul 22 16:17:05.822785   adv rtr 1.1.1.1, seq 0x80000001, cksum 0x2b96, len 36
Jul 22 16:17:05.822836     bits 0x0, link count 1
Jul 22 16:17:05.822877     id 192.168.0.0, data 255.255.255.254, type Stub (3)
Jul 22 16:17:05.822916     Topology count 0, Default metric 1
#  rec. LSU
Jul 22 16:17:05.847393 OSPF rcvd LSUpdate 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:05.847434   Version 2, length 64, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:05.847474   checksum 0x0, authtype 0
Jul 22 16:17:05.847514   adv count 1
Jul 22 16:17:05.847554   id 2.2.2.2, type Router (0x1), age 0x1
Jul 22 16:17:05.847594   options 0x22
Jul 22 16:17:05.847633   adv rtr 2.2.2.2, seq 0x80000001, cksum 0xdeda, len 36
Jul 22 16:17:05.847673     bits 0x0, link count 1
Jul 22 16:17:05.847713     id 192.168.0.0, data 255.255.255.254, type Stub (3)
Jul 22 16:17:05.847752     Topology count 0, Default metric 1
```

- **All LSAs must be individualy acknowledged by either EXPLICIT ACK (LSAck) or IMPLICIT ACK (LSU with the identical LSA)**

LSAck from master to slave:

```
Frame 22: 78 bytes on wire (624 bits), 78 bytes captured (624 bits) on interface 0
Ethernet II, Src: 50:00:00:0d:00:00 (50:00:00:0d:00:00), Dst: IPv4mcast_05 (01:00:5e:00:00:05)
Internet Protocol Version 4, Src: 192.168.0.1, Dst: 224.0.0.5
Open Shortest Path First
    OSPF Header
    LSA-type 1 (Router-LSA), len 36
        .000 0000 0000 0010 = LS Age (seconds): 2
        0... .... .... .... = Do Not Age Flag: 0
        Options: 0x22, (DC) Demand Circuits, (E) External Routing
        LS Type: Router-LSA (1)
        Link State ID: 1.1.1.1
        Advertising Router: 1.1.1.1
        Sequence Number: 0x80000001
        Checksum: 0x2b96
        Length: 36
```

```
# R1 Traceoptions

# sends LSAck
Jul 22 16:17:06.857091 OSPF sent LSAck 192.168.0.0 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:06.857132   Version 2, length 44, ID 1.1.1.1, area 0.0.0.0
Jul 22 16:17:06.857172   id 2.2.2.2, type Router (0x1), age 0x1
Jul 22 16:17:06.857212   options 0x22
Jul 22 16:17:06.857251   adv rtr 2.2.2.2, seq 0x80000001, cksum 0xdeda, len 36
# receives LSAck
Jul 22 16:17:06.858828 task_timer_uset: timer OSPF I/O./var/run/ppmd_control_PPM Hold <Touched> set to offset 2:00 at 16:19:06
Jul 22 16:17:06.858873 OSPF rcvd LSAck 192.168.0.1 -> 224.0.0.5 (ge-0/0/0.0 IFL 70 area 0.0.0.0)
Jul 22 16:17:06.858913   Version 2, length 44, ID 2.2.2.2, area 0.0.0.0
Jul 22 16:17:06.858953   checksum 0x0, authtype 0
Jul 22 16:17:06.858992   id 1.1.1.1, type Router (0x1), age 0x2
Jul 22 16:17:06.859032   options 0x22
Jul 22 16:17:06.859071   adv rtr 1.1.1.1, seq 0x80000001, cksum 0x2b96, len 36
```

Note to self: When the routers receive LSAs from each other - their databases change and LSAs are flodded again.
