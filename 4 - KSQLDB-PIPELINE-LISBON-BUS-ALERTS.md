# Simple Kappa Architecture Pipeline using KSQLDB, KAFKA Http Sink connector and Rest Api
In this tutorial, we'll work with data came from https://carris.tecmic.com/see (is data about Buses in Lisbon Portugal Area) and show how use KSQLDB to get data from joined Streams and create new topic with enriched data and write sink connector to send this data to Rest Api who send alert to whatsapp users numbers when:


1 - bus status is not in movment;

2 - one busNumber is arrived to one predefined bustop configured in Ktable.Exampe:users want to know when the bus is one Stop before their stop and receive alert from whatsapp to leave home and get this bus

3 - speed from bus is above limit



### Prerequisites

* A working Confluent Kafka instance (see the [My Confluent Kafka tutorial](https://github.com/albertochong/AWS-KAFKA-CONFLUENT-PLATFORM) for easy local setup and topics creation for this tutorial).
* C# Rest Api 
* KSQLDB
* Create Http Sink Connector
* Starting streamsets pipeline to get data from Lisbon Bus Status Web Api 

## Part 1 - Create Web Api to send alerts to whatsapp numbers

### Using Visual Studio, Twillio(provider to send message to whatsapp) and azure account to publish Web Api
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Text;
using System.Web.Http;
using Twilio;
using Twilio.Rest.Api.V2010.Account;

namespace WebApiWhatsapp.Controllers
{
    public class WhatsappController : ApiController
    {
        [HttpPost]
        [Route("ChongWhatsappApi/PostNewMessage")]
        public HttpResponseMessage PostNewMessage(string messagem)
        {
           
            HttpResponseMessage response = null;

            const string accountSid = "your twillio account";
            const string authToken = "your twillio token";

            try
            {
                TwilioClient.Init(accountSid, authToken);

                var message = MessageResource.Create(
                    from: new Twilio.Types.PhoneNumber("whatsapp:+14155238886"),
                    body: "Hello, there!",
                    to: new Twilio.Types.PhoneNumber("whatsapp:+351964663133")
                );

                if (message.Sid != "")
                {
                    response = Request.CreateResponse(HttpStatusCode.OK);
                    response.Content = new StringContent("Mensagem enviada com sucesso", Encoding.UTF8, "application/json");
                }
                else
                {
                    response = Request.CreateResponse(HttpStatusCode.NoContent);
                    response.Content = new StringContent("Mensagem não enviada com sucesso", Encoding.UTF8, "application/json");
                }
            }
            catch (Exception)
            {
                response = Request.CreateResponse(HttpStatusCode.InternalServerError);
                response.Content = new StringContent("Ocorreu um erro no servidor", Encoding.UTF8, "application/json");
            }

            return response;
        }
    }
}


```
### Web Api published 
![alt text](https://achong.blob.core.windows.net/gitimages/webapiWhatsapp.PNG)



## Part 2 - KSQLDB to Get Streams

### Creating streams from topic TpBusLisbonStatus
* Streams to get all data
```
CREATE STREAM busLisbonStatus_streams(busNumber INTEGER, state VARCHAR, lastGpsTime VARCHAR,
                                      lastReportTime VARCHAR, lat DOUBLE, lng DOUBLE,
                                      routeNumber VARCHAR, direction VARCHAR, plateNumber VARCHAR,
                                      timeStamp VARCHAR, dataServico VARCHAR )
                                      WITH (KAFKA_TOPIC='TpBusLisbonStatus', VALUE_FORMAT='JSON', KEY='busNumber',
                                      partitions=3);
```

* Streams to get data when the Bus isn´t in movement
```
CREATE STREAM buStatus_offline_Streams
  WITH (PARTITIONS=3,
        VALUE_FORMAT='AVRO') AS
  SELECT busNumber,
         lat,
         lng,
         timeStamp
  FROM busLisbonStatus_streams
  where state ='off' or state ='Standby' or state ='Undefined' or state ='Unknown'
  PARTITION BY busNumber;
 
 
 ```

## Part 3 - Sink HTTP Connector to send data to C# Rest Api
```
1 - Download from https://www.confluent.io/hub/confluentinc/kafka-connect-http 

2 - Put the Http connector downloaded above in the same folder as the Kafka Connect Http plugin
    Should be <CONFLUENT_HOME>/shared/java

3 - Open Ksql
CREATE SINK CONNECTOR Whatsapp_By_Twillio_sink WITH 
(
  'topics'                            = 'BUSTATUS_OFFLINE_STREAMS',
  'tasks.max'                         =  '1',
  'connector.class'                       = 'io.confluent.connect.http.HttpSinkConnector',
  'http.api.url'                           = 'https://webapiwhatsapp.azurewebsites.net/ChongWhatsappApi/PostNewMessage',
  'key.converter'                           = 'org.apache.kafka.connect.storage.StringConverter',
  'header.converter'                        = 'org.apache.kafka.connect.storage.StringConverter',
  'value.converter'                         = 'org.apache.kafka.connect.storage.StringConverter',
  'confluent.topic.bootstrap.servers'       = 'ec2-18-217-55-153.us-east-2.compute.amazonaws.com:9092',
  'confluent.topic.replication.factor'      = '1',
  'reporter.bootstrap.servers'              = 'ec2-18-217-55-153.us-east-2.compute.amazonaws.com:9092',
  'reporter.error.topic.replication.factor' = '1',
  'reporter.result.topic.replication.factor'= '1'
);

```
![alt text](https://achong.blob.core.windows.net/gitimages/Whatsapp_http_coonector.PNG)

## Part 4 - Starting Streamsets pipeline to write streams to kafka topic

### (see the [Pipeline tutorial](https://github.com/albertochong/PIPELINES-ETL-PROJECTS/blob/master/3%20-%20PIPELINE-LISBON-BUS-STATUS.md) for easy comphreension)
![alt text](https://achong.blob.core.windows.net/gitimages/pipepline_lisbon_bus_status_kafka.PNG)
