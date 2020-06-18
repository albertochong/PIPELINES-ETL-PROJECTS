# DATA PIPELINES -PROJECTS
Create data pipelines and ETL projects with some tools like Apache NIFi and Streamsets hosted on AWS machine


## Create 1 Instance (t2.Xlarge) with the options
* Red Hat Enterprise Linux 8 (HVM), SSD Volume Type 64 bit
* Network: vpc by default + No precense subnet + Public IP Use subnet  setting (enable)
* Storage: Root disk 40 GB + Add Volume 200GB Magnetic (delete on termination)
* Use security group created (ClsEtlProject)
* Create or use new .pem key (copy this file in a secure place)

## Security

### Create a Security Group

* Create new security group with the name: ClsChongNameNodeGroup
```bash
Type              Protocol        Port        Source

All Traffic       All             0 - 65535   Anywhere   

SSH               TCP             22          Anywhere
```

## 1. Step: Install some tools
  * Install wget
  * Install nano
  * Install Java

## 2. Step: StreamSet Installation and Configuration
  * Install and Configure Streamsets 
  
### 2.1 Step: Working with STREAMSETS
  * Create new user form based 
  * 
  *  
  
## 3. Step: SPARK-PIPELINE-LISBON-BUS-ALERTS
  * Simple Kappa Architecture Pipeline using StreamSets Data Collector, Rest Api, Amazon S3, Azure Databricks
  
## 4. Step : PIPELINE-LISBON-BUS-ALERTS 
  * Simple Kappa Architecture Pipeline using KSQL and rest API to send alerts to whatsapp
  
## 5. Step: Apache NIFI Installation and Configuration
  * Install and Configure NIFI 
  
### 5.1 Step: Working with NIFI
  *  
  * 
  *
  
## 6. Step: Elaticsearch Installation and Configuration
  * Install and Configure Elasticsearch 

