# STREAMSETS Installation and Configuration
The StreamSets DataOps Platform helps you deliver continuous data to every part of your business, 
and handle data drift using a modern approach to data engineering and integration.

## Reference 
###  https://streamsets.com/


![alt text](https://achong.blob.core.windows.net/gitimages/StreamSets.PNG)


### Run in terminal

* Download streamsets under home/user directory. Check if java 1.8 is installed, if not you should install first
```bash
sudo wget https://archives.streamsets.com/datacollector/3.13.0/tarball/streamsets-datacollector-core-3.13.0.tgz
```

* unpack files
```bash
sudo tar xvzf streamsets-datacollector-core-3.13.0.tgz 
```

* move to folder opt/streamsets and change owner to group and user hadoop
```bash
sudo mv streamsets-datacollector-core-3.13.0/ /opt/streamsets
```

* To start streamsets go to instalation folder 
```bash
bin/streamsets dc
```

* You will probably het this error
```bash
Configuration of maximum open file limit is too low: 1024 (expected at least 32768). Please consult https://goo.gl/6dmjXd
```

* To solve this problemn open the file and add 2 lines and then reboot the server and start steamset again
```bash
sudo nano /etc/security/limits.conf
--add this two lines
*      soft    nofile  32768
*      hard    nofile  32768

```

cp systemd/sdc.service /etc/systemd/system/sdc.service
cp systemd/sdc.socket /etc/systemd/system/sdc.socket
groupadd -r sdc && useradd -r -d /opt/streamsets -g sdc -s /sbin/nologin sdc
mkdir /etc/sdc
chown -R sdc:sdc /etc/sdc
mkdir /var/log/sdc
chown sdc:sdc /var/log/sdc
mkdir /var/lib/sdc
chown sdc:sdc /var/lib/sdc
mkdir /var/lib/sdc-resources
chown sdc:sdc /var/lib/sdc-resources
systemctl daemon-reload
systemctl start sdc
systemctl enable sdc
service sdc status

## My environment 
###  http://ec2-3-21-124-86.us-east-2.compute.amazonaws.com:18630/
* If you are interested to try this tool email me to beto.chong@gmail.com and I grant access and we can share pipelines and knwoledge
