

![alt text](https://achong.blob.core.windows.net/gitimages/elastic_stack.PNG)

# ELASTICSEARCH Installation and Configuration
Search and analyze your data in real time.

## Reference 
###  https://www.elastic.co/


### Run in terminal

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

