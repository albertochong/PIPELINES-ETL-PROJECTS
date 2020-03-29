# Simple Pipeline using StreamSets Data Collector, Rest Api, Amazon S3, kafka and KSQL
Creating custom Kafka producers and consumers is often a tedious process that requires manual coding. In this tutorial, we'll see how to use StreamSets Data Collector to create data ingest pipelines to get data from Rest Api and write to Kafka using a Kafka Producer, and read from Kafka with a KSQL queries streams.

The goal of this tutorial is read json files from a Rest APi(about Lisbon Area Bus status near real time) and write them to a Kafka topic using the StreamSets Kafka Producer. We'll then use a KSQL to run queries streams to get .

### Prerequisites

* A working instance of StreamSets Data Collector
* A working Confluent Kafka instance (see the [My Kafka tutorial](https://github.com/albertochong/AWS-KAFKA-CONFLUENT-PLATFORM) for easy local setup)
