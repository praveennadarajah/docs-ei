# Connecting to WSO2 MB

This section describes how to configure WSO2 Micro Integrator to connect with WSO2 Message Broker.

## Setting up the Micro Integrator with WSO2 MB

1.  If you want the Micro Integrator to receive messages from a WSO2 MB instance, or to send messages to a WSO2 MB instance, you need to update the deployment.toml file with the relevant connection parameters.

    - Add the following configurations to enable the JMS listener with WSO2 MB connection parameters.
        ```toml
        [[transport.jms.listener]]
        name = "myQueueListener"
        parameter.initial_naming_factory = "org.wso2.andes.jndi.PropertiesFileInitialContextFactory"
        parameter.broker_name = "wso2mb" 
        parameter.provider_url = "conf/jndi.properties"
        parameter.connection_factory_name = "QueueConnectionFactory"
        parameter.connection_factory_type = "queue"
        parameter.cache_level = "consumer"
        ```

    - Add the following configurations to enable the JMS sender with WSO2 MB connection parameters.
        ```toml
        [[transport.jms.sender]]
        name = "myQueueSender"
        parameter.initial_naming_factory = "org.wso2.andes.jndi.PropertiesFileInitialContextFactory"
        parameter.broker_name = "wso2mb"
        parameter.provider_url = "conf/jndi.properties"
        parameter.connection_factory_name = "QueueConnectionFactory"
        parameter.connection_factory_type = "queue"
        parameter.cache_level = "producer"
        ```

3.  Open the  `MI_HOMEconf/jndi.properties` file and make a reference to the running Broker profile as specified below:
    -   Use **carbon** as the virtual host.
    -   Define a queue named `JMSMS`.
    -   Comment out the topic since it is not required in this scenario. However, in order to avoid getting the `javax.naming.NameNotFoundException:TopicConnectionFactory` exception during server startup, make a reference to the Broker profile from the `TopicConnectionFactory` as well.  
        For example:
        ```java
        # register some connection factories
        # connectionfactory.[jndiname] = [ConnectionURL]
        connectionfactory.QueueConnectionFactory = amqp://admin:admin@clientID/carbon?brokerlist='tcp://localhost:5675'
        connectionfactory.TopicConnectionFactory = amqp://admin:admin@clientID/carbon?brokerlist='tcp://localhost:5675'
        # register some queues in JNDI using the form
        # queue.[jndiName] = [physicalName]
        queue.JMSMS=JMSMS
        queue.StockQuotesQueue = StockQuotesQueue
        ```
4.  Start WSO2 Message Broker.
5.  Start WSO2 Micro Integrator.

Now, you have both WSO2 Message Broker and the Micro Integrator configured and running.

## Securing the WSO2 MB server

JMS is an integral part of enterprise integration solutions that are highly-reliable, loosely-coupled, and asynchronous. As a result, implementing proper security to your JMS deployments is vital. The below sections discuss some of the best practices of an effective JMS security implementation when used in combination with WSO2 Micro Integrator.

Let's see how some of the key concepts of system security such as authentication, authorization, and availability are implemented in different types of brokers. Given below is an overview of how some common security concepts are implemented in WSO2 MB.

!!! Note
    You can apply the same information mentioned in this section when configuring JMS with [Apache QPid](https://qpid.apache.org/).

Given below is an overview of how some common security concepts are implemented in the WSO2 MB runtime.

| Security Concept                                        | How it is Implemented in EI-Broker                      |
|---------------------------------------------------------|---------------------------------------------------------|
| [Authentication](#ConfigurewiththeBrokerProfile-AuthMB) | Andes Authenticator connected entities to authenticate. |
| [Authorization](#ConfigurewiththeBrokerProfile-AuthrMB) | Creation and use of role-based permissions.             |
| Availability                                            | Clustering using Apache Zookeeper.                      |
| [Integrity](#ConfigurewiththeBrokerProfile-InMB)        | Message-level encryption using WS-Security.             |

Let's see how each concept in the table above is implemented in WSO2 MB.

After setting up WSO2 MB with the Micro Integrator, open `MI_HOME/wso2/broker/conf/advanced/qpid-config.xml` file and add the following line as a child element of `<tuning\>`.

```xml
<messageBatchSizeForBrowserSubscriptions>100000</messageBatchSizeForBrowserSubscriptions>
```

### Authentication: Plain Text

WSO2 MB requires all its incoming connections to be authenticated. The
`MI_HOME/conf/jndi.properties        ` file contains lines
similar to the following. They contain the username and password
credentials used to authenticate connections made to the WSO2 MB
runtime. This is plain text authentication.  

```java
connectionfactory.TopicConnectionFactory = amqp://admin:admin@clientID/carbon?brokerlist='tcp://localhost:5675'
connectionfactory.QueueConnectionFactory = amqp://admin:admin@clientID/carbon?brokerlist='tcp://localhost:5675' 
```

In the WSO2 Micro Integrator authentication example below, we send a request to the proxy service named **testJMSProxy**, which adds a message to the **example.MyQueue** queue.

```xml
<definitions xmlns="http://ws.apache.org/ns/synapse">
  <registry provider="org.wso2.carbon.mediation.registry.WSO2Registry">
     <parameter name="cachableDuration">15000</parameter>
  </registry>
  <proxy name="testJMSProxy"
         transports="https http"
         startOnLoad="true"
         trace="disable">
     <target>
        <inSequence>
           <property name="FORCE_SC_ACCEPTED" value="true" scope="axis2"/>
           <property name="target.endpoint" value="jmsEP" scope="default"/>
           <store messageStore="testMsgStore"/>
        </inSequence>
     </target>
  </proxy>
  <endpoint name="jmsEP">
     <address uri="http://localhost:9000/services/SimpleStockQuoteService"/>
  </endpoint>
  <sequence name="fault">
     <log level="full">
        <property name="MESSAGE" value="Executing default 'fault' sequence"/>
        <property name="ERROR_CODE" expression="get-property('ERROR_CODE')"/>
        <property name="ERROR_MESSAGE" expression="get-property('ERROR_MESSAGE')"/>
     </log>
     <drop/>
  </sequence>
  <sequence name="main">
     <in>
        <log level="full"/>
        <filter source="get-property('To')" regex="http://localhost:9000.*">
           <send/>
        </filter>
     </in>
     <out>
        <send/>
     </out>
     <description>The main sequence for the message mediation</description>
  </sequence>
  <messageStore class="org.wso2.carbon.message.store.persistence.jms.JMSMessageStore"
                name="testMsgStore">
     <parameter name="java.naming.factory.initial">org.wso2.andes.jndi.PropertiesFileInitialContextFactory</parameter>
     <parameter name="java.naming.provider.url">repository/conf/jndi.properties</parameter>
     <parameter name="store.jms.destination">MyQueue</parameter>
  </messageStore>
</definitions>
```

If you change the authentication credentials of the jndi.properties file, the connection will not be authenticated. You
will see an error similar to:

```java
ERROR - AMQConnection Throwable Received but no listener set: org.wso2.andes.AMQDisconnectedException: Server closed connection and reconnection not permitted. 
```

### Authentication: Encrypted

In the [previous authentication example](#authentication-plain-text), the user names and passwords are stored in plain text inside the Micro Integrator's jndi.properties file. These credentials can be stored in an encrypted manner for added security.

### Authorization

The WSO2 MB runtime allows user-based authorization as seen in the example on WSO2 MB Authentication. To set up users, follow the instructions in the [Admin Guide](https://docs.wso2.com/display/ADMIN44x/Managing+Users%2C+Roles+and+Permissions).
  
The WSO2 MB runtime allows role-based authorization for topics, where public/subscribe access can be assigned to user groups. For more
information on setting up role-based authorization for topics, see [Managing Topics and Subscriptions](http://docs.wso2.org/message-broker/Managing+Topics+and+Subscriptions).

### Integrity

Integrity is part of message-level security, and can be implemented using a standard like WS-Security.