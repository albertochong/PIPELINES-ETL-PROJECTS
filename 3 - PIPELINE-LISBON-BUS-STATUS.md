# Simple Pipeline using StreamSets Data Collector, Rest Api, Amazon S3, Azure Databricks
Creating custom Kafka producers and consumers is often a tedious process that requires manual coding. In this tutorial, we'll see how to use StreamSets Data Collector to create data ingest pipelines to get data from Rest Api and write to Kafka using a Kafka Producer, and read from Kafka topic with Azure Databricks Spark to processing and store messages on Amazon S3.

### Prerequisites

* Rest APi source - https://carris.tecmic.com/index.html
* A working instance of StreamSets Data Collector
* A working Confluent Kafka instance (see the [My Confluent Kafka tutorial](https://github.com/albertochong/AWS-KAFKA-CONFLUENT-PLATFORM) for easy local setup).
* Amazon S3 account
* Azure or AWS Databrciks account

## Part 1 - Publishing to a Kafka Producer

### Creating a Pipeline
* Launch the Data Collector console and create a new pipeline.

#### Defining the Source and Destination
* Drag the Http Client origin stage, Kafka Producer and Amazon S3 detinations stages into your canvas.

* Fill all relevant fields configurations.

![alt text](https://achong.blob.core.windows.net/gitimages/pipeline_Get_Lisbom_Bus_Status_to_Kafka.PNG)

