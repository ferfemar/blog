+++
categories = ["igps", "ospf"]
date = "2019-06-16"
description = "OSPF"
title = "OSPF"
head = "OSPF"
+++

OSPF notes

## Troubleshooting

Authentication problem is best identified using traceoptions:

```
[edit protocols ospf traceoptions]
root@Canopus# show
file debugospf size 10m;
flag hello detail;
flag error detail;

[edit protocols ospf traceoptions]
root@Canopus#
```

The message for mismatched authentication looks like this:

```
Jul 10 19:14:59.687339 OSPF packet ignored: authentication failure (bad cksum).
Jul 10 19:14:59.688689 OSPF packet ignored: authentication failure from 172.30.0.26
```