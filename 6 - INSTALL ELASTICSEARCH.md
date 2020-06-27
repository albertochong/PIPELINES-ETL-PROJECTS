

![alt text](https://achong.blob.core.windows.net/gitimages/elastic_stack.PNG)

# ELASTICSEARCH Installation and Configuration
Search and analyze your data in real time.

## Reference 
###  https://www.elastic.co/


### Method 1 to install with tar.gz

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


### Method 2 to install with Yum Repo and run as service with aws user ec2-user

* create a repository and add this content
```bash
sudo nano /etc/yum.repos.d/elastic_stack.repo
[elastic_stack-6.x]
name=Elastic Stack repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

* Installing Elasticsearch
```bash
sudo yum install java-1.8.0-openjdk elasticsearch -y
```

* Reloading
```bash
sudo systemctl daemon-reload
```

* Enable the service
```bash
sudo systemctl enable elasticsearch
```

* Starting the service
```bash
sudo systemctl start elasticsearch
```

* checking status the service
```bash
sudo systemctl status elasticsearch
```

* checking elasticsearch configuration and uncomment lines
```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
network.host: ec2-4-34-44-8.us-east-2.compute.amazonaws.com
http.port: 9200
```

* checking log
```bash
sudo less /var/log/elasticsearch/elasticsearch.log
```

* checking cluster
```bash
curl localhost:9200
```

## Using Kibana

* Installation
```bash
sudo yum install kibana -y
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
sudo less /var/log/messages
```

* checking kibana configuration and uncomment lines
```bash
sudo nano etc/kibana/kibana.yml
server.port: 5601
server.host: "ec2-4-34-44-8.us-east-2.compute.amazonaws.com"
elasticsearch.hosts: ["http://ec2-4-34-44-8.us-east-2.compute.amazonaws.com:9200"]
```



