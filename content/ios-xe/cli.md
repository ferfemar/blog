+++
categories = ["ios-xe", "cli"]
date = "2019-06-16"
description = "IOS-XE CLI"
title = "IOS-XE CLI"
head = "IOS-XE CLI"
+++

IOS-XE CLI tricks

## IOS-XE Datapath Packet Trace Feature

```
Run:

debug platform condition interface GigabitEthernet0/0/0.100 ipv4 <IP ADDRESS>/32 both
debug platform condition start
debug platform packet-trace packet 1024 
debug platform packet-trace enable

Examine:
show platform packet-trace statistics
show platform packet-trace summ
show platform packet-trace packet 1

Delete:
debug platform condition stop
clear platform packet-trace statistics  
clear platform condition all  
```

[Source Cisco Docs](https://www.cisco.com/c/en/us/support/docs/content-networking/adaptive-session-redundancy-asr/117858-technote-asr-00.html)
