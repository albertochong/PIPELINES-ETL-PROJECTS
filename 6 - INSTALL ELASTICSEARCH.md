

![alt text](https://achong.blob.core.windows.net/gitimages/elastic_stack.PNG)

# ELASTICSEARCH Installation and Configuration
Search and analyze your data in real time.

## Reference 
###  https://www.elastic.co/


### Run in terminal

* add an user
```bash

```

* unzip files
```bash
unzip nifi-1.11.4-bin.zip
```

* move to folder opt/nifi and change owner to group and user hadoop
```bash
sudo mv nifi-1.11.4/  /opt/nifi
```

* Go to <HIFI_HOME>/bin folder and edit file nifi-env.sh.Uncommnet java location tou your location
```bash
nano nifi-env.sh
```

* Starting NIFI under <HIFI_HOME>/bin folder
```bash
./nifi.sh run
```

## My environment 
###  http://ec2-3-19-103-254.us-east-2.compute.amazonaws.com:8080/nifi

![alt text](https://achong.blob.core.windows.net/gitimages/nifi_presentation.PNG)

* If you are interested to try this tool email me to beto.chong@gmail.com and I grant access and we can share pipelines and knwoledge
