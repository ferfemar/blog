+++
categories = ["mpls", "vpls", "vpn"]
date = "2019-05-16"
description = "VPLS"
title = "VPLS"
head = "VPLS"
+++

Virtual Private LAN Service = VPLS = Layer 2 MPLS VPN

## Basics

* Variants
 * RFC 4761 - Virtual Private LAN Service (VPLS) - Using BGP for Auto-Discovery and Signaling
 * RFC 4762 - Virtual Private LAN Service (VPLS) Using Label Distribution Protocol (LDP) Signaling

* BGP auto-discovery
 * PEs auto-discover other PEs in the same VPLS instance
 * automatic pseudowire establishment 
 * full MPLS LSP mesh between PEs (RSVP or LDP signalled)

* BGP or LDP for service label distribution

* Data-plane MAC learning 
 * MAC table per VPLS instance on PE
 * BUM flooding over VPLS instance

* Split horizon rules
 * frame received on PW is never sent back on the same PW
 * default: frame received on a PW is not forwarded on any other PW

* Ethernet media only (contrast w/ L2VPN)

## Configuration

### Juniper

RRs and PEs have to have BGP **l2vpn signaling** family (AFI=25, SAFI=65) configured to support BGP signaled VPLS:

```
set protocols bgp group rr-cluster family l2vpn signaling
```