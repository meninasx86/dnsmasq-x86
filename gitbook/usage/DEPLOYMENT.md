# Deployment

### Download the deployment package
    
You can download the deployment package from web page or command line.

* From web:  
Go to the [release page](https://github.com/elespejo/dnsmasq/releases) of this project. Select the package according to the architecture of your machine.

* From command line:  
```bash
wget https://github.com/elespejo/dnsmasq/releases/download/[VERSION]/dnsmasq-[ARCH]-[VERSION].zip
```
  * VERSION : the release tag  
  * ARCH : the architecture of your machine 

  e.g : Deploy a dnsmasq on a x86 machine with the release 0.5.6 by executing
  ```bash
  wget https://github.com/elespejo/dnsmasq/releases/download/0.5.6/dnsmasq-x86-0.5.6.zip
  ```

### Unzip

```bash
unzip dnsmasq-[ARCH]-[VERSION].zip
cd dnsmasq-imageAPI-[ARCH]
```

### Generate the docker compose file

Docker compose file is used for dnsmasq deployment. Its generation requires three parameters:
* [CONF_PATH] : The absolute path to configuration directory.  
* [LOG_PATH] : The absolute path for storage generated log. This path will be created if it is not existed.  
* [COMP_NAME] : The name of this compose file. This name is used to control the service. Must be **uniqueness**.

```bash
make config CONF=[CONF_PATH] LOG=[LOG_PATH] NAME=[COMP_NAME]
```

e.g : Generate a compose file named `dns.yml` with the configuration in `~/dnsmasq_conf/` and log generated into `~/dnsmasq_log/`.
```bash
cd ~/dnsmasq-x86/
make config CONF=~/dnsmasq_conf/ LOG=~/dnsmasq_log/ NAME=dns
```
Therefore a compose file named `dns.yml` is generated in `~/dnsmasq-imageAPI-x86/compose/`.
```yaml
# dns.yml
services:
  router_dnsmasq:
    cap_add:
    - NET_ADMIN
    image: elespejo/dnsmasq-x86:0.5.6
    network_mode: host
    restart: always
    volumes:
    - source: ~/dnsmasq_conf/dnsmasq
      target: /etc/dnsmasq
      type: bind
    - source: ~/dnsmasq_conf/dnsmasq.d
      target: /etc/dnsmasq.d
      type: bind
    - source: ~/dnsmasq_log
      target: /etc/dnsmasq_log
      type: bind
version: '3.2'
```

### Start the service
Start the service with the name you specified in the config step above.
```bash 
make start NAME=[COMP_NAME]
```
e.g: start service `dns`
```bash
cd dnsmasq-imageAPI-x86/
make start NAME=dns
```
After starting the service successfully, you may see the output similar with the following: 
```
docker-compose -p dns -f ~/dnsmasq-imageAPI-x86/compose/dns.yml up -d
Pulling router_dnsmasq (elespejo/dnsmasq-x86:0.5.6)...
0.5.6: Pulling from elespejo/dnsmasq-x86
4fe2ade4980c: Already exists
5a492975f351: Pull complete
070fe1f3f59a: Pull complete
Digest: sha256:f4682be5a4eb5b740d865eef6bb79f537410739f233e495292f09ffeba1b6344
Status: Downloaded newer image for elespejo/dnsmasq-x86:0.5.6
Creating dns_router_dnsmasq_1 ... done
```

### Restart the service
```bash
make restart NAME=[COMP_NAME]
```
e.g
```bash
make restart NAME=dns
```
After restarting the service successfully, you may see the output similar with the following:
```
docker-compose -p dns -f ~/dnsmasq-imageAPI-x86/compose/dns.yml up -d --force-recreate
Recreating dns_router_dnsmasq_1 ... done
```

### Check status of the service
```bash
make status NAME=[COMP_NAME]
```
e.g,
```bash
make stop NAME=dns
```
You may see the output similar with the following:
```
docker-compose -p dns -f ~/dnsmasq-imageAPI-x86/compose/dns.yml ps
        Name                Command          State      Ports
-------------------------------------------------------------
dns_router_dnsmasq_1   /bin/sh -c ./init      UP
docker-compose -p dns -f ~/dnsmasq-imageAPI-x86/compose/dns.yml logs
Attaching to dns_router_dnsmasq_1
router_dnsmasq_1  | ...
...
```

### Stop the service
```bash
make stop NAME=[COMP_NAME]
```
e.g,
```bash
make stop NAME=dns
```
After stoping the service successfully, you may see the output similar with the following:
```
docker-compose -p dns -f ~/dnsmasq-imageAPI-x86/compose/dns.yml down
Stopping dns_router_dnsmasq_1 ... done
Removing dns_router_dnsmasq_1 ... done
```

### List the services
```bash
make list
```
You may see the output similar with the following:
```
for compose in `ls ~/dnsmasq-imageAPI-x86/compose`;do name=`echo $compose|awk -F "." '{print $1}'`;echo $name;docker-compose -p $name -f ~/dnsmasq-imageAPI-x86/compose/$compose ps;done
dns
Name   Command   State   Ports
------------------------------
...
```

### Remove the compose file
```bash
make remove NAME=[COMP_NAME]
```
e.g,
```bash
make remove NAME=dns
```
You may see the output similar with the following:
```
rm ~/dnsmasq-imageAPI-x86/compose/dns.yml
```
Check whether the remove step successfully:
```bash
ls compose | grep dns
```
