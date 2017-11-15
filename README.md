VRF role
========

This role facilitates to configure the basics of virtual routing and forwarding (VRF) that helps in the partition of physical routers to multiple virtual routers. This role is abstracted for dellos9.

The vrf role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in OS connection variables or the *provider* dictionary.

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-vrf

Role variables
--------------

- Role is abstracted using the variable *ansible_net_os_name* that can take the dellos9 value
- If *dellos_cfg_generate* is set to true, the variable generates the role configuration commands in a file
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable
- Setting an empty value for any variable negates the corresponding configuration.
- Variables and values are case-sensitive

**dellos_vrf keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``vrfdetails`` | list              | Configures the list of VRF instances (see ``instances.*``)  | dellos9 |
| ``vrfdetails.vrf_name``      | string         | Specifies the VRF instance name (default is management)  | dellos9 |
| ``vrfdetails.vrf_id``      | integer (required)        | Configures the VRF ID for the corresponding vrf    | dellos9 |
| ``vrfdetails.description`` | string    | Configures a one line description for the VRF  | dellos9 |
| ``vrfdetails.state``       | string    | Deletes the VRF instance name if set to absent | dellos9 |
| ``vrfdetails.tagged_portname``      | list        | Specifies list of valid interface names | dellos9 |
| ``tagged_portname.port``   | string    | Specifies valid interface name | dellos9 |
| ``tagged_portname.state``  | string    | Deletes VRF association in the interface if set to absent | dellos9 |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Connection variables
--------------------

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories, or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``port``        | no       |            | Specifies the port used to build the connection to the remote device; if unspecified, the value defaults to 22 |
| ``username`` | no       |            | Specifies the username that authenticates the CLI login for connection to the remote device; if value is unspecified, the ANSIBLE_NET_USERNAME environment variable value is used |
| ``password``    | no       |            | Specifies the password that authenticates the connection to the remote device; if value is unspecified, the ANSIBLE_NET_PASSWORD environment variable value is used |
| ``authorize`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_NET_AUTHORIZE environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``auth_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if *authorize* is set to no, the key does not apply; if value is unspecified, the ANSIBLE_NET_AUTH_PASS environment variable value is used |
| ``provider`` | no       |            | Passes all connection arguments as a dictionary object; all constraints (such as required or choices) must be met either by individual arguments or values in this dictionary |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-vrf* role is built on modules included in the core Ansible code. These modules were added in ansible version 2.2.0

Example playbook
----------------

This example uses the *dellos-vrf* role to setup a VRF and associate it to an interface. It creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_net_os_name* variable with corresponding Dell EMC networking OS name.

When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default, the variable is set to false. It writes a  simple playbook that references the *dellos-vrf* role.

**Sample hosts file**
  
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

**Sample host_vars/leaf1**

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: XXXX
      password: XXXX
      authorize: yes
      authpass: XXXX
    build_dir: ../temp/dellos9
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


**Simple playbook to setup system - leaf.yaml**

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-vrf

**Run**

    ansible-playbook -i hosts leaf.yaml

(c) 2017 Dell EMC
