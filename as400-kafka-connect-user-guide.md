# USER GUIDE

# IBM i AS/400 Connector (Source and Sink) for Confluent Platform

**Introduction** 

Infoview Systems AS400Gateway suite of products eliminates the stress and impact of IBM i / AS400 legacy system integration on development teams, minimizes the time and resources put into building integrations by hand, and enables non-IBM i developers to unlock legacy business logic and data directly from the comfort of their modern integration development stack. Certified and rigorously tested by Confluent, the connector was designed to accelerate IBM i / AS400 integrations with other systems and services.
We are a global cross-platform service team with a unique fluency in legacy and modern technology stacks, including Confluent and IBM i / AS400. Infoview’s dedicated customer success representatives coordinate just-in-time technical assistance and support to client teams ensuring you have all the help you may need, when you need it. We are more than happy to provide a trial license for our products, participate in discovery sessions, run live demos for typical integration scenarios with our Gateway products, as well as assist with or perform a proof of concept based on particular use cases.

[Contact us](http://www.infoviewsystems.com/contact-us) for connector pricing info, trial license, or support questions.

**IBM i / AS400 Connector for Confluent Overview** 

The IBM i / AS400 was first introduced in 1988, and since then has evolved keeping pace with new concepts and technologies, it is a very stable and robust modern all-purpose system that surprisingly  requires little or no management. The system is able to run core line of business applications securely and predictably, focusing on quality of service and availability and offering a compelling total cost of ownership for integrated on-premises solutions. IBM made several changes to the server and OS name (iSeries, System i, IBM i, Power Systems for i) but most still refer to it as AS/400.
The IBM i platform offers several integration options including PHP, Java, WebSphere, specialized lightweight web service containers, FTP, SMTP / emails, DB2 interfaces, data queues, integrated file systems - IFS, as well as an abundance of products offered by IBM and Third-party vendors. The main benefit of using "native" options such as Program Calls and Data Queues is that IBM i development teams do not have to learn another language or purchase and support another technology in order to build the integration layer and can easily communicate with external systems using only traditional development tools.
The Program Call is the most straightforward and low code option for exposing IBM i business logic as a reusable asset. The IBM i connector enables direct program calls from Kafka applications, passing parameters into the program and receiving the results back in real time.
Data queues are native IBM i objects designed primarily for inter-process communications. They are lightweight persistent queues that support processing by a key and support order by FIFO or LIFO organization. The majority of integration use cases can be implemented with the pair of request and response Data Queues. The source system places a message to the request data queue and waits for an acknowledgement message to be placed on the response data queue. The target system receives and processes a message from the request data queue then places the acknowledgement to the response data queue.
The IBM i connector makes it easy to build Kafka code that talks to IBM i applications. Infoview Systems also developed the IBM i ‘Web Transaction Framework’ that makes it very fast and easy to develop IBM i code that communicates with Kafka.


**Prerequisites**

The connector is designed to work with IBM i objects (e.g. programs and data queues) and therefore this document assumes you are familiar with IBM i operational and development environments and tools.
This document assumes you are familiar with Confluent Kafka, Kafka Connect and Confluent Control Center and provides configuration examples and a detailed explanation of each and properties required to run the connector in Confluent Control Center.

**IBM i server configuration and requirements** 

-   IBM i must have ports 449, 8470, 8472,8473,8475 and 8476, 9470, 9472, 9473, 9475 and 9476 accessible from Mule runtime

-   IBM i must have *CENTRAL, *DTAQ, *RMTCMD, *SIGNON and *SRVMAP host servers running in the QSYSWRK subsystem

-   If secure TLS connection is used, the TLS certificate must be applied to Central, Data Queue, 
    Remote Command, Sign on, and DDM / DRDA services in Digital Certificate Manager

-   IBM i user ID must be authorized to perform the operations on the intended IBM i objects

-   If there’s an additional security software that locks down the remote execution functionality,
    the IBM i user ID defined for connector configuration must be allowed to execute remote calls 
    and access database, IFS and DDM services

**Dependencies** 

-   Access to IBM i server

-   Access to Confluent Kafka  

**Compatibility Matrix**
| Application/Service     | Version       |
| ----------------------- |---------------|
| Confluent Kafka         |6.0.0 or higher|
| Confluent Control Center|6.0.0 or higher|
| IBM i / OS400           |V5R4 or higher |
             

**Connector Operations** 

The IBM i connector is an operation-based connector, which means that when you add the 
connector to your Kafka connect cluster, you need to configure a specific operation the connector
is intended to perform. The connector supports the following operations:

| Operation                          | Version       |
| -----------------------------------|---------------|
| Read Data Queue (Message Source)   | Perpetually listen for new messages arriving to specific data queue.|
| Write to Data Queue                | Write messages to data queue.|
| Program Call                       | Execute IBM i program.       |
             
             
**Install the AS400 Connector** 

Connectors are packaged as Kafka Connect plugins. Kafka connect isolates each plugin so that the 
plugin libraries do not conflict with each other. Download and extract the ZIP file for 
your connector and then follow the below installation instructions.
-  **In Standalone mode**

    1.  kafka-connect-as400 connector can be downloaded directly from the confluent-hub using 
      	                       
              confluent-hub install confluentinc/kafka-connect-as400:1.0.0
              
    2.  Extract the content into the desired location (Preferred: /confluent/share/java or /confluent/share/confluent-hub-component)

    3.  Start confluent control center using command “confluent local services start”
    
     ![image](https://user-images.githubusercontent.com/46368616/133743614-0642b415-96c1-49c2-9501-507388935129.png)
    
    4.  Control center should be up and running and can be verified with [http://localhost:9021](http://localhost:9021)
       
     ![image](https://user-images.githubusercontent.com/46368616/133744830-27cd0bc2-4bf0-4f4c-8b97-c8f84a21bad8.png)
    
    5.  Source and Sink connectors are ready to configure now. And here are the sample configurations to be used.

-  **In Distributed mode**

    1.	kafka-connect-as400 connector can be downloaded directly from the confluent-hub using
	
              confluent-hub install confluentinc/kafka-connect-as400:1.0.0
                
    2.	Connector can be installed using the Confluent Hub or you can manually download the ZIP file.
	
    3.	Extract the content into the desired location 
	
              /usr/share/java,/home/apeksha/confluent-6.2.0/share/confluent-hub-components
                
    4.	Add downloaded zip file path to the plugin path in connect-distributed properties file. For example
	
              plugin.path=/usr/share/java, /home/apeksha/confluent-6.2.0/share/confluent-hub-components

      ![image](https://user-images.githubusercontent.com/46368616/133746430-5a95aec4-4576-4efd-a09e-74ae1fd3fbaa.png)
      
    5.  Kafka connect finds the plugins using its plugin path. A plugin path is a comma-separated list of directories defined in the kafka connects worker configurations
    
    6.  Start the Connect workers using “confluent local services start”. Connect will discover all connectors defined for plugin.path

      ![image](https://user-images.githubusercontent.com/46368616/133747225-e2767d29-5b0a-4fa5-b6e8-0be14838003d.png)
     
    7.  Repeat above steps for all the machines where connect is running. Each connector must be available on each worker
      
    8.	Control center should be up and running and can be verified with [http://localhost:9021](http://localhost:9021)

     ![image](https://user-images.githubusercontent.com/46368616/133747563-fb564e96-f5be-4e92-bc75-c2b2fd8f00db.png)
     
    9. 	Source and Sink connectors are ready to configure now. And here are the sample configurations to be used.

-  **Through Docker**

    1.  kafka-connect-as400 connector can be downloaded directly from the confluent-hub using
    
               confluent-hub install confluentinc/kafka-connect-as400:1.0.0
	       
    2.	Extract the content into the desired location (Example: ./Downloads)
    
    3.	Add downloaded zip file path (./Downloads) in the docker file as shown below

    ![image](https://user-images.githubusercontent.com/46368616/133751696-3d17ae04-d2fe-468f-a6ee-6634a930e9d3.png)
    
    4.  Run “Docker-compose up -d” to start the confluent kafka platform

    ![image](https://user-images.githubusercontent.com/46368616/133751829-3e9ae6cd-3eb0-4ff7-8e90-f4811243a1ff.png)

    5.  Check whether all the components are up and running using “docker-compose ps -a”

    ![image](https://user-images.githubusercontent.com/46368616/133752746-471617dc-876e-4372-9d55-e02e86bc9ea4.png)

    
    6.  Control center should be up and running and can be verified with [http://localhost:9021](http://localhost:9021) 

    ![image](https://user-images.githubusercontent.com/46368616/133752176-02e16a83-6d16-40bd-a209-5ed5d7a5a4a5.png)

    7.  Source and Sink connectors are ready to configure now. And here are the sample configurations to be used. 


**License**

The IBM i connector requires a license file “as400-license.lic” from Infoview to enable access to specific IBM i system(s). 
    
  1.   If the confluent platform running in standalone mode, then the connector license must be placed into the 
       directory which should be configured in license path of connect configuration. 
    
    
 ![image](https://user-images.githubusercontent.com/46368616/133753154-aef26d53-f4f6-4090-8dc3-1c1a08922a72.png)
 
 
 ![image](https://user-images.githubusercontent.com/46368616/133755111-6d2b9a70-0da5-4e50-b38b-9f881587bb79.png)
 

 2.   If the confluent platform running through docker then license path should be declared in the docker file.

 
 ![image](https://user-images.githubusercontent.com/46368616/133755318-caec2ade-52e2-4aa4-a28c-d7bdb267bd89.png)


  Please contact Infoview Systems Connector support team at **(734) 293-2160** and **(+91) 4042707110** or via email sales@infoviewsystems.com and marketing@infoviewsystems.com 
  
**AS400 Connection Configuration Properties**
-  **Connection**

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
| Name          |Enter a unique label for the connector in your application.| Required                          |AS400SourceConnectorConnector_0|
|AS400 URL      |AS400 system connection user.                              |    Required                          |null|
|PASSWORD     |AS400 system connection password.|Required|null|
|License Path|License file path.|Required|null|
|IASP|Logical partitions in the systems.|Optional|null|
|Library List|List of libraries, in addition to the library list associated with the user id. The libraries must be separated with comma and will be added to the top of the library list.|Optional|null|
|Secure Connection|Enable secure connection with AS400 over encrypted channel.|Optional|False|
|Socket Timeout|Socket Timeout, ms. Default value of -1 means the default JVM SO_TIMEOUT will be used|Optional|-1|
|Time unit to be used for Socket Timeout|Socket Timeout time unit|Optional|MILLISECONDS|
|Connection Retries|Number of times to retry establishing the connection internally before throwing an exception and passing back to Kafka connection Manager.|Optional|3|
|Reconnection Period|Time between internal reconnection retries in ms.|Optional|60000|
|Time unit to be used for Reconnection Period|Reconnection period time unit.|Optional|MILLISECONDS|
|Connection Time to Live|Max time (Seconds) that the connection can be used.|Optional|0|
|Reconnection Period time out|Time out to be used for connection time out to live|Optional|SECONDS|

-  **Connection (Optional)**

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Operation Type |An Operation type to be done on AS400 FILE = 0, PRINT = 1,COMMAND = 2,DATAQUEUE = 3, DATABASE = 4, RECORDACCESS = 5, CENTRAL = 6, SIGNON = 7|Optional|2|
|CCSID|-|Optional|0|
|Pre Start Count Data Q|-|Optional|2|
|Pre Start Count Command|-|Optional|2|
|Cleanup Interval|-|Optional|2|
|Max Connection|Maximum connections allowed.|Optional|5|
|Max Inactivity|Maximum time to inactive the session for connection.|Optional|10|
|Max Lifetime|Maximum lifetime for connection.|Optional|60000|
|Max Use Count|-|Optional|10|
|Max Use Time|-|Optional|30000|
|Pre-Test Connection|-|Optional|true|
|Run Maintenance|-|Optional|true|
|Thread Used|-|Optional|true|
|Keep Alive|-|Optional|true|
|Login Timeout|-|Optional|0|
|Receive Buffer Size|-|Optional|1000|
|Send Buffer Size|-|Optional|1000|
|So Linger|-|Optional|0|
|So Timeout|-|Optional|0|
|TCP No Delay|-|Optional|true|

**AS400 Source Connector Configuration Properties**

Configure these connector properties.

![image](https://user-images.githubusercontent.com/46368616/133764594-8d2caf1b-be1c-4745-8a72-c413f4e6e8fc.png)


| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Data Queue|Read data queue name.|Required|null|
|Library|Read data queue library.|Required|null|
|Key|Must be specified for keyed data queues and blank for non-keyed data queues. For reading any message from data queue.|Optional|null|
|Key Search Type|Must be specified for keyed data queues. For reading any message from data queue, enter Greater or Equal.|Required|null|
|Keep messages in Queue|Ensure it is unchecked unless the intent is to leave the message in the queue after reading.|Optional|true|
|Format File Name|Optional parameter allows treating data queue entry as an externally defined data structure. When defined, the connector will dynamically retrieve the record format from the specified IBM i file, and parse the received data queue entry into the map of field name / value pairs. The connector will perform the type conversion, supporting all types such as packed, date / time etc.|Optional|null|
|Format File Library|When format file is specified, the format file library can also be specified, otherwise the format file will be located based on the connection library list.|Optional|null|
|Number of Consumers|Number of consumers.|Optional|4|

- **Source Response**


| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Response Data Queue|Will update the response back to the data queue.|Optional|null|
|Response Data Queue Library|Response data queue library.|Optional|null|
|Response Data Queue Expression|Response data queue Expression.|Optional|null|

**Note:** Here is a sample property for AS400 source connector configuration

![image](https://user-images.githubusercontent.com/46368616/133767029-3d6e908f-e846-4c68-b180-971f8680c091.png)

**AS400 Data Queue Sink Connector Configuration Properties**

Configure these connector properties.

![image](https://user-images.githubusercontent.com/46368616/133767545-f4f0bf51-c9cb-4435-ac0f-04a5190f3502.png)

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Data Queue     |Write data queue name.|Required|null|
|Library        |Write data queue library.|Required|null|
|Is Keyed DataQ |Must be specified for keyed data queues and blank for non-keyed data queues. For reading any message from data queue.|Optional|false|
|Format File Name|Optional parameter allows treating data queue entry as an externally defined data structure. When defined, the connector will dynamically retrieve the record format from the the specified IBM i file and parse the received data queue entry into the map of field name / value pairs. The connector will perform the type conversion, supporting all types such as packed, date / time etc.|Optional|null|
|Format File Library|When format file is specified, the format file library can also be specified, otherwise the format file will be located based on the connection library list.|Optional|null|
|Format File Library|When format file is specified, the format file library can also be specified, otherwise the format file will be located based on the connection library list.|Optional|null|
|DQ Entry Length|Max DQ Entry Length. When specified and greater than 0, the parameter value will be truncated to fit the max length.|Optional|0|
|DQ Key Length|Max DQ Key Length. When specified and greater than 0, the parameter value will be used (instead of dynamically retrieving it from DQ definitions on the server).|Optional|null|


**Note:** Here is a sample property for AS400 Data Queue Sink connector configuration


![image](https://user-images.githubusercontent.com/46368616/133770581-cbc0d2e2-94d2-4882-8fc7-92eaaf34fa8f.png)

**AS400 Program Call Sink Connector Configuration Properties**

Configure these connector properties

![image](https://user-images.githubusercontent.com/46368616/133770695-f3f16a0b-dd3d-4214-b117-a8420a73a929.png)

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Program Name   |AS400 program name|Required|null|
|Program Library|Program library name|Required|null|
|Program Parameters|List of definitions and value references of program parameters|Optional|null|
|Procedure Name|Name of the procedure|Optional|null|
|Procedure Returns Value|Indicator if the program procedure returns a value.|Optional|false|
|Threadsafe|Indicator if the program is thread safe|Optional|false|
|Sink Target Topic|Sink target topic is a topic to push program call output|Required|null|
|Kafka Partition Key|Kafka partition key to push record to topic in the specific location|Required|null|

**Note:** Here is a sample property for AS400 Data Queue Sink connector configuration

![image](https://user-images.githubusercontent.com/46368616/133773202-dd26b9dd-3bae-4ea4-bb3b-b45f70cfd889.png)

**TLS Configuration**

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Truststore|Truststore is used to store certificates from Certified Authorities (CA) that verify the certificate presented by the server in SSL connection|


![image](https://user-images.githubusercontent.com/46368616/133774025-d397bcd4-b88f-49a7-b06f-305b0dac9f5b.png)


**Truststore**

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Path           |The location (which will be resolved relative to the current classpath and file system, if possible) of the trust store.|Optional|null|
|Password|The password used to protect the trust store.|Optional|null|
|Insecure|If true, no certificate validations are performed, rendering connections vulnerable to attacks. Use at your own risk.|Optional|false|
|IsKeystoreConfigured|-|Optional|true|
|IsTruststoreConfigured|-|Optional|true|



**Contact Us**

[Contact us](http://www.infoviewsystems.com/contact-us) for connector pricing info, trial license, or support questions.