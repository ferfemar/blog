+++
categories = ["netops", "ansible"]
date = "2019-09-06"
description = "Basics of using Ansible to manage Juniper devices"
title = "Junos/Ansible basics"
+++

Disclaimer: may be outdated

## Retrieve config

Using Galaxy module:

```
 - name: Retrieve the interface configuration using Galaxy module
      juniper_junos_config:
        retrieve: 'committed'
        filter: '<interfaces><interface><name>{{interface}}</name><unit><name>{{unit}}</name></unit></interface></interfaces>'
        diff: true
        check: false
        commit: false
        format: xml
      register: iface_info     
```

Using netconf_get module:

```
- name: Retrieve the interface configuration using netconf_get module
      netconf_get: 
        source: running
        filter: '<configuration><interfaces><interface><name>{{interface}}</name><unit><name>{{unit}}</name></unit></interface></interfaces></configuration>'
        # filter: '<configuration><interfaces><interface[name="ge-0/0/6"]></interface></interfaces></configuration>'
      register: iface_info
```

Note: Junos doesnt support xpath filtering in get-config operation