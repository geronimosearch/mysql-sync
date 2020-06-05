# Geronimo MySQL-Elasticsearch-Sync
This tool keep the Elasticsearch index synchronized fully synchronized with a MySQL table. It listens to any change made - insert, update or delete - and performs this action on Elasticsearch.

## About GeronimoSearch
Geronimo or GeronimoSearch is a project dedicated to bring the world of Full-text search to every developer. We make full text search easy and accessible by creating tools, simplifying common workflows, offering automated services and much more.

Feel free to post an issue or to contact us at contact@geronimosearch.com for any question or request, or if you need help with setting up the tool or with your Elasticsearch service. We are interested in your feedback and looking for to add features and improvements to suit better the community's needs.

## The MySQL sync tool
The synchronizer is a process that watches the MySQL server's binlogs and looks for logs regarding the table defined. Each record is mapped a document in a specified Elasticsearch index and linked via the `id` property. This tool is brought in the form a docker container and uses RabbitMQ as its massage broker. Therefore it's recommended to spin it up via `docker-compose`.

## Before getting started
#### Setting up MySQL
In order for the synchronizer to work properly, MySQL server **binlogs** must be turned on and set to `ROW` format. If you're not using any service but manage the MySQL servers yourself, run this query:
```sql 
SET GLOBAL binlog_format = "ROW";
```
If you're using AWS Elasticsearch service you need to set the `binlog_format` property via a *parameter group*.

In addition, the MySQL user must have `REPLICATION SLAVE` and `REPLICATION CLIENT` privileges. Those can be granted:
```sql
GRANT REPLICATION SLAVE, REPLICATION CLIENT, SELECT ON *.* TO 'geronimo'
```

#### Install docker and docker-compose
You can find instructions here: [docker](https://docs.docker.com/get-docker/) and [docker-compose](https://docs.docker.com/compose/install/)

## Setting up
The tool if fully configurable using environment variables:
##### MySQL:
* `MYSQL_HOST` - MySQL host address.
* `MYSQL_USERNAME` - MySQL username. This user must have the right [privileges](#Setting-up-MySQL).
* `MYSQL_PASSWORD` - User password.
* `MYSQL_DB` - Schema name to be watched for changes.
* `MYSQL_TABLE` - Table name to be watched for changes.
##### Elasticsearch:
* `ES_HOST` - Elasticsearch host address.
* `ES_INDEX` - Name of the index which should be synched with the MySQL table.
* `ES_SERVICE` - (Optional) Indicates whether Elasticsearch is a managed service. Possible values are `none` and `aws` if you're using AWS Elasticsearch. Default: none.
* `ES_PORT` - (Optional) Elasticsearch port. Default: 9200.
* `ES_USERNAME` - (Optional) Elasticsearch username.
* `ES_PASSWORD` - (Optional) Elasticsearch password.
* `ES_REGION` - (Optional) AWS Elasticsearch region (mandatory when ES_SERVICE=aws).
##### RabbitMQ:
* `RMQ_HOST` - (Optional) RabbitMQ host address. If you're using the docker-compose settings which is brought here, don't change its value. Default: amqp://rabbitmq
##### Logs:
* `LOG_LEVEL` - (Optional) Log level (debug, warning, info or error). Default: info.
* `LOG_FORMAT` - (Optional) Logs format (json or line). Default: line.