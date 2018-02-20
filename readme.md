# Debizium CDC Sample quick start (MAC OS)

Pre-requisites:
+ Docker


Step 1:
Start an Instance of ZooKeeker :
$ docker run -it --rm --name zookeeper -p 2181:2181 -p 2888:2888 -p 3888:3888 debezium/zookeeper:0.7

Step 2:
Start an instance of Kafka :
$ docker run -it --rm --name kafka -p 9092:9092 --link zookeeper:zookeeper debezium/kafka:0.7

Step 3:
Start an instance of MySQL :
$ docker run -it --rm --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser -e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:0.7

Step 4:
Start an instance of PostGres:
$ Docker run -it --rm --name postgres -p 5432:5432 -e POSTGRES_USER=postgresuser -e POSTGRES_PASSWORD=postgrespw -e POSTGRES_DB=inventory debezium/postgres:9.6

Step 5:
Start the Dedizium JDBC Connector :
docker run -it --rm --name connect -p 8083:8083 -p 5005:5005  -e GROUP_ID=1 -e CONFIG_STORAGE_TOPIC=my_connect_configs -e OFFSET_STORAGE_TOPIC=my_connect_offsets --link zookeeper:zookeeper --link kafka:kafka --link mysql:mysql --link postgres:postgres debezium/connect-jdbc-es:0.7

Step 6 :
Start the destination Connector :
$ curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://{LOCAL_IP_ADDRESS}:8083/connectors/ -d @jdbc-sink.json

Step 7 :
Start the source connector :
$ curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://{LOCAL_IP_ADDRESS}:8083/connectors/ -d @source.json

Step 8:
Open a MySQL Client :
docker run -it --rm --name mysqlterm --link mysql --rm mysql:5.7 sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
mysql> use inventory;
mysql> show tables;
SELECT * FROM customers;

Open 9:
Open a PostGres :
$ docker exec -it {container_id} bash
# psql
# \c inventory
inventory=# select * from customers;
