![alt text](https://achong.blob.core.windows.net/gitimages/cassandra.PNG)

# Installation and Configuration
Search and analyze your data in real time.

## Reference 
###  https://cassandra.apache.org/


### Run in terminal

OBS: check if java is installed,if not you should install JDK8 and Python2.7

* Create/edit-if-exists the datastax.repo file with the command given as follows 
```bash
sudo nano /etc/yum.repos.d/datastax.repo
--Add conntent
[datastax] 
name = DataStax Repository
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0
```

* Install Cassandra
```bash
sudo yum install dsc21
```

* Edit  /etc/cassandra/conf/cassandra.yaml
```bash
sudo nano  /etc/cassandra/conf/cassandra.yaml
--Edit some properties
listen_address: <private-ip-address-of-instance>
broadcast_address: <public-ip-address-of-instance>
rpc_address: 0.0.0.0
broadcast_rpc_address: <public-ip-address-of-instance>
seed_provider:
 - class_name: org.apache.cassandra.locator.SimpleSeedProvider
   parameters:
   - seeds: “<public-ip-address-of-seed-instance>”
```

* we are ready to start the seed node
```bash
sudo service cassandra start
```

* checking status
```bash
nodetool status
```
![alt text]()


* Connecting to CQL
```bash
cqlsh 3.19.103.254
```
![alt text]()
![alt text]()



