VRF Role for Dell EMC Networking OS
====================================

This role facilitates to configure the basics of virtual routing and forwarding (VRF) that helps in the partition of physical routers to multiple virtual routers. This role is abstracted for dellos9.

Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-vrf
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

Role Variables
--------------

This role is abstracted using the variable ``ansible_net_os_name`` that can take the following value: dellos9.

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration.

The variables and its values are case-sensitive.

``dellos_vrf`` holds the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|-----------:|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vrfdetails | list              | Configures the list of VRF instances. See the following instances.* keys for each list item. |
| vrfdetails.vrf_name      | string         | Specifies the VRF instance name. The default setting is management.  |
| vrfdetails.vrf_id      | integer(required)        | Configures the VRF ID for the corresponding VRF.    |
| vrfdetails.description | string    | Configures a one line description for the VRF.  |
| vrfdetails.state       | string    | Removes the VRF instance name if state is set to absent. |
| vrfdetails.tagged_portname      | list        | Specifies list of valid interface names on dellos9 device.  |
| tagged_portname.port   | string    | Specifies valid dellos9 interface name. |
| tagged_portname.state  | string    | Removes VRF association in the interface if this state is set to absent. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```


Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible group_vars or host_vars directories, or in the playbook itself.

|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Specifies the host name or address for connecting to the remote device over the specified ``transport`` variable. The value of ``host`` is the destination address for the transport. |
|        port | no       |            | Specifies the port to use when building the connection to the remote device. This value applies to either acceptable value of ``transport``. The port value defaults to the appropriate transport common port if the key provides none (CLI=22, HTTP=80, HTTPS=443). |
|    username | no       |            | Configures the username that authenticates the connection to the remote device.  The value of ``username`` is used to authenticate the CLI login. If the key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password to use to authenticate the connection to the remote device. This is a common argument used for either acceptable value of ``transport``. If the key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If not specified, the device attempts to execute all commands in non-privileged mode. If the key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. |
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this argument does nothing. If the key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. The ``transport`` argument supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all if the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```
Dependencies
------------

The dellos-vrf role is built on modules included in the core Ansible code. These modules were added in ansible version 2.2.0

Example Playbook
----------------

The following example uses the dellos-vrf role to setup a VRF and associate it to an interface. This example also creates a ``hosts`` file with the switch, a corresponding ``host_vars`` file, and then a simple playbook that references the dellos-vrf role.

Sample ``hosts`` file:
  
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: XXXX
      password: XXXX
      authorize: yes
          authpass: XXXX
      transport: cli

    dellos_vrf:
        vrfdetails:
          - vrf_id: 1
            vrf_name: VLTi-KEEPALIVE
            description: VRF-to-support-Peer-Keepalive-Link
            state: present
            tagged_portname:
              - port: fortyGigE 1/7
                state: present
              - port:fortyGigE 1/8
                state: present


A simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-vrf

Then run with:

    ansible-playbook -i hosts leaf.yaml


License
--------

Copyright (c) 2016, Dell Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
                
