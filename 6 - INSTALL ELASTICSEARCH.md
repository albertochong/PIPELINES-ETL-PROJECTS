

![alt text](https://achong.blob.core.windows.net/gitimages/elastic_stack.PNG)

# ELASTICSEARCH Installation and Configuration
Search and analyze your data in real time.

## Reference 
###  https://www.elastic.co/


### Method 1 to install with tar.gz on Red hat linux 8

OBS: check if java is installed,if not you should install java 1.8

* add an user
```bash
sudo adduser user_elasticsearch
```

* create folder to install elasticsearch
```bash
sudo mkdir -p /opt/elasticsearch/node1 
```

* give previlege to the new user we create above
```bash
sudo chown -R user_elasticsearch /opt/elasticsearch
```

* lets download Elasticsearch, unpack files and start 
```bash
sudo su - user_elasticsearch
cd /opt/elasticsearch/node1
 wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.2.tar.gz
 tar -xzf elasticsearch-6.4.2.tar.gz
bin/elasticsearch
```

* checking cluster
```bash
curl localhost:9200
```
![alt text](https://achong.blob.core.windows.net/gitimages/elastic_install.PNG)


### Method 2 to install as Cluster with 1 master node and 2 data Nodes with Yum Repo and run as service with aws Read Hat Linux 8 machine and user ec2-user

* create a repository on 3 machines and add this content
```bash
sudo nano /etc/yum.repos.d/elastic_stack.repo
[elasticsearch]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md

[kibana-7.x]
name=Kibana repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md

```

* Installing java and Elasticsearch on 3 machines
```bash
sudo yum install java-1.8.0-openjdk elasticsearch kibana -y
```

* Reloading on 3 machines
```bash
sudo systemctl daemon-reload
```

* Add internal and external ip n hosta
```bash
nano /etc/hosts
# internal ips
34.68.243.71            nodemaster              masternode.chong.com
34.71.126.7             datanode1               datanode1.chong.com
35.193.136.17           datanode2               datanode2.chong.com

```

* checking elasticsearch configuration on master node and define some configurations properties
where _local_ refers to Any loopback addresses on the system and _site_ refers to Any site-local addresses on the system e.g. IP address of the server.
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml

cluster.name: srvChongElastic
node.name: srvChongElasticMaster
node.master: true
node.data: false
bootstrap.memory_lock: true
network.host: [_local_,_site_] 
http.port: 9200
#Security
xpack.security.enabled: true
#xpack.security.authc.api_key.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12

```

* checking elasticsearch configuration on each data node and define some configurations proerties
where _local_ refers to Any loopback addresses on the system and _site_ refers to Any site-local addresses on the system e.g. IP address of the server.
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml

cluster.name: srvChongElastic
node.name: srvChongElasticdataNode1
node.master: false
node.data: true
bootstrap.memory_lock: true
network.host: [_local_,_site_]
http.port: 9200
discovery.zen.ping.unicast.hosts: ["ip_master_node"]
#Security
xpack.security.enabled: true
#xpack.security.authc.api_key.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```

* Adjusting JVM heap size for up to 50% of your RAM but no more than 32GB (due to Java pointer inefficiency in larger heaps) on 3 machines.
```bash
sudo nano /etc/elasticsearch/jvm.options

-Xms2g
-Xmx2g
```

* Increasing on 3 machines open file descriptor limit.Elasticsearch makes use of a large amount of file descriptors, you must ensure the defined limit is enough otherwise you might end up losing data.
```bash
sudo nano /etc/security/limits.conf

- nofile 65536
```

* Disable Swapping on 3 machines.go to the file service and add LimitMEMLOCK=infinity.Save and close and reload
```bash
sudo nano /usr/lib/systemd/system/elasticsearch.service

[Service]
LimitMEMLOCK=infinity


* Elasticsearch uses a mmapfs directory by default to store its indices. 
Check virtual memory and increase
```bash
sysctl -w vm.max_map_count=262144

```

* Elasticsearch uses a number of thread pools for different types of operations.
It is important that it is able to create new threads whenever needed. Make sure that the number of threads 
that the Elasticsearch user can create is at least 4096.
```bash
sudo nano /etc/security/limits.conf
- nproc 4096

```

* Reloading on 3 machines
```bash
sudo systemctl daemon-reload
```

* On each node configure TLS.Dont forget give rigth permission to the certicates ex chomd 777
Or give permission to user elasticsearch : chown root:elastisearch /etc/elasticsearch/elastic-certificates.p12
```bash
go to /usr/share/elasticsearch and run
bin/elasticsearch-certutil cert -out /etc/elasticsearch/elastic-certificates.p12 -pass ""
```

* Enable the service on 3 machines
```bash
sudo systemctl enable elasticsearch
```

* Starting the service on 3 machines
```bash
sudo systemctl start elasticsearch
```

* checking he service status on 3 machines
```bash
sudo systemctl status elasticsearch
```

* On master node generate random password s for system users or interative
```bash
bin/elasticsearch-setup-passwords auto
```

* checking log
```bash
sudo less /var/log/elasticsearch/elasticsearch.log
```

* checking cluster
```bash
curl localhost:9200
```

* checking lincence
```bash
curl localhost:9200/_license/trial_status
```

* start your trial
```bash
curl -X POST localhost:9200/_license/start_trial?acknowledge=true
```

## Using Kibana

* Installation
```bash
sudo yum install kibana -y
```

* checking kibana configuration and uncomment lines
```bash

sudo nano /etc/kibana/kibana.yml

server.port: 5601
server.host: "your_ip_public"
elasticsearch.hosts: ["ip_master_node", "ip_datanode1", "ip_datanode2"]
```


* Enable the service
```bash
sudo systemctl enable kibana
```

* Start the service
```bash
sudo systemctl start kibana
```

* checking log
```bash
#sudo less /var/log/messages
tail -300f /var/log/messages
```

* Kibana web
```bash
http://ec2-4-34-44-8.us-east-2.compute.amazonaws.com:5601
```
![alt text](https://achong.blob.core.windows.net/gitimages/kibana.PNG)


## Working with Indexes

* Indexes creation

![alt text](https://achong.blob.core.windows.net/gitimages/indexes.PNG)
![alt text](https://achong.blob.core.windows.net/gitimages/index_template.PNG)

* Some operations
![alt text](https://achong.blob.core.windows.net/gitimages/indexe_operation.PNG)

## Working with Documents

* Documents Creation

![alt text](https://achong.blob.core.windows.net/gitimages/documents.PNG)
![alt text](https://achong.blob.core.windows.net/gitimages/documents1.PNG)


## Elasticsearch CAT APIs

* CAT Apis:

![alt text](https://achong.blob.core.windows.net/gitimages/cat.PNG)


* Some operations
![alt text](https://achong.blob.core.windows.net/gitimages/cat_list_Apis.PNG)
![alt text](https://achong.blob.core.windows.net/gitimages/cat_allocation.PNG)


## Elasticsearch Search APIs

* Search Apis:

![alt text](https://achong.blob.core.windows.net/gitimages/search_api.PNG)
![alt text](https://achong.blob.core.windows.net/gitimages/search_query_dsl_api.PNG)
![alt text](https://achong.blob.core.windows.net/gitimages/search_query_context_api.PNG)



* Some operations
![alt text]()
![alt text]()

