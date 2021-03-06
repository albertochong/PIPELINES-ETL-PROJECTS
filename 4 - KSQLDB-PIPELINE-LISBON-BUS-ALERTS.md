# Simple Kappa Architecture Pipeline using Streamsets, KSQLDB, KAFKA Http Sink connector and Rest Api
In this tutorial, I'll work with data came from https://carris.tecmic.com/see (is data about Buses in Lisbon Portugal Area) and write streams to kafka and show how use KSQLDB to get data from Streams and create new topic with enriched data and write Http sink connector to send this data to Web Api who send alert to whatsapp users numbers when:

1 - one bus Number is near 1 KM by the user predefined Bus Stop and route and he can know that´s time to get bus


2 - bus status is not in movment(not finish yet);


3 - speed from bus is above limit(not finish yet)


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
        public HttpResponseMessage PostNewMessage([FromBody] ISNEARMYSTOP_STREAM ISNEARMYSTOP_STREAM)
        {
            //if (!ModelState.IsValid)
            //    return BadRequest("Invalid data.");

            HttpResponseMessage response = null;

            const string accountSid = "your_account";
            const string authToken = "your_token";

            string msg = "Boa tarde, o autocarro número " + ISNEARMYSTOP_STREAM.BusNumber + " está a " + ISNEARMYSTOP_STREAM.DISTANCE_KM + " Km de distância da sua paragem.";

            try
            {
                TwilioClient.Init(accountSid, authToken);

                var message = MessageResource.Create(
                    from: new Twilio.Types.PhoneNumber("whatsapp:+14155238886"),
                    body: msg,
                    to: new Twilio.Types.PhoneNumber("whatsapp:+244933198143")
                );

                if (message.ErrorMessage != "")
                {
                    response = Request.CreateResponse(HttpStatusCode.OK);
                    response.Content = new StringContent("Mensagem enviada com sucesso", Encoding.UTF8, "application/json");
                }
                else
                {
                    response = Request.CreateResponse(HttpStatusCode.NoContent);
                    response.Content = new StringContent("Mensagem não enviada com sucesso" + message.ErrorMessage, Encoding.UTF8, "application/json");
                }
            }
            catch (Exception ex)
            {
                response = Request.CreateResponse(HttpStatusCode.InternalServerError);
                response.Content = new StringContent("Ocorreu um erro no servidor " + ex.Message, Encoding.UTF8, "application/json");
            }

            return response;
        }
    }

    public class ISNEARMYSTOP_STREAM
    {
        public Int64 ROWTIME { get; set; }
        public string ROWKEY { get; set; }
        public string TIME { get; set; }
        public int BusNumber { get; set; }
        public string State { get; set; }
        public double Latitude { get; set; }
        public double Longitude { get; set; }
        public string DISTANCE_KM { get; set; }
        public string ROUTENUMBER { get; set; }
        public string DIRECTION { get; set; }

    }
}


```
### Web Api published on Azure
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

* Streams to get data when the almost one bus is near from predefined user busStop by 1 KM 
```
create stream IsNearMyStop_stream with(partitions = 1, VALUE_FORMAT='JSON')
 AS
select TIMESTAMPTOSTRING(ROWTIME, 'HH:mm', 'UTC+1')as time,
       busnumber,
       state,
       lat,
       lng,
       substring(CAST(GEO_DISTANCE(lat, lng, 38.761097, -9.157179, 'KM') AS VARCHAR), 1, 3) AS DISTANCE_KM,
       routenumber, direction
from busLisbonStatus_streams 
where routenumber = '717' and 
      ((direction = 'ASC' and (TIMESTAMPTOSTRING(ROWTIME, 'HH:mm', 'UTC+1') > '19:30' and TIMESTAMPTOSTRING(ROWTIME, 'HH:mm', 'UTC+1') < '20:00')) or 
       (Direction = 'DESC' and TIMESTAMPTOSTRING(ROWTIME, 'HH:mm', 'UTC+1') > '19:30' and TIMESTAMPTOSTRING(ROWTIME, 'HH:mm', 'UTC+1') < '20:00')) and
      (CAST(GEO_DISTANCE(lat, lng, 38.761097, -9.157179, 'KM') AS double) >= 1.0 and
       CAST(GEO_DISTANCE(lat, lng, 38.761097, -9.157179, 'KM') AS double) < 1.3)
emit changes;
 
```

## Part 3 - Sink HTTP Connector to send data to C# Rest Api
*
```
1 - Download from https://www.confluent.io/hub/confluentinc/kafka-connect-http 

2 - Put the Http connector downloaded above in the same folder as the Kafka Connect Http plugin
    Should be <CONFLUENT_HOME>/shared/java

3 - Open Ksql

```

* Sink connector to send message to Web Api and alert bus proximity
```
CREATE SINK CONNECTOR  WHATSAPP_BY_TWILLIO_BUSNEARMYSTOP_SINK with
(
  'topics'                            = 'ISNEARMYSTOP_STREAM',
  'tasks.max'                         =  '1',
  'connector.class'                       = 'io.confluent.connect.http.HttpSinkConnector',
  'http.api.url'                           = 'https://webapiwhatsapp.azurewebsites.net/ChongWhatsappApi/PostNewMessage',
  'key.converter'                           = 'org.apache.kafka.connect.storage.StringConverter',
  'header.converter'                        = 'org.apache.kafka.connect.storage.StringConverter',
  'value.converter'                         = 'org.apache.kafka.connect.storage.StringConverter',
  'confluent.topic.bootstrap.servers'       = 'ec2-18-217-55-153.us-east-2.compute.amazonaws.com:9092',
  'confluent.topic.replication.factor'      = '1',
  'max.retries'                             = '10',
  'confluent.license'                       = '',
  'reporter.bootstrap.servers'              = 'ec2-18-217-55-153.us-east-2.compute.amazonaws.com:9092',
  'reporter.error.topic.replication.factor' = '1',
  'reporter.result.topic.replication.factor'= '1'
);

```

* Sink connector to send message to Web Api and alert when bus isn´t in movement
```
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


* Check log error if exists errors
```
 confluent local log connect tail
 OR
 describe connector conn_name
```


## Part 4 - Starting Streamsets pipeline to write streams to kafka topic

### (see the [Pipeline tutorial](https://github.com/albertochong/PIPELINES-ETL-PROJECTS/blob/master/3%20-%20PIPELINE-LISBON-BUS-STATUS.md) for easy comphreension)
![alt text](https://achong.blob.core.windows.net/gitimages/pipepline_lisbon_bus_status_kafka.PNG)



Checking video demo

<div align="center">
      <a href="https://vimeo.com/417672143">
     <img 
      src="https://achong.blob.core.windows.net/gitimages/WHATSAPP_BY_TWILLIO_SINK_status.PNG" 
      alt="Everything Is AWESOME" 
      style="width:100%;">
      </a>
    </div>
