FROM mysql:latest

ENV MYSQL_ROOT_PASSWORD secret
ENV MYSQL_DATABASE sample
ENV MYSQL_USER mysql
ENV MYSQL_PASSWORD mysql

ADD setup.sql /docker-entrypoint-initdb.d
