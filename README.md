VRF role
========

This role facilitates to configure the basics of virtual routing and forwarding (VRF) that helps in the partition of physical routers to multiple virtual routers. This role is abstracted for dellos9.

The vrf role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in OS connection variables .

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-vrf

Role variables
--------------

- Role is abstracted using the variable *ansible_network_os* that can take the dellos9 value
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

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories, or inventory or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``ansible_host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``ansible_port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_PORT option is used; it defaults to 22 |
| ``ansible_ssh_user`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_USER environment variable value is used  |
| ``ansible_ssh_pass`` | no       |            | Specifies the password that authenticates the connection to the remote device.  |
| ``ansible_become`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_BECOME environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``ansible_become_method`` | no       | enable, sudo\*   | Instructs the module to allow the become method to be specified for handling privilege escalation; if value is unspecified, the ANSIBLE_BECOME_METHOD environment variable value is used |
| ``ansible_become_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``ansible_become`` is set to no this key is not applicable |
| ``ansible_network_os`` | yes      | dellos6/dellos9/dellos10, null\*  | Loads the correct terminal and cliconf plugins to communicate with the remote device |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-vrf* role is built on modules included in the core Ansible code. These modules were added in ansible version 2.2.0

Example playbook
----------------

This example uses the *dellos-vrf* role to setup a VRF and associate it to an interface. It creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_network_os* variable with corresponding Dell EMC networking OS name.

When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default, the variable is set to false. It writes a  simple playbook that references the *dellos-vrf* role.

**Sample hosts file**
  
    leaf1 ansible_host= <ip_address> 

**Sample host_vars/leaf1**

    hostname: leaf1
    ansible_become: yes
    ansible_become_method: xxxxx
    ansible_become_pass: xxxxx
    ansible_ssh_user: xxxxx
    ansible_ssh_pass: xxxxx
    ansible_network_os: dellos9
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

(c) 2017 Dell Inc. or its subsidiaries. All Rights Reserved.
