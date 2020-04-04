.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

=======================================
Monitor > Infrastructure > Config Nodes
=======================================

Select **Monitor > Infrastructure > Config Nodes** to view the information about the system config nodes.

-  `Monitor Config Nodes`_ 


-  `Monitor Individual Config Node Details`_ 


-  `Monitor Individual Config Node Console`_ 



Monitor Config Nodes
====================

Select **Monitor > Infrastructure > Config Nodes** to view a summary of activities for the analytics nodes. See `Figure 95`_ .

.. _Figure 95: 

*Figure 95* : Config Nodes Summary

.. figure:: s041557.gif

`Table 38`_ describes the fields in the Config Nodes summary.

.. _Table 38: 


*Table 38* : Config Nodes Summary Fields

+-----------------------------------+-----------------------------------+
| Field                             | Description                       |
+===================================+===================================+
| **Host name**                     | The name of this node.            |
+-----------------------------------+-----------------------------------+
| **IP address**                    | The IP address of this node.      |
+-----------------------------------+-----------------------------------+
| **Version**                       | The version of software installed |
|                                   | on the system.                    |
+-----------------------------------+-----------------------------------+
| **Status**                        | The current operational status of |
|                                   | the node — Up or Down — and the   |
|                                   | length of time it is in that      |
|                                   | state.                            |
+-----------------------------------+-----------------------------------+
| **CPU (%)**                       | The average CPU percentage usage  |
|                                   | for this node.                    |
+-----------------------------------+-----------------------------------+
| **Memory**                        | The average memory usage for this |
|                                   | node.                             |
+-----------------------------------+-----------------------------------+


Monitor Individual Config Node Details
======================================

Click the name of any config node displayed on the config nodes summary to view the **Details** tab for that node; see `Figure 96`_ .

.. _Figure 96: 

*Figure 96* : Individual Config Nodes— Details Tab

.. figure:: s041558.gif

`Table 39`_ describes the fields on the Details screen.

.. _Table 39: 


*Table 39* : Individual Config Nodes— Details Tab Fields

+-----------------------------------+-----------------------------------+
| Field                             | Description                       |
+===================================+===================================+
| **Hostname**                      | The name of the config node.      |
+-----------------------------------+-----------------------------------+
| **IP Address**                    | The IP address of this node.      |
+-----------------------------------+-----------------------------------+
| **Version**                       | The installed version of the      |
|                                   | software.                         |
+-----------------------------------+-----------------------------------+
| **Overall Node Status**           | The current operational status of |
|                                   | the node — Up or Down — and the   |
|                                   | length of time it is in this      |
|                                   | state.                            |
+-----------------------------------+-----------------------------------+
| **Processes**                     | The current operational status of |
|                                   | the processes associated with the |
|                                   | config node, including AI Server, |
|                                   | Schema Transformer, Service       |
|                                   | Monitor, and the like.            |
+-----------------------------------+-----------------------------------+
| **Analytics Node**                | The analytics node associated     |
|                                   | with this node.                   |
+-----------------------------------+-----------------------------------+
| **CPU (%)**                       | The average CPU percentage usage  |
|                                   | for this node.                    |
+-----------------------------------+-----------------------------------+
| **Memory**                        | The average memory usage by this  |
|                                   | node.                             |
+-----------------------------------+-----------------------------------+


Monitor Individual Config Node Console
======================================

Click the **Console** tab for an individual config node to display system logging information for a defined time period. See `Figure 97`_ .

.. _Figure 97: 

*Figure 97* : Individual Config Node—Console Tab

.. figure:: s041565.gif

See `Table 40`_ for descriptions of the fields on the **Console** tab screen.

.. _Table 40: 


*Table 40* : Individual Config Node-Console Tab Fields

+-----------------------------------+-----------------------------------+
| Field                             | Description                       |
+===================================+===================================+
| **Time Range**                    | Select a timeframe for which to   |
|                                   | review logging information as     |
|                                   | sent to the console. Use the drop |
|                                   | down calendar in the fields From  |
|                                   | Time and To Time to select the    |
|                                   | date and times to include in the  |
|                                   | time range for viewing.           |
+-----------------------------------+-----------------------------------+
| **Log Category**                  | Select from the drop down menu a  |
|                                   | log category to display. The      |
|                                   | option to view All is also        |
|                                   | available.                        |
+-----------------------------------+-----------------------------------+
| **Log Type**                      | Select a log type to display.     |
+-----------------------------------+-----------------------------------+
| **Log Level**                     | Select a log severity level to    |
|                                   | display:                          |
+-----------------------------------+-----------------------------------+
| **Limit**                         | Select from a list an amount to   |
|                                   | limit the number of messages      |
|                                   | displayed:                        |
|                                   |                                   |
|                                   | -  All                            |
|                                   | -  Limit 10 messages              |
|                                   | -  Limit 50 messages              |
|                                   | -  Limit 100 messages             |
|                                   | -  Limit 200 messages             |
|                                   | -  Limit 500 messages             |
+-----------------------------------+-----------------------------------+
| **Keywords**                      | Enter any key words by which to   |
|                                   | filter the log messages           |
|                                   | displayed.                        |
+-----------------------------------+-----------------------------------+
| **Auto Refresh**                  | Click the check box to            |
|                                   | automatically refresh the display |
|                                   | if more messages occur.           |
+-----------------------------------+-----------------------------------+
| **Display Logs**                  | Click this button to refresh the  |
|                                   | display if you change the display |
|                                   | criteria.                         |
+-----------------------------------+-----------------------------------+
| **Reset**                         | Click this button to clear any    |
|                                   | selected display criteria and     |
|                                   | reset all criteria to their       |
|                                   | default settings.                 |
+-----------------------------------+-----------------------------------+

