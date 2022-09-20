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

-   IBM i must have ports 449, 8470, 8472,8473,8475 and 8476, 9470, 9472, 9473, 9475 and 9476 accessible from confluent runtime

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

**Confluent Setup**
    
-  **Steps to setup confluent kafka standalone environment in local system**

      https://docs.confluent.io/5.5.0/quickstart/ce-quickstart.html

-  **Steps to setup confluent kafka standalone environment in cloud**

      https://dzone.com/articles/installing-and-configuring-confluent-platform-kafk


once the confluent kafka install follows the below steps for connector installation
            
  **Install the AS400 Connector** 

  Connectors are packaged as Kafka Connect plugins. Kafka connect isolates each plugin so that the 
  plugin libraries do not conflict with each other. Download and extract the ZIP file for 
  your connector and then follow the below installation instructions.
  
  -  **In Standalone mode**

      1.  kafka-connect-as400 connector can be downloaded directly from the confluent-hub using 
                                
                confluent-hub install infoviewsystems/kafka-connect-as400:1.1.1
                
      2.  Extract the content into the desired location (Preferred: /confluent/share/java or /confluent/share/confluent-hub-component)
          Generally it will install /confluent/share/confluent-hub-component location so no need to extract it manually.

      3.  Start confluent control center using command “confluent local services start”
      
      ![image](https://user-images.githubusercontent.com/46368616/133743614-0642b415-96c1-49c2-9501-507388935129.png)
      
      4.  Control center should be up and running and can be verified with http://{HOST}:9021
        
      ![image](https://user-images.githubusercontent.com/46368616/133744830-27cd0bc2-4bf0-4f4c-8b97-c8f84a21bad8.png)
      
      5.  Source and Sink connectors are ready to configure now. And here are the sample configurations to be used.


- **Confluent Setup and connector installation through docker**

    1.  please find the predefine docker-compose.yml file
    
	     https://github.com/infoviewsystems/as400-gateway-kafka-doc/blob/main/docker-compose.yml
	       
    2.	Check once the connect service from the above file for 

		volumes:

		/home/ubuntu/license/:/opt/

		create license directory in local system (ex. mkdir license) and place the as400-license.lic file in the same directory.

		while running docker-compose.yml file license file i.e as400-license.lic will copy from local to docker container path i.e /opt/.
    
    3.	Now execute below command 
        
		docker-compose up -d

		It will download all images from docker hub and install 

		once downloading completed verify the status with below command is all services up and running

		docker-compose ps -a

		![image](https://user-images.githubusercontent.com/88314020/191201820-56c62361-3abb-48c5-8d8b-19b7a3d18530.png)

    
    4.  To verify the license is copied to /opt/, execute the below command to connect with kafka connect service with interactive mode
        
		docker exec -it connect bash

		execute below command to validate

		cd /opt/
		
		ls -l

		find the screenshot for reference

		![image](https://user-images.githubusercontent.com/88314020/191203434-03ebdc39-d320-4c38-b9de-a595950f89fc.png)


    5.  To verify the infoviewsystems-as400-kafka-connect is install or not go through below steps
         
		 cd /usr/share/confluent-hub-components/ 
		 
		 ls -l
		 
		 find the screenshot for reference

		 ![image](https://user-images.githubusercontent.com/88314020/191204410-9d57fe7d-b4ca-4d21-8b57-3238455b2468.png)

     
    6.  Control center should be up and running and can be verified with http://{HOST}:9021 

        ![image](https://user-images.githubusercontent.com/46368616/133752176-02e16a83-6d16-40bd-a209-5ed5d7a5a4a5.png)

    7.  Source and Sink connectors are ready to configure now. And here are the sample configurations to be used. 



  **License Management:**

	The IBM i connector requires a license file &quot;as400-license.lic&quot; from Infoview to enable access to specific IBM i system(s).

	Managing license in different ways by using different protocols such as S3, HTTP/HTTPS, FTP, FILE, SMB etc. and accessing it through these protocols in 
	
	our application needs to configure in connector configuration.

	Available Protocols to load license file/truststore file (HTTP,HTTPS, FTP, SMB, S3, FILE, CLASSPATH)

	Based on the protocol parameters needs to be configure.
	
	1. FILE
	
		find the attached screenshot for reference
		
		![image](https://user-images.githubusercontent.com/88314020/191207207-c25650a9-670b-4699-80a0-2a628450903d.png)

		It requires two values 
		
		  a. path 

		   This path will be common for license file and truststore file

		  b. filename

		    Provide the license file name
		    
	2. S3
	
		If the license/truststore file wanted to access from  S3 
		 
		Please find the screenshot for reference 
		 
		 ![image](https://user-images.githubusercontent.com/88314020/191210478-f0869272-96e8-4d1b-b888-4e1b965f32d1.png)

		It requires five values to access files from S3
		  
		   a. S3 bucket path
		   
		   b. Filename
		   
		   c. S3 region
		   
		   d. Access key
		   
		   f. Secret Key 
		   
	3. CLASSPATH
	
		Please find the screenshot for reference
		    
		  ![image](https://user-images.githubusercontent.com/88314020/191212958-386f01f2-ba5c-4d5f-a6fc-89ab748406d4.png)

		It requires two values
		    
		    a. classpath
		    
		       The path which is used to set CLASSPATH for licence/truststore file

		    b. filename
	4. FTP
	 
		Please refere the screenshot to configure the values required for FTP protocol inorder to access license/truststore file
		     
		     
		  ![image](https://user-images.githubusercontent.com/88314020/191214265-88000b8c-17e1-4b16-a945-74b173810fbc.png)
		     
	5. HTTP
	 
		 Please refere the screenshot to configure the values required for HTTP protocol inorder to access license/truststore file
		  
		     
		  ![image](https://user-images.githubusercontent.com/88314020/191215190-d703d1b6-d94c-4b6c-bb34-b5d6eade8762.png)
		  
	     	
	6. HTTPS
	 
		Please refere the screenshot to configure the values required for HTTPS protocol inorder to access license/truststore file
		    
		    
		  ![image](https://user-images.githubusercontent.com/88314020/191217041-2324a0e6-b3c3-4e23-9eb0-fc603f65d0c5.png)
		  

Based on protocol type needs to configure below properties


| Protocols     | Parameters to configure                                   |Mandatory                    |configuration keys for parameters |
|:--------------:|-----------------------------------------------------------|-----------------------------------|-----------------------------------|
| FILE| path <br> filename                                                   | required <br>  required           |as400.license.path<br> license.fileName|
| S3|S3 bucket path<br>filename<br>S3 region<br>Access key<br>Secret key     | required<br>required<br>required<br>required<br>required|s3.bucket<br>license.fileName<br>s3.region<br>s3.accessKey<br>s3.secretKey|
|FTP|Host<br>directory path<br>filename<br>username<br>password|required<br>required<br>required<br>required<br>required|ftp.host<br>ftp.dir.path<br>license.fileName<br>ftp.username<br>ftp.password|
|CLASSPATH|license/truststore classpath<br>filename|required<br>required|license.classpath<br>license.fileName|
|HTTP|HTTP Host<br>HTTP Directory<br>filename<br>HTTP Username<br>HTTP Password|required<br>required<br>required<br>required<br>required|http.url<br>http.dir.path<br>license.fileName<br>http.username<br>http.password|
|HTTPS|HTTPS Host<br>HTTPS Directory<br>filename<br>HTTPS Username<br>HTTPS Password|required<br>required<br>required<br>required<br>required|https.url<br>https.dir.path<br>license.fileName<br>https.username<br>https.password|

Please contact Infoview Systems Connector support team at **(734) 293-2160** and **(+91) 4042707110** or via email sales@infoviewsystems.com and     marketing@infoviewsystems.com 
  
**AS400 Connection Configuration Properties**
-  **Connection**

| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters |
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
| Name          |Enter a unique label for the connector in your application.| Required                          |AS400SourceConnectorConnector_0|name|
|AS400 URL      |AS400 system connection url.                              |    Required                          |null|as400.url|
|User Id        |AS400 System user                                          |Required     |null|as400.userId|
|PASSWORD     |AS400 system connection password.|Required|null|as400.password|
|License/truststore protocol |Please refere above mentioned license management section |Required|null|as400.license.protocol|
|IASP|Logical partitions in the systems.|Optional|null|as400.iasp|
|Library List|List of libraries, in addition to the library list associated with the user id. The libraries must be separated with comma and will be added to the top of the library list.|Optional|null|as400.libraries|
|Secure Connection|Enable secure connection with AS400 over encrypted channel.|Optional|False|as400.secure.connection|
|Socket Timeout|Socket Timeout, ms. Default value of -1 means the default JVM SO_TIMEOUT will be used|Optional|-1|as400.socket.timeout|
|Time unit to be used for Socket Timeout|Socket Timeout time unit|Optional|MILLISECONDS|as400.socket.timeunit|
|Connection Retries|Number of times to retry establishing the connection internally before throwing an exception and passing back to Kafka connection Manager.|Optional|3|as400.connection.retry|
|Reconnection Period|Time between internal reconnection retries in ms.|Optional|60000|as400.reconnection.period|
|Time unit to be used for Reconnection Period|Reconnection period time unit.|Optional|MILLISECONDS|as400.reconnection.timeunit|
|Connection Time to Live|Max time (Seconds) that the connection can be used.|Optional|0|as400.connection.live|
|Reconnection Period time out|Time out to be used for connection time out to live|Optional|SECONDS|

-  **Connection (Optional)**

| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|----------------------------------|
|Operation Type |An Operation type to be done on AS400 FILE = 0, PRINT = 1,COMMAND = 2,DATAQUEUE = 3, DATABASE = 4, RECORDACCESS = 5, CENTRAL = 6, SIGNON = 7|Optional|2|as400.operation.type|
|CCSID|-|Optional|0|as400.ccsid|
|Pre Start Count Data Q|-|Optional|2|as400.prestart.count.dq|
|Pre Start Count Command|-|Optional|2|as400.prestart.count.command|
|Cleanup Interval|-|Optional|2|as400.cleanup.interval|
|Max Connection|Maximum connections allowed.|Optional|5|as400.max.connection|
|Max Inactivity|Maximum time to inactive the session for connection.|Optional|10|as400.max.inactivity|
|Max Lifetime|Maximum lifetime for connection.|Optional|60000|as400.max.lifetime|
|Max Use Count|-|Optional|10|as400.max.usecount|
|Max Use Time|-|Optional|30000|as400.max.usetime|
|Pre-Test Connection|-|Optional|true|as400.pretest.connection|
|Run Maintenance|-|Optional|true|as400.run.maintenance|
|Thread Used|-|Optional|true|as400.thread.used|
|Keep Alive|-|Optional|true|as400.keep.alive|
|Login Timeout|-|Optional|0|as400.login.timeout|
|Receive Buffer Size|-|Optional|1000|as400.receive.buffer.size|
|Send Buffer Size|-|Optional|1000|as400.send.buffer.size|
|So Linger|-|Optional|0|as400.so.linger|
|So Timeout|-|Optional|0|as400.so.timeout|
|TCP No Delay|-|Optional|true|as400.tcp.nodelay|

**AS400 Source Connector Configuration Properties**

Configure these connector properties.

![image](https://user-images.githubusercontent.com/46368616/133764594-8d2caf1b-be1c-4745-8a72-c413f4e6e8fc.png)


| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
|Data Queue|Read data queue name.|Required|null|as400.read.dataqueue.name|
|Library|Read data queue library.|Required|null|as400.read.dataqueue.library|
|Key|Must be specified for keyed data queues and blank for non-keyed data queues. For reading any message from data queue.|Optional|null|as400.read.dataqueue.key|
|Key Search Type|Must be specified for keyed data queues. For reading any message from data queue, available search types are equal,not equal,greater than,less than,greater than or equal,less than or equal.|Optional|null|as400.read.dataqueue.key.search.type|
|Keep messages in Queue|Ensure it is unchecked unless the intent is to leave the message in the queue after reading.|Optional|true|as400.source.keep.message|
|Format File Name|Optional parameter allows treating data queue entry as an externally defined data structure. When defined, the connector will dynamically retrieve the record format from the specified IBM i file, and parse the received data queue entry into the map of field name / value pairs. The connector will perform the type conversion, supporting all types such as packed, date / time etc.|Optional|null|as400.source.format.name|
|Format File Library|When format file is specified, the format file library can also be specified, otherwise the format file will be located based on the connection library list.|Optional|null|as400.source.file.library|
|Number of Consumers|Number of consumers.|Optional|4|as400.source.consumer.numbers|

- **Source Response**


| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
|Response Data Queue|Will update the response back to the data queue.|Optional|null|as400.write.dataqueue.name|
|Response Data Queue Library|Response data queue library.|Optional|null|as400.write.dataqueue.library|
|Response Data Queue Expression|Response data queue Expression.|Optional|null|as400.response.dataqueue.expression|

**Note:** Here is a sample property for AS400 source connector configuration
```
{
  "name": "AS400SourceConnector_CDCDQ8",
  "config": {
    "connector.class": "com.infoviewsystems.kafka.connect.as400.core.AS400SourceConnector",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "io.confluent.connect.json.JsonSchemaConverter",
    "as400.url": "xxxxxxxxxx",
    "as400.userId": "xxxxx",
    "as400.password": "xxxxxxx",
    "as400.secure.connection": "false",
    "as400.license.protocol": "FILE",
    "as400.license.path": "/home/ubuntu/license",
    "license.fileName": "as400-license.lic",
    "as400.read.dataqueue.name": "abc",
    "as400.read.dataqueue.library": "Library",
    "as400.source.format.name": "xyz",
    "as400.source.file.library": "Library",
    "source.kafka.topic": "Test",
    "value.converter.schema.registry.url": "http://localhost:8081"
  }
}
```

**AS400 Data Queue Sink Connector Configuration Properties**

Configure these connector properties.

![image](https://user-images.githubusercontent.com/46368616/133767545-f4f0bf51-c9cb-4435-ac0f-04a5190f3502.png)

| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
|Data Queue     |Write data queue name.|Required|null|as400.write.dataqueue.name|
|Library        |Write data queue library.|Required|null|as400.write.dataqueue.library|
|Is Keyed DataQ |Must be specified for keyed data queues and blank for non-keyed data queues. For reading any message from data queue.|Optional|false|as400.write.dataqueue.key|
|Format File Name|Optional parameter allows treating data queue entry as an externally defined data structure. When defined, the connector will dynamically retrieve the record format from the the specified IBM i file and parse the received data queue entry into the map of field name / value pairs. The connector will perform the type conversion, supporting all types such as packed, date / time etc.|Optional|null|as400.sink.format.name|
|Format File Library|When format file is specified, the format file library can also be specified, otherwise the format file will be located based on the connection library list.|Optional|null|as400.sink.file.library|
|DQ Entry Length|Max DQ Entry Length. When specified and greater than 0, the parameter value will be truncated to fit the max length.|Optional|0|as400.dq.entry.length|
|DQ Key Length|Max DQ Key Length. When specified and greater than 0, the parameter value will be used (instead of dynamically retrieving it from DQ definitions on the server).|Optional|null|as400.dq.key.length|


**Note:** Here is a sample property for AS400 Data Queue Sink connector configuration
```
{
  "name": "AS400DataQueueSinkConnectorConnector_0",
  "config": {
    "connector.class": "com.infoviewsystems.kafka.connect.as400.core.AS400DataQueueSinkConnector",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "io.confluent.connect.json.JsonSchemaConverter",
    "topics": "default_ksql_processing_log",
    "as400.url": "xxxxxxxxxx",
    "as400.userId": "xxxxx",
    "as400.password": "xxxxxx",
    "as400.secure.connection": "false",
    "as400.license.protocol": "FILE",
    "as400.license.path": "/home/apeksha/license",
    "license.fileName": "as400-license.lic",
    "as400.write.dataqueue.name": "abc",
    "as400.write.dataqueue.library": "Library",
    "as400.sink.format.name": "xyz",
    "as400.sink.file.library": "Library",
    "value.converter.schema.registry.url": "http://localhost:8081"
  }
}
```

**AS400 Program Call Sink Connector Configuration Properties**

Configure these connector properties

![image](https://user-images.githubusercontent.com/88314020/191261271-f63a4fdb-e1ff-4f97-a9f1-5fd9252aa630.png)



| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
|Program Name   |AS400 program name|Required|null|as400.program.name|
|Program Library|Program library name|Required|null|as400.program.library|
|Program Parameters|List of definitions and value references of program parameters|Optional|null|as400.program.parameters|
|Procedure Name|Name of the procedure|Optional|null|as400.procedure.name|
|Procedure Returns Value|Indicator if the program procedure returns a value.|Optional|false|as400.procedure.returnsValue|
|Threadsafe|Indicator if the program is thread safe|Optional|false|as400.threadsafe|
|Sink Target Topic|Sink target topic is a topic to push program call output|Required|null|sink.kafka.topic|
|Kafka Partition Key|Kafka partition key to push record to topic in the specific location|Required|null|sink.kafka.partition.key|

**Note:** Here is a sample property for AS400 Data Queue Sink connector configuration


```
{
  "name": "AS400ProgramCallSinkConnector_POSTORDSP",
  "config": {
    "connector.class": "com.infoviewsystems.kafka.connect.as400.core.AS400ProgramCallSinkConnector",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "topics": "POSTORDSP_INPUT",
    "as400.url": "xxxxxxxxxx",
    "as400.userId": "xxxx",
    "as400.password": "xxxx",
    "as400.secure.connection": "false",
    "as400.license.protocol": "FILE",
    "as400.license.path": "/home/ubuntu/license",
    "license.fileName": "as400-license.lic",
    "as400.program.name": "POSTORDSP",
    "as400.program.library": "Library",
    "as400.program.parameters": "{ }",
    "as400.procedure.name": "POSTORDERS",
    "as400.procedure.returnsValue": "false",
    "as400.threadsafe": "false",
    "sink.kafka.topic": "POSTORDSP_OUTPUT",
    "value.converter.schemas.enable": "false"
  }
}
```

**TLS Configuration**

| Parameter     | Description                                               |Mandatory                          |Default Value|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|
|Truststore|Truststore is used to store jks type of certificates from Certified Authorities (CA) that verify the certificate presented by the server in SSL connection|


![image](https://user-images.githubusercontent.com/46368616/133774025-d397bcd4-b88f-49a7-b06f-305b0dac9f5b.png)


**Truststore**

| Parameter     | Description                                               |Mandatory                          |Default Value|configuration keys for parameters|
|---------------|-----------------------------------------------------------|-----------------------------------|-------------|---------------------------------|
|Truststore Filename| truststore file name|optional|null|truststore.fileName|
|Password|The password used to protect the trust store.|Optional|null|TLS.password|
|Insecure|If true, no certificate validations are performed, rendering connections vulnerable to attacks. Use at your own risk.|Optional|false|TLS.isInsecure|
|IsKeystoreConfigured|-|Optional|true|TLS.isKeystoreConfigure|
|IsTruststoreConfigured|-|Optional|true|TLS.isTruststoreConfigured|



**Contact Us**

[Contact us](http://www.infoviewsystems.com/contact-us) for connector pricing info, trial license, or support questions.
