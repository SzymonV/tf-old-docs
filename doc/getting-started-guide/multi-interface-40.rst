.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

===================================================
Supporting Multiple Interfaces on Servers and Nodes
===================================================

This section describes how to set up and manage multiple interfaces.

-  `Support for Multiple Interfaces`_ 


-  `Server Interface Examples`_ 


-  `Interface Naming and Configuration Management`_ 




Support for Multiple Interfaces
-------------------------------

Servers and nodes with multiple interfaces should be deployed with exclusive management and control and data networks. In the case of multiple interfaces per server, the expectation is that the management network provides only management connectivity to the cluster, and the control and data network carries the control plane information and the guest traffic data.

Examples of control traffic include the following:

- XMPP traffic between the control nodes and the compute nodes.


- BGP protocol messages across the control nodes.


- Statistics, monitoring, and health check data collected by the analytics engine from different parts of the system.


In Contrail , control and data must share the same interface, configured in the ``testbed.py`` file in a section named ``control_data`` .



Number of cfgm Nodes Supported
------------------------------

The Contrail system can have any number of ``cfgm`` nodes.​



Uneven Number of Database Nodes Required
-----------------------------------------

In Contrail, Apache ZooKeeper resides on the database node. Because a ZooKeeper ensemble operates most effectively with an odd number of nodes, it is required to have an odd number (3, 5, 7, and so on) of database nodes in a Contrail system.



Support for VLAN Interfaces
---------------------------

A VLAN ID can also be specified in the ``server.json`` file under the ``network, interfaces`` section, similar to the following example: ​

::

 “network”: {
  “interfaces”: [
      {
         “name”: “vlan2003”,
         “type”  : “vlan”,
         “vlan”: “2003”,
         “parent_interface”: “bond0”,
         “ip_address": “ip-address/24”,
         “default_gateway”:  “default-gateway-ip-address”
        }
    ]
 }




Support for Bonding Options
---------------------------

​Contrail provides support for bond interface options.
The default bond interface options are:
``miimon=100, mode=802.3ad(lacp), xmit_hash_policy=layer3+4`` 
For Contrail 4.0 and later, in the provisioning file bond section, anything other than name and member are treated as a bond interface option, and provisioned as such. The following is an example:

::

 “network”: {
   “interfaces”: [
        name”: “bond0”,
          “type”  : “bond”,
          “bond_options” : {“miimon”: “100”, “mode”: “802.3ad”, “xmit_hash_policy”: “layer3+4”},
         “member_interfaces”: [“p20p1”, “p20p2”]
     },
 ],




Support for Static Route Options
--------------------------------

​Contrail provides support for adding static routes on target systems. This option is ideal for use cases in which a system has servers with multiple interfaces and has control data or management connections that span multiple networks.
The following shows static routes added in the server.json under the ‘network’ section.

::

 “network”: {
           "routes": [
               {
                  "gateway": "gateway-ip-address",
                  "interface": "enp129s0f0",
                  "netmask": "subnet-mask",
                  "network": "ip-address"
               },
              {
                 "gateway": "gateway-ip-address",
                 "interface": "enp129s0f1",
                 "netmask": "subnet-mask",
                 "network": "ip-address"
               }
             ]
          }





Server Interface Examples
-------------------------



In Contrail Release 1.10 and later, control and data are required to share the same interface. A set of servers can be deployed in any of the following combinations for management, control, and data:

- ``mgmt=control=data`` -- Single interface use case


- ``mgmt, control=data`` -- Exclusive management access, with control and data sharing a single network.


In Contrail, the following server interface combinations are not allowed:

- ``mgmt=control, data`` --Dual interfaces in Layer 3 mode, management and control shared on a single network


- ``mgmt, control, data`` –Complete exclusivity across management, control, and data traffic.




Interface Naming and Configuration Management
---------------------------------------------

On a standard Linux installation there is no guarantee that a physical interface will come up with the same name after a system reboot. Linux NetworkManager tries to accommodate this behavior by linking the interface configurations to the hardware addresses of the physical ports. However, Contrail avoids using hardware-based configuration files because this type of solution cannot scale when using remote provisioning and management techniques.

The Contrail alternative is a threefold interface-naming scheme based on *<bus, device, port (or function)>*. As an example, on a server operating system that typically assigns interface names such as **p4p0** and **p4p1** for onboard interfaces, the Contrail system assigns **p4p0p0** and **p4p0p1** , when using the optional **contrail-interface-name** package.

When the **contrail-interface-name** package is installed, it uses the threefold naming scheme to provide consistent interface naming after reboots. The **contrail-interface-name** package is installed by default when a Contrail ISO image is installed. If you are using an RPM-based installation, you should install the **contrail-interface-name** package before doing any network configuration.

If your system already has another mechanism for getting consistent interface names after a reboot, it is not necessary to install the **contrail-interface-name** package.

