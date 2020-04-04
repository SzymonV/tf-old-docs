.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

===============================================================
Setting Up and Using a Simple Virtual Gateway with Contrail 4.0
===============================================================

-  `Introduction to the Simple Gateway`_ 


-  `How the Simple Gateway Works`_ 


-  `Setup Without Simple Gateway`_ 


-  `Setup With a Simple Gateway`_ 


-  `Simple Gateway Configuration Features`_ 


-  `Packet Flows with the Simple Gateway`_ 


-  `Packet Flow Process From the Virtual Network to the Public Network`_ 


-  `Packet Flow Process From the Public Network to the Virtual Network`_ 


-  `Methods for Configuring the Simple Gateway`_ 


-  `Using the vRouter Configuration File to Configure the Simple Gateway`_ 


-  `Using Thrift Messages to Dynamically Configure the Simple Gateway`_ 


-  `Common Issues with Simple Gateway Configuration`_ 



Introduction to the Simple Gateway
==================================

Every virtual network has a routing instance associated with it. The routing instance defines the network connectivity for the virtual machines in the virtual network. By default, the routing instance contains routes only for virtual machines spawned within the virtual network. Connectivity between virtual networks is controlled by defining network policies.

The public network is the IP fabric or the external networks across the IP fabric. The virtual networks do not have access to the public network, and a gateway is used to provide connectivity to the public network from a virtual network. In traditional deployments, a routing device such as a Juniper Networks MX Series router can act as a gateway.

The simple virtual gateway for Contrail is a restricted implementation of a gateway that can be used for experimental purposes, only. The simple gateway provides the Contrail virtual networks with access to the public network, and is represented as ``vgw`` .

The simple gateway is valid ONLY for a kernel vrouter, and *cannot* be used with any other flavor of vrouter, such as DPDK, SR-IOV, or the like. The simple gateway *cannot* be used in a production environment, it is for experimental uses only.


How the Simple Gateway Works
============================

The following sections illustrate how the simple gateway works, first, by showing a virtual network setup with no simple gateway, then illustrating the same setup with a simple gateway configured.


Setup Without Simple Gateway
============================

The following shows a virtual network setup when the simple gateway is not configured.

- A virtual network, ``default-domain:admin:net1`` , is configured with the subnet 192.168.1.0/24.


- The routing instance ``default-domain:admin:net1:net1`` is associated with the ``default-domain:admin:net1`` virtual network .


- A virtual machine with the 192.168.1.253 IP address is spawned in net1.


- A virtual machine is spawned on compute server 1.


- An interface, ``vhost0`` , is in the host OS of server 1 and is assigned the 10.1.1.1/24 IP address.


- The ``vhost0`` interface is added to the vRouter in the routing instance fabric.


- The simple gateway is not configured.



.. figure:: s042013.gif




Setup With a Simple Gateway
===========================

`Figure 14`_ shows a virtual network setup with the simple gateway configured for the ``default-domain:admin:net1`` virtual network.

The simple gateway configuration uses a gateway interface (vgw) to provide connectivity between the fabric routing instance and the ``default-domain:admin:net1`` virtual network.

`Figure 14`_ shows the packet flows between the fabric VRF and the ``default-domain:admin:net1`` virtual network.

In the diagram, routes marked with (*) are added by the simple gateway feature.

.. _Figure 14: 

*Figure 14* : Virtual Network Setup With a Simple Gateway

.. figure:: s042014.gif


Simple Gateway Configuration Features
=====================================

The simple gateway configuration has the following features.
- The simple gateway is configured for the ``default-domain:admin:net1`` virtual network.

 - The ``vgw`` gateway interface provides connectivity between the routing instance ``default-domain:admin:net1:net1`` and the fabric. ​


 - An IP address is not configured for the ``vgw`` gateway interface.



- The host OS is configured with the following:

 - Two INET interfaces are added to the host OS: ``vgw`` and ``vhost0``  


 - The host OS is not aware of the routing instances, so the ``vgw`` and ``vhost0`` interfaces are part of the same routing instance in the host OS.


 - The simple gateway adds the ``192.168.1.0/24`` route, pointing to the ``vgw`` interface, and that setup is added to the host OS. This route ensures that any packet destined to the virtual machine is sent to the vRouter on the ``vgw`` interface.



- The vRouter is configured with the following:

 - The routing instance named Fabric is created for the fabric network.


 - The interface ``vhost0`` is added to the routing instance Fabric.


 - The interface ``eth0`` , which is connected to the fabric network, is added to the routing instance named Fabric.


 - The simple gateway adds the ``192.168.1.0/24`` route to the ``vhost0`` interface. Consequently, packets destined to the ``default-domain:admin:net1`` virtual network are sent to the host OS.



- The ``default-domain:admin:net1:net1`` routing instance is created for the ``default-domain:admin:net1`` virtual network.

 - The ``vgw`` interface is added to the ``default-domain:admin:net1:net1`` routing instance.


 - The simple gateway adds a default route ( ``0.0.0.0/0`` ) that points to the ``vgw`` interface. Packets in the ``default-domain:admin:net1:net`` routing instance that match this route are sent to the host OS on the ``vgw`` interface. The host OS routes the packets to the fabric network over the ``vhost0`` interface.

Simple Gateway Restrictions
---------------------------

The following are restrictions of the simple gateway:
- A single compute node can have the simple gateway configured for multiple virtual networks, however, there cannot be overlapping subnets. The host OS does not support routing instances. Therefore, all gateway interfaces in the host OS are in the same routing instance and the subnets in the virtual networks must not overlap.

- Each virtual network can have a single simple gateway interface. ECMP is not supported.


Packet Flows with the Simple Gateway
====================================

The following sections describe the packet flow process when the simple gateway is configured on a Contrail system.

First, the packet flow process from the virtual network to the public network is described. Next, the packet flow process from the public network to the virtual network is described.


Packet Flow Process From the Virtual Network to the Public Network
==================================================================

The following describes the procedure used to move a packet from the virtual network (net1) to the public network.




#. A packet with a source IP address of ``192.168.1.253`` and a destination IP address of ``10.1.1.253`` comes from a virtual machine and is received by the vRouter on the ``tap0`` interface.



#. The ``tap0`` interface is in the ``default-domain:admin:net1:net1`` routing instance.



#. The route lookup for 10.1.1.253 in the ``default-domain:admin:net1:net1`` routing instance finds the default route pointing to the tap interface named ``vgw`` .



#. The vRouter transmits the packet toward the ``vgw`` interface and it is received by the networking stack of the host OS.



#. The host OS performs forwarding based on its routing table and forwards the packet on the ``vhost0`` interface.



#. Packets transmitted on the ``vhost0`` interface are received by the vRouter.



#. The ``vhost0`` interface is added to the ``Fabric`` routing instance.



#. The routing table for 10.1.1.253 in the ``Fabric`` routing instance indicates that the packet is to be transmitted on the ``eth0`` interface.



#. The vRouter transmits the packet on the ``eth0`` interface.



#. The 10.1.1.253 host on the ``Fabric`` routing instance receives the packet.



Packet Flow Process From the Public Network to the Virtual Network
==================================================================

The following describes the procedure used to move a packet from the public network to the virtual network (net1).




#. A packet with a source IP address of ``10.1.1.253`` and a destination IP address of ``192.168.1.253`` coming from the public network is received on the ``eth0`` interface.



#. The ``tap0`` interface is in the ``default-domain:admin:net1:net1`` routing instance.



#. The vRouter receives the packet from the ``eth0`` interface in the ``Fabric`` routing instance.



#. The route lookup for 192.168.1.253 in the ``Fabric`` routing instance points to the interface vhost0.



#. The vRouter transmits the packet on the ``vhost0`` interface and it is received by the networking stack of the host OS.



#. The host OS performs forwarding according to its routing table and forwards the packet on the ``vgw`` interface.



#. The vRouter receives the packet on the ``vgw`` interface into the routing instance default-domain:admin:net1:net1.



#. The route lookup for 192.168.1.253 in the ``default-domain:admin:net1:net1`` routing instance points to the ``tap0`` interface.



#. The vRouter transmits the packet on the ``tap0`` interface.



#. The virtual machine receives the packet destined to 192.168.1.253.



Methods for Configuring the Simple Gateway
==========================================

There are different methods that can be used to configure the simple gateway. Each of the methods is described in the following sections.


Using the vRouter Configuration File to Configure the Simple Gateway
====================================================================

Another way to enable a simple gateway is to configure one or more ``vgw`` interfaces within the ``contrail-vrouter-agent.conf`` file.

Any changes made in this file for simple gateway configuration are implemented upon the next restart of the vRouter agent. To configure the simple gateway in the ``contrail-vrouter-agent.conf`` file, each simple gateway interface uses the following parameters:

-  ``interface=vgwxx`` — Simple gateway interface name.


-  ``routing_instance=default-domain:admin:public xx:public xx`` — Name of the routing instance for which the simple gateway is being configured.


-  ``ip_block=1.1.1.0/24`` — List of the subnet addresses allocated for the virtual network. Routes within this subnet are added to both the host OS and routing instance for the fabric instance. Represent multiple subnets in the list by separating each with a space.


-  ``routes=10.10.10.1/24 11.11.11.1/24`` — List of subnets in the public network that are reachable from the virtual network. Routes within this subnet are added to the routing instance configured for the ``vgw`` interface. Represent multiple subnets in the list by separating each with a space.



Using Thrift Messages to Dynamically Configure the Simple Gateway
=================================================================

Another way to configure the simple gateway is to dynamically send create and delete thrift messages to the vrouter agent.

Starting with Contrail Release 1.10 and greater, the following thrift messages are available:

-  ``AddVirtualGateway`` —add a virtual gateway


-  ``DeleteVirtualGateway`` —delete a virtual gateway


-  ``ConnectForVirtualGateway`` —allows audit of the virtual gateway configuration by stateful clients. Upon a new ``ConnectForVirtualGateway`` request, one minute is allowed for the configuration to be redone. Any older virtual gateway configuration remaining after this time is deleted.


-  `How to Dynamically Create a Virtual Gateway`_ 


-  `How to Dynamically Delete a Virtual Gateway`_ 


-  `Using Devstack to Configure the Simple Gateway`_ 



How to Dynamically Create a Virtual Gateway
-------------------------------------------

To dynamically create a simple virtual gateway, you run a script on the compute node where the virtual gateway is being created.
When run, the script does the following:



#. Enables forwarding on the node.



#. Creates the required interface.



#. Adds the interface to the vRouter.



#. Adds required routes to the host OS.



#. Sends the ``AddVirtualGateway`` thrift message to the vRouter agent telling it to create the virtual gateway.



Example: Dynamically Create a Virtual Gateway
---------------------------------------------

The following procedure dynamically creates the ``vgw1`` interface, with ``20.30.40.0/24`` and ``30.40.50.0/24`` subnets in the ``default-domain:admin:vn1:vn1`` VRF.

#. Set the ``PYTHONPATH`` variable to the location of the ``InstanceService.py`` and ``types.py`` files, for example:

   ``export PYTHONPATH=/usr/lib/python2.7/dist-packages/nova_contrail_vif/gen_py/instance_service`` 
   ``export PYTHONPATH=/usr/lib/python2.6/site-packages/contrail_vrouter_api/gen_py/instance_service`` 


#. Run the virtual gateway ``provision`` command with the ``oper create`` option.
   Use the ``subnets`` option to specify the subnets defined for virtual network vn1.
   Use the ``routes`` option to specify the routes in the public network that are injected into vn1.
   In the following example, the virtual machines in ``vn1`` can access subnets ``8.8.8.0/24`` and ``9.9.9.0/24`` in the public network:
   python /opt/contrail/utils/provision_vgw_interface.py --oper create --interface vgw1 --subnets 20.30.40.0/24 30.40.50.0/24 --routes 8.8.8.0/24 9.9.9.0/24 --vrf default-domain:admin:vn1:vn1


How to Dynamically Delete a Virtual Gateway
-------------------------------------------

To dynamically delete a virtual gateway, run a script on the compute node where the virtual gateway is.
When run, the script does the following:

#. Sends the ``DeleteVirtualGateway`` thrift message to the vRouter agent. Tell it to delete the virtual gateway.



#. Deletes the virtual gateway interface from the vRouter.



#. Deletes the virtual gateway routes that were added in the host OS when the virtual gateway was created.



Example: Dynamically Create a Virtual Gateway
---------------------------------------------

The following procedure dynamically deletes the ``vgw1`` interface. It also deletes the ``20.30.40.0/24`` and ``30.40.50.0/24`` subnets in the ``default-domain:admin:vn1:vn1`` VRF .

#. Set the ``PYTHONPATH`` variable to the location of the ``InstanceService.py`` and ``types.py`` files, for example:
   ``export PYTHONPATH=/usr/lib/python2.7/dist-packages/nova_contrail_vif/gen_py/instance_service`` 
   ``export PYTHONPATH=/usr/lib/python2.6/site-packages/contrail_vrouter_api/gen_py/instance_service`` 


#. Run the virtual gateway  provisioncommand with the  oper deleteoption.
     python /opt/contrail/utils/provision_vgw_interface.py --oper delete --interface vgw1 --subnets 20.30.40.0/24 30.40.50.0/24 --routes 8.8.8.0/24 9.9.9.0/24


#. (optional) If you are using a stateful client, send the ``ConnectForVirtualGateway`` thrift message to the vRouter agent when the client starts.


.. note:: If the vRouter agent restarts or if the compute node reboots, it is expected that the client reconfigures again.



Using Devstack to Configure the Simple Gateway
----------------------------------------------

Another way to configure the simple gateway is to set configuration parameters in the devstack ``localrc`` file.
The following parameters are available:
-  ``CONTRAIL_VGW_PUBLIC_NETWORK`` — The name of the routing instance for which the simple gateway is being configured.


-  ``CONTRAIL_VGW_PUBLIC_SUBNET`` — A list of subnet addresses allocated for the virtual network. Routes containing these addresses are added to both the host OS and the routing instance for the fabric. List multiple subnets by separating each with a space.


-  ``CONTRAIL_VGW_INTERFACE`` — A list of subnets in the public network that are reachable from the virtual network. Routes containing these subnets are added to the routing instance configured for the simple gateway. List multiple subnets by separating each with a space.


This method can only add the default route ``0.0.0.0/0`` into the routing instance specified in the ``CONTRAIL_VGW_PUBLIC_NETWORK`` option.

Example: Devstack Configuration for Simple Gateway
--------------------------------------------------

Add the following lines in the ``localrc`` file for ``stack.sh:`` 

::

 CONTRAIL_VGW_INTERFACE=vgw1

 CONTRAIL_VGW_PUBLIC_SUBNET=192.168.1.0/24

 CONTRAIL_VGW_PUBLIC_NETWORK=default-domain:admin:net1:net1


.. note:: This method can only add the ``0.0.0.0/0`` default route into the routing instance specified in the ``CONTRAIL_VGW_PUBLIC_NETWORK`` option.




Common Issues with Simple Gateway Configuration
===============================================

The following are common problems you might encounter when configuring a simple gateway.

- Packets from the external network are not reaching the compute node.

The devices in the fabric network must be configured with static routes for the IP addresses defined in the public subnet (192.168.1.0/24 in the example) to reach the compute node that is running as a simple gateway.


- Packets are reaching the compute node, but are not routed from the host OS to the virtual machine.

Check to see if the ``firewall_driver`` in the ``/etc/nova/nova.conf`` file is set to ``nova.virt.libvirt.firewall.IptablesFirewallDriver`` , which enables IPTables. IPTables can discard packets.

Resolutions include disabling IPTables during runtime or setting the ``firewall_driver`` in the ``localrc`` file: ``LIBVIRT_FIREWALL_DRIVER=nova.virt.firewall.NoopFirewallDriver``