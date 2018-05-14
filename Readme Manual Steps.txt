#Manual Step by step 

#From cli go to your home directory
cd ~

#download work.tar.gz 
wget https://github.com/ValeruS/docker/raw/master/workdir.tar.gz
tar -zxf work.tar.gz

#Install Elasticsearch, Logstash , Kibana
#First install Elasticsearch
docker run -d -p 9200:9200 -p 9300:9300 -h elasticsearch --name=elasticsearch elasticsearch
docker run -d -p 5601:5601 -h kibana --name=kibana --link elasticsearch:elasticsearch kibana
#Install logstash with diferent config file. (You need to inject data into Logstash: $ nc localhost 9500 and write something, then close)
docker run -d -p 9500:9500 -h logstash --name=logstash --link elasticsearch:elasticsearch -v ~/workdir/config/logstash:/config-dir logstash -f /config-dir/logstash.conf

#Install apache with local static web site at host server
docker run -d -p 9080:80 -h httpd --name=httpd -v ~/workdir/public_html/httpd:/usr/local/apache2/htdocs httpd

#Install DB(Mysql) with static DatBase
docker run -d -p 3306:3306 -h mysql --name=mysql -v ~/workdir/db/mysql_data:/var/lib/mysql -e MYSQL_USER=mysql -e MYSQL_PASSWORD=mysql -e MYSQL_DATABASE=sample -e MYSQL_ROOT_PASSWORD=supersecret mysql

#--(valer/mysql:v1   - docker run -d -h mysqldb --name=mysqldb -v /home/dockerep/db/mysql_data:/var/lib/mysql valer/mysql:v1 -- create personal image)
----------------------------------------------------------------------------------------------------

#Create NLB and test it
#First build nodejs app
cd ~/workdir/workdocker/Dockerfile/application
docker build -t valer/nodejsapp:v1 .

#Install nodejs app
docker run -d -p 8081:8080 -h app1 --name=app1 -e "MESSAGE=First instance" valer/nodejsapp:v1
docker run -d -p 8082:8080 -h app2 --name=app2 -e "MESSAGE=Second instance" valer/nodejsapp:v1
curl http://localhost:8081
curl http://localhost:8082

#Create image for nginx
cd ~/workdir/workdocker/Dockerfile/nginxnlb
docker build -t valer/nginxnlb:v1 .
#Install nginx NLB to work with nodejs app
docker run -d -p 8080:80 -h nginxnlb --name=nginxnlb --link app1:app1 --link app2:app2 -v ~/workdir/config/nginx:/etc/nginx/conf.d valer/nginxnlb:v1

#install nginx from dockerfile with conf and index incorporated
cd ~/workdir/workdocker/Dockerfile/nginxsrv
docker build -t valer/nginxsrv:v1 .
docker run -d -p 9090:80 -h nginxsrv --name=nginxsrv valer/nginxsrv:v1
