# Connecting to IBM WebSphere App Server

This page describes how to configure the WSO2 Micro Integrator with IBM WebSphere Application Server.

1. Set up IBM WebSphere Application Server according to the instructions provided by IBM.
2. Create a JMS queue (e.g., **samplequeue**) and a JMS connection factory (e.g., **QueueConnectionFactory** ) as described in the topics under [Setting Up JMS in IBM WebSphere Application Server](http://pic.dhe.ibm.com/infocenter/iisinfsv/v8r5/index.jsp?topic=%2Fcom.ibm.swg.im.iis.infoservdir.user.doc%2Ftopics%2Ft_isd_user_creating_jms_que_cx_fact.html) in the IBM documentation.
3. Copy the following libraries from `WEBSHPERE_HOME>/java/lib` directory to `MI_HOMEW/lib` directory.

    -   com.ibm.ws.runtime.jar
    -   com.ibm.ws.admin.client_7.0.0.jar
    -   com.ibm.ws.sib.client.thin.jms_7.0.0.jar
    -   com.ibm.ws.webservices.thinclient_7.0.0.jar
    -   bootstrap.jar

4. Add the following entries to the `MI_HOME/conf/etc/launch.ini` file.

    ```java
    javax.jms,\
    javax.rmi.CORBA,\
    ```
5. If you want the Micro Integrator to receive messages from an IBM Websphere app server, or to send messages to an IBM Websphere app server, you need to update the deployment.toml file with the relevant connection parameters.

    - Add the following configurations to enable the JMS listener with ActiveMQ connection parameters.
        ```toml
        [[transport.jms.listener]]
        name = "myTopicListener"
        parameter.initial_naming_factory = "com.ibm.websphere.naming.WsnInitialContextFactory"
        parameter.broker_name = "activemq" 
        parameter.provider_url = "iiop://localhost:2809"
        parameter.connection_factory_name = "QueueConnectionFactory"
        parameter.connection_factory_type = "queue"
        ```

    - Add the following configurations to enable the JMS sender with ActiveMQ connection parameters.
        ```toml
        [[transport.jms.sender]]
        name = "myTopicSender"
        parameter.initial_naming_factory = "com.ibm.websphere.naming.WsnInitialContextFactory"
        parameter.broker_name = "activemq"
        parameter.provider_url = "iiop://localhost:2809"
        parameter.connection_factory_name = "QueueConnectionFactory"
        parameter.connection_factory_type = "queue"
        ```
  
5. Start IBM WebSphere Application Server and WSO2 Micro Integrator.

You have now configured instances of IBM WebSphere Application Server and WSO2 Micro Integrator.
