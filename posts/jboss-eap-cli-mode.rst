.. title: Jboss EAP CLI Mode
.. slug: jboss-eap-cli-mode
.. date: 2016-10-23 20:21:46 UTC+05:30
.. tags: jboss
.. category: tech
.. link: 
.. description: 
.. type: text
   
It was a hard time to figure out how to control jboss EAP 6 from cli mode, hence the lack of straight forward information.
So here I'm posting some of the things I learnt from the official documentation and other blogs together.

Enter to ``CLI`` mode
---------------------

Goto ``$JBOSS_HOME/bin`` directory, execute ``./jboss-cli.sh`` and you will get a prompt like ``[disconnected /]``. 
Then connect via management port.

   connect <ip>:<mgmt port>

   ex: connect 192.168.56.103:9999

And you will see how the prompt gets changed as soon as it got connected. Furthermore, to find out the management port, the 
easiest way is to add the offset to the default value.
``default+offset``. You may get the default value by opening
``$JBOSS_HOME/standalone/configuration/standalone.xml`` file. (in domain mode, it will be ``$JBOSS_HOME/domain/configuration/domain.xml`` file)

.. code-block:: xml
   
   <socket-binding-group name="standard-sockets" default-interface="public" port-offset="${jboss.socket.binding.port-offset:0}">
   <socket-binding name="management-native" interface="management" port="${jboss.management.native.port:9999}"/>
   <socket-binding name="management-http" interface="management" port="${jboss.management.http.port:9990}"/>
   <socket-binding name="management-https" interface="management" port="${jboss.management.https.port:9443}"/>



Create a datasource
-------------------

Creating a datasource from ``CLI`` mode is not straight forward. First, you need to add ``jdbc`` driver as a module and install it.
Shift to jboss ``CLI`` mode and execute,

.. code-block:: Bash

    module add --name=name_of_the_module --resource=jdbc_location --dependencies=list_of_dependencies

Once you have added the module, you need to install it as a driver,

.. code-block:: Bash
		
    /subsystem=datasources/jdbc-driver=jdbc_driver_name:add(driver-name=jdbc_driver_name,driver-module-name=module_name,driver-class-name=class_name)

The final part is to create a datasource.

.. code-block:: Bash
		
    data-source add --jndi-name=java:/jndi_name --name=dt_name --connection-url=jdbc_url --driver-name=jdbc_driver_name --user-name=username --password=password --enabled=true


Deploy an application
---------------------

In standalone mode, the application deployment can be done like this,

.. code-block:: Bash

    deploy path_to_the_app_with_file_name

In domain mode, you need to specify the server group.

.. code-block:: Bash

    deploy path_to_the_app_with_file_name --server-groups=main-server-group


Some useful ``CLI`` commands
----------------------------

To remove a datasource,

.. code-block:: Bash

    data-source remove --name=data_source_name

To remove a module,

.. code-block:: Bash

    module remove --name=module_name

To remove a driver,

.. code-block:: Bash

    /subsystem=datasources/jdbc-driver=driver_name:remove

List all resources,

.. code-block:: Bash

    /subsystem=datasources:read-resource(recursive=true)

List installed drivers,

.. code-block:: Bash

    /subsystem=datasources:installed-drivers-list

Check a datasource connection,

.. code-block:: Bash

    /subsystem=datasources/data-source=jndi_name:test-connection-in-pool

Undeploy an application (in standalone mode),

.. code-block:: Bash

    undeploy module_name

Undeploy an application (in domain mode),

.. code-block:: Bash

    undeploy module_name --all-relevant-server-groups

