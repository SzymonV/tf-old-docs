.. This work is licensed under the Creative Commons Attribution 4.0 International License.
   To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

=========================================================
Using contrailctl to Configure Services Within Containers
=========================================================

Starting with Contrail 4.0, some subsystems of Contrail are delivered as Docker containers. The ``contrailctl`` tool is a set of commands that enable a user to make some changes to the configuration file within a Contrail container.

-  `What is contrailctl?`_ 


-  `Command Operations`_ 

What is contrailctl?
--------------------

Starting with Contrail 4.0, some modules of the Contrail architecture have been grouped by function into Docker containers. Each container has an INI-based configuration file to maintain the specific configuration for that container. The ``contrailctl`` is a tool within the container that provides the user a simple command structure for provisioning and operating the Contrail services packaged in the container.

Because it is complex to provision and manage the various services within Contrail containers, the ``contrailctl`` tool helps configure the services in the container to be in sync with the container-specific configuration files.

The ``contrailctl`` tool is driven by the single INI-based configuration file per container, for example, the ``/etc/contrailctl/controller.conf`` for the controller container. Any state changes of the services within the container must be made according to the configuration in the ``contrailctl`` configuration file for that container. The ``contrailctl`` configuration files are available on each node at a default location of ``/etc/contrailctl/*.conf`` .

Any changes made to the configuration files in the node are available within the container.

Each Contrail container has a separate ``contrailctl`` configuration file, currently:

-  ``contrail-controller`` — ``/etc/contrailctl/controller.conf`` 


-  ``contrail-analytics`` — ``/etc/contrailctl/analytics.conf`` 


-  ``contrail-analyticsdb`` — ``/etc/contrailctl/analyticsdb.conf`` 


Sample container configuration files can be seen at

 https://github.com/Juniper/contrail-docker/tree/master/tools/python-contrailctl/examples/configs 



Command Operations
------------------

The ``contrailctl`` is used within the node that holds a container. It is used at startup to configure and start the services within the container. The user must connect to the container to run ``contrailctl`` , or use the following command syntax to run ``contrailctl`` :

``docker exec<container name or id>contrailctl<arguments>`` 

Example:

``docker exec controller contrailctl config sync -c controller -Fv`` 

The main function of the ``contrailctl`` is to ensure that the desired configurations for the services within a container are in sync with the ``contrailctl`` master configuration file within the container.


Command Syntax and Options
--------------------------
::

 $ contrailctl config sync -h
 usage: contrailctl config sync [-h] [-f CONFIG_FILE] -c
                                {controller,analyticsdb,analytics,agent,lb,kubemanager,mesosmanager}
                                [-F] [-t TAGS]

 optional arguments:
   -h, --help            show this help message and exit
   -f CONFIG_FILE, --config-file CONFIG_FILE
                         Master config file path
   -c {controller,analyticsdb,analytics,agent,lb,kubemanager,mesosmanager}, --component {controller,analyticsdb,analytics,agent,lb,kubemanager,mesosmanager}
                         Component[s] to be configured
   -F, --force           Whether to apply config forcibly
   -t TAGS, --tags TAGS  comma separated list of tags to runspecific set of
                         ansible code



Updating and Syncing Service Configurations Within the Container
----------------------------------------------------------------

You can update service configurations by editing the appropriate container configuration file and then syncing.

While starting the container, the ``contrailctl`` configurations are provided under ``/etc/contrailctl`` . During startup, ``contrailctl config sync`` runs to synchronize the configurations to the internal services.

If a user wants to add or change configurations, the user can edit the appropriate configuration file in ``/etc/contrailctl/`` and then manually run ``contrailctl config sync`` on that specific container.

Using ``contrailctl config sync`` synchronizes the entire configuration from the master configuration file in ``/etc/contrailctl`` to the service configurations within the container.



Syntax and Usage: config sync
-----------------------------

``contrailctl config sync [section] [param] [-f|--force]`` 

- Use the options ``section`` and ``parameter`` to restrict the data to be synced to a specific section and parameter.


- Use the optional ``force`` to perform an Ansible run, even if there is no configuration change to be synced.




Example: config sync
--------------------

In this example, the user wants to add a configuration "foo=bar" to the controller container after the container is started.

The following example shows the procedure to sync a configuration change within the controller container.


#. The user edits the ``/etc/contrailctl/controller.conf`` to add the desired configuration changes within the node that holds the container.



#. The user syncs the change to the services running within the container.

    ``$ docker exec<my controller>config sync -c controller -v`` 


**Related Documentation**

-  `Introduction to Containerized Contrail Modules`_ 

.. _Introduction to Containerized Contrail Modules: containers-overview.html


.. _https://github.com/Juniper/contrail-docker/tree/master/tools/python-contrailctl/examples/configs: https://github.com/Juniper/contrail-docker/tree/master/tools/python-contrailctl/examples/configs
