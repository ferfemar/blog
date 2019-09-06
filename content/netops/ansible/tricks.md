+++
categories = ["netops", "ansible"]
date = "2019-09-06"
description = "Ansible tips, tricks and notes"
title = "Ansible tips, tricks and notes"
+++

## Remove key from dictionary

Takes hostvars for the given inventory_hostname and reconstructs it without the groups key

```
- set_fact:
    dict: {}
 - set_fact:
    dict: "{{ dict | combine({item.key: item.value}) }}"
   when: item.key not in ['groups']
   with_dict: "{{hostvars[inventory_hostname]}}"
 - debug:
    msg: "{{dict}}"
```