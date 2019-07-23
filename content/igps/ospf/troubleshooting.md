+++
categories = ["igps", "ospf"]
date = "2019-06-16"
description = "OSPF troubleshooting"
title = "OSPF troubleshooting"
head = "OSPF troubleshooting"
+++

## Troubleshooting

### Neighborship problems checklist

Source: [iNET ZERO](https://www.inetzero.com/what-is-your-ospf-neighbor-doing-adjancency-problems-in-ospf/)

1. Interfaces present in [edit protocols ospf]
2. Interfaces are not passive
3. Interfaces are not down
4. No FF is blocking OSPF protocol 89 
5. Area mismatch
6. Timer mismatch
7. Auth. mismatch
8. Area type mismatch
9. Subnet mask mismatch
10. Duplicate router-id
11. MTU mismatch
12. DR priority = 0 on both routers
13. Duplicate interface ip address
14. Interface type mismatch

### Tools

Show commands.
Traceoptions.
Monitor traffic interface.

```
monitor traffic interface em1 size 1500 no-resolve detail | matching “ip proto ospf”
```

### Authentication debugs

Authentication problem is best identified using traceoptions:

```
[edit protocols ospf traceoptions]
root@vrsx# show
file debugospf size 10m;
flag hello detail;
flag error detail;

[edit protocols ospf traceoptions]
root@vsrx#
```

The message for mismatched authentication looks like this:

```
Jul 10 19:14:59.687339 OSPF packet ignored: authentication failure (bad cksum).
Jul 10 19:14:59.688689 OSPF packet ignored: authentication failure from 172.30.0.26
```

Message when the other side doesn't use authentication, but it is required:

```
Jul 10 19:23:40.327021 OSPF packet ignored: authentication type mismatch (0) from 172.30.0.26
```

Succesful negotiation after fixing the issue looks like this:

```
Jul 10 19:25:11.714647 OSPF rcvd Hello 172.30.0.26 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.714695   Version 2, length 44, ID 172.30.5.6, area 0.0.0.0
Jul 10 19:25:11.714738   checksum 0x0, authtype 2
Jul 10 19:25:11.714782   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.714825   dead_ivl 40, DR 172.30.0.26, BDR 0.0.0.0
Jul 10 19:25:11.714870 OSPF restart signaling: Received hello with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.714923 OSPF restart signaling: Received hello with LR bit set from nbr ip=172.30.0.26 id=172.30.5.6. Set oob-resync capabilty 1.
Jul 10 19:25:11.715291 OSPF restart signaling: set L bit in hellos sent on interface ge-0/0/4.136.
Jul 10 19:25:11.715338 OSPF sent Hello 172.30.0.25 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.715381   Version 2, length 48, ID 172.30.5.3, area 0.0.0.0
Jul 10 19:25:11.715425   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.715467   dead_ivl 40, DR 172.30.0.25, BDR 0.0.0.0
Jul 10 19:25:11.715510 OSPF restart signaling: Add LLS data for Hello packet on interface ge-0/0/4.136.
Jul 10 19:25:11.716654 OSPF rcvd Hello 172.30.0.26 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.716699   Version 2, length 44, ID 172.30.5.6, area 0.0.0.0
Jul 10 19:25:11.716742   checksum 0x0, authtype 2
Jul 10 19:25:11.720641   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.720697   dead_ivl 40, DR 172.30.0.26, BDR 0.0.0.0
Jul 10 19:25:11.720742 OSPF restart signaling: Received hello with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.720786 OSPF restart signaling: Received hello with LR bit set from nbr ip=172.30.0.26 id=172.30.5.6. Set oob-resync capabilty 1.
Jul 10 19:25:11.748618 OSPF rcvd Hello 172.30.0.26 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.748673   Version 2, length 48, ID 172.30.5.6, area 0.0.0.0
Jul 10 19:25:11.748730   checksum 0x0, authtype 2
Jul 10 19:25:11.748774   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.748817   dead_ivl 40, DR 172.30.0.26, BDR 0.0.0.0
Jul 10 19:25:11.748866 OSPF restart signaling: Received hello with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.748912 OSPF restart signaling: Received hello with LR bit set from nbr ip=172.30.0.26 id=172.30.5.6. Set oob-resync capabilty 1.
Jul 10 19:25:11.749027 RPD_OSPF_NBRUP: OSPF neighbor 172.30.0.26 (realm ospf-v2 ge-0/0/4.136 area 0.0.0.0) state changed from Init to ExStart due to 2WayRcvd (event reason: neighbor detected this router)
Jul 10 19:25:11.749117 OSPF restart signaling: Send DBD with LR bit on to nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.749216 OSPF restart signaling: Add LLS data for DbD packet on interface ge-0/0/4.136.
Jul 10 19:25:11.756076 OSPF DR is 172.30.5.6, BDR is 172.30.5.3
Jul 10 19:25:11.756757 OSPF hello from 172.30.0.26 (IFL 74, area 0.0.0.0) absorbed
Jul 10 19:25:11.762080 OSPF restart signaling: set L bit in hellos sent on interface ge-0/0/4.136.
Jul 10 19:25:11.762768 OSPF sent Hello 172.30.0.25 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.763076   Version 2, length 48, ID 172.30.5.3, area 0.0.0.0
Jul 10 19:25:11.763152   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.763225   dead_ivl 40, DR 172.30.0.26, BDR 172.30.0.25
Jul 10 19:25:11.763303 OSPF restart signaling: Add LLS data for Hello packet on interface ge-0/0/4.136.
Jul 10 19:25:11.763893 OSPF restart signaling: Received DBD with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.764106 OSPF restart signaling: Send DBD with LR bit on to nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.802212 OSPF restart signaling: Add LLS data for DbD packet on interface ge-0/0/4.136.
Jul 10 19:25:11.821028 OSPF rcvd Hello 172.30.0.26 -> 224.0.0.5 (ge-0/0/4.136 IFL 74 area 0.0.0.0)
Jul 10 19:25:11.821083   Version 2, length 48, ID 172.30.5.6, area 0.0.0.0
Jul 10 19:25:11.821124   checksum 0x0, authtype 2
Jul 10 19:25:11.821217   mask 255.255.255.252, hello_ivl 10, opts 0x12, prio 128
Jul 10 19:25:11.821269   dead_ivl 40, DR 172.30.0.26, BDR 172.30.0.25
Jul 10 19:25:11.821390 OSPF restart signaling: Received hello with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.821436 OSPF restart signaling: Received hello with LR bit set from nbr ip=172.30.0.26 id=172.30.5.6. Set oob-resync capabilty 1.
Jul 10 19:25:11.823745 OSPF restart signaling: Received DBD with LLS data from nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.823806 OSPF restart signaling: Send DBD with LR bit on to nbr ip=172.30.0.26 id=172.30.5.6.
Jul 10 19:25:11.858401 OSPF restart signaling: Add LLS data for DbD packet on interface ge-0/0/4.136.
Jul 10 19:25:11.882496 RPD_OSPF_NBRUP: OSPF neighbor 172.30.0.26 (realm ospf-v2 ge-0/0/4.136 area 0.0.0.0) state changed from Loading to Full due to LoadDone (event reason: OSPF loading completed)
Jul 10 19:25:12.266533 OSPF periodic xmit from 172.30.0.21 to 224.0.0.5 (IFL 73 area 0.0.0.4)
Jul 10 19:25:14.533324 OSPF hello from 172.30.0.22 (IFL 73, area 0.0.0.4) absorbed
Jul 10 19:25:18.489407 OSPF hello from 172.30.0.13 (IFL 72, area 0.0.0.0) absorbed
Jul 10 19:25:19.197534 OSPF periodic xmit from 172.30.0.14 to 224.0.0.5 (IFL 72 area 0.0.0.0)
Jul 10 19:25:19.826699 OSPF hello from 172.30.0.26 (IFL 74, area 0.0.0.0) absorbed
```

## Resources

[iNET ZERO](https://www.inetzero.com/what-is-your-ospf-neighbor-doing-adjancency-problems-in-ospf/)