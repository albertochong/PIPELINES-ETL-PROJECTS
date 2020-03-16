# APACHE-ETL-PROJECTS
Create ETL projects with some tools like Apache NIFi and Streamsets hosted on AWS machine


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
  * Install SSH
  * Install Java

## 2. Step: StreamSet Installation and Configuration
  * Install and Configure Streamsets 
  
### 2.1 Step: Working with STREAMSETS
  *  
  * 
  *  
  
## 3. Step: Apache NIFI Installation and Configuration
  * Install and Configure NIFI 
  
### 2.1 Step: Working with NIFI
  *  
  * 
  *
  


