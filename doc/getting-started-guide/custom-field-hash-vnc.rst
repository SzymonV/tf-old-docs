.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

=======================================================
Customized Hash Field Selection for ECMP Load Balancing
=======================================================



Overview: Custom Hash Feature
-----------------------------

Starting with Contrail Release 3.0, it is possible to configure the set of fields used to hash upon during equal-cost multipath (ECMP) load balancing.

Earlier versions of Contrail had this set of fields fixed to the standard 5-tuple set of: source L3 address, destination L3 address, L4 protocol, L4 SourcePort, and L4 DestinationPort.

With the custom hash feature, users can configure an exact subset of fields to hash upon when choosing the forwarding path among a set of eligible ECMP candidates.

The custom hash configuration can be applied in the following ways:

- globally


- per virtual network (VN)


- per virtual network interface (VNI)


VNI configurations take precedence over VN configurations, and VN configurations take precedence over global level configuration (if present).

Custom hash is useful whenever packets originating from a particular source and addressed to a particular destination must go through the same set of service instances during transit. This might be required if source, destination, or transit nodes maintain a certain state based on the flow, and the state behavior could also be used for subsequent new flowsl, between the same pair of source and destination addresses. In such cases, subsequent flows must follow the same set of service nodes followed by the initial flow.

You can use the Contrail UI to identify specific fields in the network upon which to hash at the **Configure > Networking > Network, Create Network** window, in the **ECMP Hashing Fields** section as shown in the following figure.


.. figure:: S018553.png

If the hashing fields are configured for a virtual network, all traffic destined to that VN will be subject to the customized hash field selection during forwarding over ECMP paths by vRouters. This may not be desirable in all cases, as it could potentially skew all traffic to the destination network over a smaller set of paths across the IP fabric.

A more practical scenario is one in which flows between a source and destination must go through the same service instance in between, where one could configure customized ECMP fields for the virtual machine interface (VMI) of the service instance. Then, each service chain route originating from that VMI would get the desired ECMP field selection applied as its path attribute, and eventually get propagated to the ingress vRouter node. See the following example.


.. figure:: s018740.png



Using ECMP Hash Fields Selection
--------------------------------

Custom hash fields selection is most useful in scenarios where multiple ECMP paths exist for a destination. Typically, the multiple ECMP paths point to ingress service instance nodes, which could be running anywhere in the Contrail cloud.



Configuring ECMP Hash Fields Over Service Chains
------------------------------------------------

Use the following steps to create customized hash fields with ECMP over service chains.


#. Create the virtual networks needed to interconnect using service chaining, with ECMP load-balancing.



#. Create a service template and enable scaling.



#. Create a service instance, and using the service template, configure by selecting:

   - the desired number of instances for scale-out


   - the left and right virtual network to connect


   - the shared address space, to make sure that instantiated services come up with the same IP address for left and right, respectively


   This configuration enables ECMP among all those service instances during forwarding.



#. Create a policy, then select the service instance previously created and apply the policy to to the desired VMIs or VNs.



#. After the service VMs are instantiated, the ports of the left and right interfaces are available for further configuration. At the Contrail UI Ports section under Networking, select the left port (VMI) of the service instance and apply the desired ECMP hash field configuration.


.. note:: Currently the ECMP field selection configuration for the service instance left or right interface must be applied by using the Ports (VMIs) section under Networking and explicitly configuring the ECMP fields selection for each of the instantiated service instances' VMIs. This must be done for all service interfaces of the group, to ensure the end result is as expected, because the load balance attribute of only the best path is carried over to the ingress vRouter. If the load balance attribute is not configured, it is not propagated to the ingress vRouter, even if other paths have that configuration.




When the configuration is finished, the vRouters get programmed with routing tables with the ECMP paths to the various service instances. The vRouters are also programmed with the desired ECMP hash fields to be used during load balancing of the traffic.



Sample Flows
------------

This section provides sample flows with and without ECMP custom hash field selection.



Sample Traffic Flow Path Without Custom ECMP Hash Fields
--------------------------------------------------------

The following is an example of a traffic flow path without using a customized ECMP hash fields selection configuration. The flow is configured with standard 5-tuple flow fields.
::

 tcpdump -i eth0 'port 1023 and tcp[tcpflags] & (tcp-syn) != 0 and tcp[tcpflags] & (tcp-ack) == 0'
 tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
 listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
 14:55:10.115122 IP 2.2.2.5.18337 > 2.2.2.100.1023: Flags [S], seq 2276852196, win 29200, options [mss 1398,sackOK,TS val 25208882 ecr 0,nop,wscale 7], length 0
 14:55:10.132753 IP 2.2.2.4.21193 > 2.2.2.100.1023: Flags [S], seq 4161487314, win 29200, options [mss 1398,sackOK,TS val 25208886 ecr 0,nop,wscale 7], length 0
 14:55:10.152053 IP 2.2.2.5.24230 > 2.2.2.100.1023: Flags [S], seq 2466454857, win 29200, options [mss 1398,sackOK,TS val 25208892 ecr 0,nop,wscale 7], length 0
 14:55:11.146029 IP 2.2.2.5.24230 > 2.2.2.100.1023: Flags [S], seq 2466454857, win 29200, options [mss 1398,sackOK,TS val 25209142 ecr 0,nop,wscale 7], length 0
 14:55:13.147616 IP 2.2.2.5.24230 > 2.2.2.100.1023: Flags [S], seq 2466454857, win 29200, options [mss 1398,sackOK,TS val 25209643 ecr 0,nop,wscale 7], length 0
 14:55:13.164367 IP 2.2.2.3.25582 > 2.2.2.100.1023: Flags [S], seq 2259034580, win 29200, options [mss 1398,sackOK,TS val 25209644 ecr 0,nop,wscale 7], length 0
 14:55:13.179939 IP 2.2.2.5.24895 > 2.2.2.100.1023: Flags [S], seq 2174031724, win 29200, options [mss 1398,sackOK,TS val 25209648 ecr 0,nop,wscale 7], length 0
 14:55:14.168282 IP 2.2.2.5.24895 > 2.2.2.100.1023: Flags [S], seq 2174031724, win 29200, options [mss 1398,sackOK,TS val 25209898 ecr 0,nop,wscale 7], length 0
 14:55:16.172384 IP 2.2.2.5.24895 > 2.2.2.100.1023: Flags [S], seq 2174031724, win 29200, options [mss 1398,sackOK,TS val 25210399 ecr 0,nop,wscale 7], length 0
 14:55:16.189864 IP 2.2.2.5.22952 > 2.2.2.100.1023: Flags [S], seq 3099816842, win 29200, options [mss 1398,sackOK,TS val 25210401 ecr 0,nop,wscale 7], length 0
 14:55:16.205142 IP 2.2.2.4.16487 > 2.2.2.100.1023: Flags [S], seq 3961114202, win 29200, options [mss 1398,sackOK,TS val 25210405 ecr 0,nop,wscale 7], length 0
 14:55:17.196763 IP 2.2.2.4.16487 > 2.2.2.100.1023: Flags [S], seq 3961114202, win 29200, options [mss 1398,sackOK,TS val 25210655 ecr 0,nop,wscale 7], length 0
 14:55:19.200623 IP 2.2.2.4.16487 > 2.2.2.100.1023: Flags [S], seq 3961114202, win 29200, options [mss 1398,sackOK,TS val 25211156 ecr 0,nop,wscale 7], length 0
 14:55:19.215809 IP 2.2.2.3.18914 > 2.2.2.100.1023: Flags [S], seq 3157557440, win 29200, options [mss 1398,sackOK,TS val 25211158 ecr 0,nop,wscale 7], length 0
 14:55:19.228405 IP 2.2.2.7.15569 > 2.2.2.100.1023: Flags [S], seq 3850648420, win 29200, options [mss 1398,sackOK,TS val 25211161 ecr 0,nop,wscale 7], length 0
 14:55:20.223482 IP 2.2.2.7.15569 > 2.2.2.100.1023: Flags [S], seq 3850648420, win 29200, options [mss 1398,sackOK,TS val 25211412 ecr 0,nop,wscale 7], length 0
 14:55:22.232068 IP 2.2.2.7.15569 > 2.2.2.100.1023: Flags [S], seq 3850648420, win 29200, options [mss 1398,sackOK,TS val 25211913 ecr 0,nop,wscale 7], length 0
 14:55:22.247325 IP 2.2.2.4.28388 > 2.2.2.100.1023: Flags [S], seq 3609240658, win 29200, options [mss 1398,sackOK,TS val 25211915 ecr 0,nop,wscale 7], length 0



Sample Traffic Flow Path With Custom ECMP Hash Fields
-----------------------------------------------------

The following is an example of a traffic flow path using a customized ECMP hash fields selection configuration, for ``source-ip`` and ``destination-ip`` only.
::

 tcpdump -i eth0 'port 1023 and tcp[tcpflags] & (tcp-syn) != 0 and tcp[tcpflags] & (tcp-ack) == 0'
 tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
 listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
 15:57:18.680853 IP 2.2.2.4.21718 > 2.2.2.100.1023: Flags [S], seq 2052086108, win 29200, options [mss 1398,sackOK,TS val 26141024 ecr 0,nop,wscale 7], length 0
 15:57:18.696114 IP 2.2.2.4.13585 > 2.2.2.100.1023: Flags [S], seq 2039627277, win 29200, options [mss 1398,sackOK,TS val 26141028 ecr 0,nop,wscale 7], length 0
 15:57:18.714846 IP 2.2.2.4.16414 > 2.2.2.100.1023: Flags [S], seq 3252526560, win 29200, options [mss 1398,sackOK,TS val 26141033 ecr 0,nop,wscale 7], length 0
 15:57:18.731281 IP 2.2.2.4.32499 > 2.2.2.100.1023: Flags [S], seq 1389133175, win 29200, options [mss 1398,sackOK,TS val 26141037 ecr 0,nop,wscale 7], length 0
 15:57:18.747051 IP 2.2.2.4.6081 > 2.2.2.100.1023: Flags [S], seq 427936299, win 29200, options [mss 1398,sackOK,TS val 26141041 ecr 0,nop,wscale 7], length 0
 15:57:19.740204 IP 2.2.2.4.6081 > 2.2.2.100.1023: Flags [S], seq 427936299, win 29200, options [mss 1398,sackOK,TS val 26141291 ecr 0,nop,wscale 7], length 0
 15:57:21.743951 IP 2.2.2.4.6081 > 2.2.2.100.1023: Flags [S], seq 427936299, win 29200, options [mss 1398,sackOK,TS val 26141792 ecr 0,nop,wscale 7], length 0
 15:57:21.758532 IP 2.2.2.4.13800 > 2.2.2.100.1023: Flags [S], seq 3020971712, win 29200, options [mss 1398,sackOK,TS val 26141794 ecr 0,nop,wscale 7], length 0
 15:57:21.772646 IP 2.2.2.4.23894 > 2.2.2.100.1023: Flags [S], seq 3373734307, win 29200, options [mss 1398,sackOK,TS val 26141797 ecr 0,nop,wscale 7], length 0
 15:57:22.764469 IP 2.2.2.4.23894 > 2.2.2.100.1023: Flags [S], seq 3373734307, win 29200, options [mss 1398,sackOK,TS val 26142047 ecr 0,nop,wscale 7], length 0
 15:57:24.768511 IP 2.2.2.4.23894 > 2.2.2.100.1023: Flags [S], seq 3373734307, win 29200, options [mss 1398,sackOK,TS val 26142548 ecr 0,nop,wscale 7], length 0
 15:57:24.784119 IP 2.2.2.4.21858 > 2.2.2.100.1023: Flags [S], seq 2212369297, win 29200, options [mss 1398,sackOK,TS val 26142550 ecr 0,nop,wscale 7], length 0
 15:57:24.797149 IP 2.2.2.4.29440 > 2.2.2.100.1023: Flags [S], seq 2007897735, win 29200, options [mss 1398,sackOK,TS val 26142554 ecr 0,nop,wscale 7], length 0
 15:57:25.792816 IP 2.2.2.4.29440 > 2.2.2.100.1023: Flags [S], seq 2007897735, win 29200, options [mss 1398,sackOK,TS val 26142804 ecr 0,nop,wscale 7], length 0
 15:57:27.797538 IP 2.2.2.4.29440 > 2.2.2.100.1023: Flags [S], seq 2007897735, win 29200, options [mss 1398,sackOK,TS val 26143305 ecr 0,nop,wscale 7], length 0
 15:57:27.814002 IP 2.2.2.4.23452 > 2.2.2.100.1023: Flags [S], seq 1659332655, win 29200, options [mss 1398,sackOK,TS val 26143307 ecr 0,nop,wscale 7], length 0

