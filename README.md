Getting started

Tested on Debian 9 x64
* Docker CE >= 18.03.1-ce
* Docker Compose >= 1.21

Installation
------------
### Install Docker and Docker Compose
* First, install Docker. https://docs.docker.com/install/linux/docker-ce/debian/
* Then, install Docker Compose. https://docs.docker.com/compose/install/

On linux make sure you have the last version of Docker Compose.

From cli go to your home directory
```
$ cd ~
```

Download archive file from github 
https://github.com/ValeruS/docker

Be sure you are in home directory
```
$ cd ~
$ wget https://github.com/ValeruS/docker/raw/master/workdir.tar.gz
$ tar -zxf workdir.tar.gz
$ cd workdir/
```

Build Containers:
```
$ docker-compose up -d
```

Containers:

- elasticsearch  :9200 :9300
- logstash       :9500
- kibana         :5601
- nginxnlb       :8080
- app1           :8081
- app2           :8082
- nginxsrv       :9090
- mysql          :3306
- httpd          :9080


Cherck container to be up and running
```
$ docker ps -a
```

    Name                   Command               State           Ports
-------------------------------------------------------------------------------
app1            node /usr/src/app/index          Up      0.0.0.0:8081->8080/tcp
app2            node /usr/src/app/index          Up      0.0.0.0:8082->8080/tcp
elasticsearch   /docker-entrypoint.sh elas ...   Up      9200/tcp, 9300/tcp
httpd           httpd-foreground                 Up      0.0.0.0:9080->80/tcp
kibana          /docker-entrypoint.sh kibana     Up      0.0.0.0:5601->5601/tcp
logstash        /docker-entrypoint.sh logs ...   Up      0.0.0.0:9500->9500/tcp
mysql           docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp
nginxnlb        nginx -g daemon off;             Up      0.0.0.0:8080->80/tcp
nginxsrv        nginx -g daemon off;             Up      0.0.0.0:9090->80/tcp


Verifying if work:

* nginxnlb load balancing. Upstream srv is app1 and app2 (execute command twice!)
```
$ curl http://localhost:8080
$ curl http://localhost:8080
```

* nginxsrv (nginx installed from Dockerfile)
```
$ curl http://localhost:9090
```

* httpd (mapped web site from host). Better viewed from the browser (http://iphost:9080)
```
$ curl http://localhost:9080
```

* MySQL (mapped DB)
```
$ docker exec -it mysql bash
# mysql -umysql -pmysql
  mysql> select * from sample.EMPLOYEE;
```

* ELK
- Logstash TCP Input. 
- NOTE: You need to inject data into Logstash before being able to configure a Logstash index pattern via the Kibana web UI.
```
$ nc localhost 9500
testasdlfhafh
ctrl+C
```
- Kibana web UI
http://iphost:5601
