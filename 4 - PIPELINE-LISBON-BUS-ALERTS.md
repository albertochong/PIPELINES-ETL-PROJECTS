# Simple Kappa Architecture Pipeline using KSQL, KAFKA Sink connector and Rest Api
In this tutorial, we'll see how to use KSQL to get data from joined Streams and create new topic with enriched data and write sink connector to send this data to Rest Api who send alert to whatsapp users numbers when:

1 - one busNumber is arrived to one predefined bustop configured in Ktable.Exampe:users want to know when the bus is one Stop before their stop and receive alert from whatsapp to leave home and get this bus

2 - speed from bus is above limit

3 - bus status is not in movment

### Prerequisites

* A working Confluent Kafka instance (see the [My Confluent Kafka tutorial](https://github.com/albertochong/AWS-KAFKA-CONFLUENT-PLATFORM) for easy local setup and topics creation).
* c# Rest Api 
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
![alt text](https://achong.blob.core.windows.net/gitimages/pipeline_Get_Lisbom_Bus_Status_to_Kafk.PNG)


## Part 2 - KSQL to get streams

### Creating streams  from topic
* NOT FINISHED


## Part 3 - Starting Streamsets pipeline to write streamns to kafka topic

### (see the [Pipeline tutorial](https://github.com/albertochong/PIPELINES-ETL-PROJECTS/blob/master/3%20-%20PIPELINE-LISBON-BUS-STATUS.md) for easy comphreension)
[![Watch the video](https://photos.app.goo.gl/C2sGwkQJpNE5zpaK8)


