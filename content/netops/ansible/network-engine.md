+++
categories = ['ansible']
date = '2019-09-06'
description = 'Ansible network-engine module'
title = 'Network-engine module'
+++

```
    1 	---
    2 	 - name: Parser role
    3 	   hosts: ios
    4 	   roles:
    5 	     - "ansible-network.network-engine"
    6 	   tasks:
    7 	    - name: "Get router configuration"
    8 	      ios_command:
    9 	        commands: "show running-config | section ^vrf_definition"
   10 	      register: vrf_config
   11 	
   12 	    - name: DEBUG1
   13 	      debug:
   14 	       var: vrf_config.stdout[0]
   15 	
   16 	    - name: "Parse RT data"
   17 	      command_parser:
   18 	        file: "parsers/vrf.yml"
   19 	        content: "{{ vrf_config.stdout[0] }}"
   20 	    
   21 	    - name: DEBUG2
   22 	      debug:
   23 	       msg: "TEST {{item}}"
   24 	      loop: "{{vrf_data | dictsort }}"
   25 	
   26 	    
```

```
    1 	---
    2 	 - name: "Parser metadata"
    3 	   parser_metadata:
    4 	     version: 1.0
    5 	     command: "show running-config | section ^vrf definition"
    6 	     network_os: ios
    7 	
    8 	 - name: "Match each VRF"
    9 	   pattern_match:
   10 	      regex: "vrf\\s+definition"
   11 	      match_all: true
   12 	      match_greedy: true
   13 	   register: vrf_list
   14 	  #  export: yes
   15 	
   16 	 - name: "Match VRF components"
   17 	   pattern_group:
   18 	
   19 	     - name: "Parse VRF name"
   20 	       pattern_match:
   21 	         regex: "vrf\\s+definition\\s+(\\S+)"
   22 	         content: "{{ item }}"
   23 	       register: name
   24 	
   25 	     - name: "Parse imported RTs"
   26 	       pattern_match:
   27 	         regex: "route-target\\s+import\\s+(\\d+:\\d+)"
   28 	         content: "{{ item }}"
   29 	         match_all: true
   30 	       register: rti_matches
   31 	
   32 	     - name: "Parse exported RTs"
   33 	       pattern_match:
   34 	         regex: "route-target\\s+export\\s+(\\d+:\\d+)"
   35 	         content: "{{ item }}"
   36 	         match_all: true
   37 	       register: rte_matches
   38 	
   39 	   loop: "{{ vrf_list }}"
   40 	   register: vrf_components
   41 	   #export: yes
   42 	
   43 	 - name: "Create JSON structure"
   44 	   json_template:
   45 	     template:
   46 	       - key: "{{ item.name.matches[0] }}"
   47 	         object:
   48 	          - key: route_import
   49 	            value: "{{ item.rti_matches | map(attribute='matches') | list }}"
   50 	          - key: route_export
   51 	            value: "{{ item.rte_matches | map(attribute='matches') | list }}"
   52 	   loop: "{{ vrf_components }}"
   53 	   export: true
   54 	   export_as: dict
   55 	   register: vrf_data
```

```
    1 	cra@lablinux:~/workbench/network-engine tests$ ansible-playbook do_parsing.yml
    2 	
    3 	PLAY [Parser role] **************************************************************************************************************************************************************************************************************************************************
    4 	TASK [Get router configuration] *************************************************************************************************************************************************************************************************************************************ok: [CSR]
    5 	
    6 	TASK [DEBUG1] *******************************************************************************************************************************************************************************************************************************************************ok: [CSR] => {
    7 	    "vrf_config.stdout[0]": "vrf definition VPN-A\n rd 1.1.1.1:1\n route-target export 65000:1\n route-target export 65000:2\n route-target export 65000:5\n route-target import 65000:5\n route-target import 65000:7\nvrf definition VPN-B\n rd 1.1.1.1:2\n route-target export 65000:9\n route-target export 65000:7\n route-target export 65000:1\n route-target import 65000:22\n route-target import 65000:11"
    8 	}
    9 	
   10 	TASK [Parse RT data] ************************************************************************************************************************************************************************************************************************************************ok: [CSR]
   11 	
   12 	TASK [DEBUG2] *******************************************************************************************************************************************************************************************************************************************************ok: [CSR] => (item=[u'VPN-A', {u'route_export': [u'65000:1', u'65000:2', u'65000:5'], u'route_import': [u'65000:5', u'65000:7']}]) => {
   13 	    "msg": "TEST [u'VPN-A', {u'route_export': [u'65000:1', u'65000:2', u'65000:5'], u'route_import': [u'65000:5', u'65000:7']}]"
   14 	}
   15 	ok: [CSR] => (item=[u'VPN-B', {u'route_export': [u'65000:9', u'65000:7', u'65000:1'], u'route_import': [u'65000:22', u'65000:11']}]) => {
   16 	    "msg": "TEST [u'VPN-B', {u'route_export': [u'65000:9', u'65000:7', u'65000:1'], u'route_import': [u'65000:22', u'65000:11']}]"
   17 	}
   18 	
   19 	PLAY RECAP **********************************************************************************************************************************************************************************************************************************************************CSR                        : ok=4    changed=0    unreachable=0    failed=0
   20 	
   21 	cra@lablinux:~/workbench/network-engine tests$
   22 
```