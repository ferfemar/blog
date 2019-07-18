+++
categories = ["netops", "ansible"]
date = "2019-07-18"
description = "Working with timestamps in Ansible"
title = "Ansible timestamps"
head = "Ansible timestamps"
+++

# Timestamps in Ansible

Using facts (gather_facts cannot be False):

```
  tasks:
  - name: Ansible date fact example
    debug:
      var=ansible_date_time.date
```

Using local time via lookup:

```
- hosts: all
  tasks:
    - debug: msg="{{ lookup('pipe','date') }}"
```

or rather formatted:

```
- hosts: all
  tasks:
    - debug: msg="{{ lookup('pipe','date +%Y-%m-%d-%H-%M-%S') }}
```

Reference of other facts:

```
"date": "2017-07-27",
"day": "27",
"epoch": "1501173540",
"hour": "22",
"iso8601": "2017-07-27T16:39:00Z",
"iso8601_basic": "20170727T220900129160",
"iso8601_basic_short": "20170727T220900",
"iso8601_micro": "2017-07-27T16:39:00.129240Z",
"minute": "09",
"month": "07",
"second": "00",
"time": "22:09:00",
"tz": "IST",
"tz_offset": "+0530",
"weekday": "Thursday",
"weekday_number": "4",
"weeknumber": "30",
"year": "2017"
```

# Sources and References

http://www.mydailytutorials.com/working-date-timestamp-ansible/