+++
categories = ["igps", "isis"]
date = "2019-06-16"
description = "Integrated IS-IS notes"
title = "Integrated IS-IS notes"
head = "Integrated IS-IS notes"
+++

## Basics

* originaly ISO protocol
* [RFC 1195](https://tools.ietf.org/pdf/rfc1195.pdf) defined for (also) routing TCP/IP
* SPF algoritm / Dijkstra

### ISO Adressing

* Address consists of: IDP + DSP
    * IDP = Initial Domain Part
        * part standardized by ISO, specifies format & addressing authority for DSP part 
    * DSP = Domain Specific Part
        * HO-DSP (High Order part of DSL)
        * ID (system identifier)
        * SEL (NSAP selector)
* IDP+HO-DSP can be called area address
    * for transition purposes there might be multiple area addresses in one area
    * area address in OSI addressing allows l1 routers to identify packets to destinations outside their area


### ISO Areas

* Level 1 and Level 2 areas
* Level 1 
    * L1 routers know the topology inside their area; they do not know the identity of routers outside their area
    * L1 routers forward all traffic for destinations outside their area to level 2 router in their area
    * L1 routers within an area, based on ID portion of the ISO address
    * L1 routers examine the ISO address, if within their own area then they route towards destination, if not they ship the packet to nearest L2 router 
    * L1 router will become neighbor only with routers in the SAME area
* Level 2
    * L2 routers in Level 2 area do not need to know the topology of level 1 areas as well
    * L2 routers route based on area address (IDP+HO-DSP), they route towards areas (without regard to the internal structure of the area)
    * L2 router will become neighbor with another L2 router REGARDLESS of area address
        * only L2 LSPs will flow on link between two different areas
* L2 router might be also L1 router in one area  

### IP areas

* Level 1
    * L1 routers route via L1 routing if destination address MATCHES [IP address, subnet mask, metric] found reachable withing the area
    * L1 routes route to nearest L2 router if destination address DOES NOT MATCH ANY [IP address, subnet mask, metric] found reachable withing the area
* Level 2
    * L2 routers include in their L2 LSPs a comple list of  [IP address, subnet mask, metric] specifying all IP addresses found in their area, this is obtained from L1 routers (or configured manually)
    * default routes are permitted only at L2

### Broadcast segments (LANs)

* Problem:
    * multiple routers on LAN - each router would announce a link to every other router (n^2 links reported)
* Solution:
    * create a pseudonode representing the LAN, each router announces it has a link to the pseudonode
    * one router is selected to send LSP on behalf of the pseudonode (designated router); reporting links to all of the router on the LAN

### 3-Way handshaking

* LAN Hello uses the IS Neighbors TLV to list all neighbors from which a hello has been received
* P2P Hellos do not carry IS Neighbors TLVs. ISO 10589 prescribes only two-way handshaking in this case. RFC 3373 specifies P2P Three-way Adjacency TLV (240)

```
Point-to-point Adjacency State (t=240, l=15)
    Type: 240
    Length: 15
    Adjacency State: Up (0)
    Extended Local circuit ID: 0x00000048
    Neighbor SystemID: 1720.1600.1002
    Neighbor Extended Local circuit ID: 0x00000047
```


### Adjacency states

* Up
* Initializing
* Down

### Debug outputs

**set protocols isis traceoptions flag state**
reset isis adjacency
```
Jun 16 15:41:36.289849 Adjacency state change, vSRX1, state Up -> Down
Jun 16 15:41:36.289923     interface ge-0/0/0.0, level 3
Jun 16 15:41:36.291278 ISIS L3 neighbor vSRX1 on interface ge-0/0/0.0 deleted
Jun 16 15:41:36.293005 ISIS programmed L3 periodic xmit to 09:00:2b:00:00:05 interface ge-0/0/0.0, interval 9 secs 0 nsecs
Jun 16 15:41:36.314703 Adjacency state change, vSRX1, state Down -> Initializing
Jun 16 15:41:36.314769     interface ge-0/0/0.0, level 3
Jun 16 15:41:36.316573 ISIS L3 neighbor vSRX1 on interface ge-0/0/0.0 set 27 secs 0 nsecs
Jun 16 15:41:36.318667 ISIS programmed L3 periodic xmit to 09:00:2b:00:00:05 interface ge-0/0/0.0, interval 9 secs 0 nsecs
Jun 16 15:41:36.344605 Adjacency state change, vSRX1, state Initializing -> Up
Jun 16 15:41:36.344654     interface ge-0/0/0.0, level 3
Jun 16 15:41:36.345246 Starting database exchange on ge-0/0/0.0
Jun 16 15:41:36.346720 ISIS L3 neighbor vSRX1 on interface ge-0/0/0.0 set 27 secs 0 nsecs
Jun 16 15:41:36.348422 ISIS programmed L3 periodic xmit to 09:00:2b:00:00:05 interface ge-0/0/0.0, interval 9 secs 0 nsecs
```

**set protocols isis traceoptions flag packets**
```
*** debugisis ***
Jun 16 15:53:21 vSRX2 clear-log[1337]: logfile cleared
Jun 16 15:53:24.345379 Received PTP IIH, source id vSRX1 on ge-0/0/0.0
Jun 16 15:53:24.346181 Sending PTP IIH on ge-0/0/0.0
Jun 16 15:53:24.346226     packet length 48
Jun 16 15:53:24.360054 Received PTP IIH, source id vSRX1 on ge-0/0/0.0
Jun 16 15:53:24.360373 Sending PTP IIH on ge-0/0/0.0
Jun 16 15:53:24.360431     packet length 1492
Jun 16 15:53:24.374327 Updating L1 LSP vSRX2.00-00 in TED
Jun 16 15:53:24.374646 Updating L2 LSP vSRX2.00-00 in TED
Jun 16 15:53:24.375810 Received PTP IIH, source id vSRX1 on ge-0/0/0.0
Jun 16 15:53:24.376482 Sending PTP IIH on ge-0/0/0.0
Jun 16 15:53:24.376527     packet length 58
Jun 16 15:53:24.400416 Received L1 LSP vSRX1.00-00, on interface ge-0/0/0.0
Jun 16 15:53:24.400464     from vSRX1
Jun 16 15:53:24.400504     sequence 0x10, checksum 0xfaea, lifetime 1198
Jun 16 15:53:24.400571 Updating L1 LSP vSRX1.00-00 in TED
Jun 16 15:53:24.400901 Updating L1 LSP vSRX2.00-00 in TED
Jun 16 15:53:24.401244 Sending L1 LSP vSRX2.00-00 on interface ge-0/0/0.0
Jun 16 15:53:24.401283     sequence 0x12, checksum 0xe4fa, lifetime 1200
Jun 16 15:53:24.401322     packet length 150
Jun 16 15:53:24.401736 Updating L2 LSP vSRX2.00-00 in TED
Jun 16 15:53:24.420761 Received L2 LSP vSRX1.00-00, on interface ge-0/0/0.0
Jun 16 15:53:24.420810     from vSRX1
Jun 16 15:53:24.420861     sequence 0x10, checksum 0x5c3f, lifetime 1198
Jun 16 15:53:24.420919 Updating L2 LSP vSRX1.00-00 in TED
Jun 16 15:53:24.421266 Sending L2 LSP vSRX2.00-00 on interface ge-0/0/0.0
Jun 16 15:53:24.421306     sequence 0x12, checksum 0xff97, lifetime 1200
Jun 16 15:53:24.421344     packet length 171
Jun 16 15:53:25.281547 Sending L1 PSN on interface ge-0/0/0.0
Jun 16 15:53:25.281635     packet length 35
Jun 16 15:53:25.281684 Sending L2 PSN on interface ge-0/0/0.0
Jun 16 15:53:25.281723     packet length 35
Jun 16 15:53:25.298222 Received L1 PSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:25.299512 Received L2 PSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:31.810825 ISIS L3 hello from vSRX1 interface ge-0/0/0.0 absorbed
Jun 16 15:53:32.923675 ISIS L3 periodic xmit to 09:00:2b:00:00:05 interface ge-0/0/0.0
Jun 16 15:53:35.429957 Sending L1 CSN on interface ge-0/0/0.0
Jun 16 15:53:35.430041     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:35.430091     packet length 67
Jun 16 15:53:35.441234 Received L1 CSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:35.441279     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:35.441318     packet length 67
Jun 16 15:53:35.454423 Sending L2 CSN on interface ge-0/0/0.0
Jun 16 15:53:35.454480     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:35.454520     packet length 67
Jun 16 15:53:35.460799 Received L2 CSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:35.460849     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:35.460887     packet length 67
Jun 16 15:53:40.434039 Sending L1 CSN on interface ge-0/0/0.0
Jun 16 15:53:40.434126     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:40.434167     packet length 67
Jun 16 15:53:40.446845 ISIS L3 hello from vSRX1 interface ge-0/0/0.0 absorbed
Jun 16 15:53:40.448599 Received L1 CSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:40.448641     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:40.448679     packet length 67
Jun 16 15:53:40.458292 Sending L2 CSN on interface ge-0/0/0.0
Jun 16 15:53:40.458352     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:40.458392     packet length 67
Jun 16 15:53:40.475493 Received L2 CSN, source vSRX1, interface ge-0/0/0.0
Jun 16 15:53:40.475543     LSP range 0000.0000.0000.00-00 to ffff.ffff.ffff.ff-ff
Jun 16 15:53:40.475583     packet length 67
```