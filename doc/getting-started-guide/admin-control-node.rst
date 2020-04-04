.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

=====================================
Configuring the Control Node with BGP
=====================================

An important task after a successful installation is to configure the control node with BGP. This procedure shows how to configure basic BGP peering between one or more virtual network controller control nodes and any external BGP speakers. External BGP speakers, such as Juniper Networks MX80 routers, are needed for connectivity to instances on the virtual network from an external infrastructure or a public network.

Before you begin, ensure that the following tasks are completed:

- The Contrail Controller base system image has been installed on all servers.


- The role-based services have been assigned and provisioned.


- IP connectivity has been verified between all nodes of the Contrail Controller.


- You can access the Contrail user interface at **http://nn.nn.nn.nn:8080** , where **nn.nn.nn.nn** is the IP address of the configuration node server that is running the **contrail-webui** service.


To configure BGP peering in the control node:


#. From the Contrail Controller module control node ( **http://nn.nn.nn.nn:8080** ), select **Configure > Infrastructure > BGP Routers** ; see `Figure 2`_ .

   .. _Figure 2: 

   *Figure 2* : Configure> Infrastructure > BGP Routers

   .. figure:: s042497.png

   A summary screen of the control nodes and BGP routers is displayed; see `Figure 3`_ .

   .. _Figure 3: 

   *Figure 3* : BGP Routers Summary

   .. figure:: s042498.png



#. (Optional) The global AS number is 64512 by default. To change the AS number, on the **BGP Router** summary screen click the gear wheel and select **Edit** . In the Edit BGP Router window enter the new number.

#. To create control nodes and BGP routers, on the **BGP Routers** summary screen, click the |s042494.png| icon. The **Create BGP Router** window is displayed; see `Figure 4`_ .

   .. _Figure 4: 

   *Figure 4* : Create BGP Router

   .. figure:: s042496.png



#. In the **Create BGP Router** window, click **BGP Router** to add a new BGP router or click **Control Node** to add control nodes.

   For each node you want to add, populate the fields with values for your system. See `Table 3`_ .

   .. _Table 3: 


   *Table 3* : Create BGP Router Fields

   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Field             | Description                                                                                           |
   +===================+=======================================================================================================+
   | Hostname          | Enter a name for the node being added.                                                                |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Vendor ID         | Required for external peers. Populate with a text identifier, for example, “MX-0”. (BGP peer only)    |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | IP Address        | The IP address of the node.                                                                           |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Router ID         | Enter the router ID.                                                                                  |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Autonomous System | Enter the AS number for the node. (BGP peer only)                                                     |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Address Families  | Enter the address family, for example, **inet-vpn**.                                                  |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Hold Time         | BGP session hold time. The default is 90 seconds; change if needed.                                   |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | BGP Port          | The default is 179; change if needed.                                                                 |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   |AuthenticationMode | Enable MD5 authentication if desired.                                                                 |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Authentication key| Enter the Authentication Key value.                                                                   |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Physical Router   | The type of the physical router.                                                                      |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   |Available Peers    | Displays peers currently available.                                                                   |
   +-------------------+-------------------------------------------------------------------------------------------------------+
   | Configured Peers  | Displays peers currently configured.                                                                  |
   +-------------------+-------------------------------------------------------------------------------------------------------+



#. Click **Save** to add each node that you create.

#. To configure an existing node as a peer, select it from the list in the **Available Peers** box, then click **>>** to move it into the **Configured Peers** box.

   Click **<<** to remove a node from the **Configured Peers** box.

#. You can check for peers by selecting **Monitor > Infrastructure > Control Nodes** ; see `Figure 5`_ .

   .. _Figure 5: 

   *Figure 5* : Control Nodes

   .. figure:: s042499.png

   In the **Control Nodes** window, click any hostname in the memory map to view its details; see `Figure 6`_ .

   .. _Figure 6: 

   *Figure 6* : Control Node Details

   .. figure:: s042500.png



#. Click the **Peers** tab to view the peers of a control node; see `Figure 7`_ .

   .. _Figure 7: 

   *Figure 7* : Control Node Peers Tab

   .. figure:: s042501.png


**Related Documentation**

-  `Creating a Virtual Network with Juniper Networks Contrail`_ 

-  `Creating a Virtual Network with OpenStack Contrail`_ 

.. _Creating a Virtual Network with Juniper Networks Contrail: creating-virtual-network-juniper-vnc.html

.. _Creating a Virtual Network with OpenStack Contrail: creating-virtual-network-vnc.html


.. |s042494.png| image:: s042494.png
