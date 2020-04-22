# Simple Pipeline using KSQL, KAFKA Sink connector and Rest Api
In this tutorial, we'll see how to use KSQL to to get data from joined Streamns and create new topic with enriched data and write sink connector to send this data to Rest Api who send alert to whatsapp number when one bus is arrived to one predefined bustops store in Ktable .

### Prerequisites

* A working Confluent Kafka instance (see the [My Confluent Kafka tutorial](https://github.com/albertochong/AWS-KAFKA-CONFLUENT-PLATFORM) for easy local setup and topics creation).
* Rest Api

## Part 1 - Publishing to a Kafka Producer

### Creating a Pipeline
* Launch the Data Collector console and create a new pipeline.

#### Defining the Source and Destination
* Drag the **Http Client** origin stage, **Kafka Producer** and **Amazon S3** detinations stages into your canvas.

* Fill all relevant fields configurations.

![alt text](https://achong.blob.core.windows.net/gitimages/pipeline_Get_Lisbom_Bus_Status_to_Kafka.PNG)

## Part 2 - Spark with Databricks processing

### Creating a Job
* NOT FINISHED
