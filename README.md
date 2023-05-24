# FIAP Data Ingestion

Hello everyone!

Let's create an amazing repository about data ingestion. I'll use Docker to set up our ecosystem. We need some databases to populate with sample data. We have chosen MySQL as our relational database management system (RDBMS), and Cassandra and MongoDB as our NoSQL databases.

Here are some details about the use case: We are an e-commerce company specializing in shoe sales. We have recently acquired a small competitor e-commerce business and obtained their CSV files containing product and customer data.

Now, we need to populate our databases with this data in order to extract and present it in dashboards. We will automate this process using Apache NiFi to create a data pipeline.

Let's enhance the insights of SalesHub with data analysis!

## Pre-requirements

- Docker (used Docker Desktop 4.19.0 when I write this)

## Diagram Entity Relationship

![DER](/assets/SalesHubDER.drawio.png)

## Setting up Staging Ambient

On root folder run the code `docker-compose up -d` to create our ecosystem.

We are equiped with some GUI to help us manage data.

Graphical User Interface | Database | Address
:-- | :--: | :--:
Adminer | MySQL | http://localhost:8080
Mongo Express | MongoDB | http://localhost:8081
Cassandra Web | Cassandra | http://localhost:3000
Apache NiFi | - | http://localhost:9090/nifi

#### Cassandra WEB

After generate the ecosystem, you need configure Cassandra Web: 
Firstly get CassandraDB ipv4 with command `docker inspect cassandra`, then put the address ipv4 in variable *CASSANDRA_HOST_IPS* at `docker-compose.yml`. Finally, execute `docker rm cassandra-web & docker-compose up -d cassandra-web`.

## Using NiFi to ingest data from APIs

We'll use MongoDB to storage raw data from API calls, Update data about log transactions on Cassandra, and storage data about exchange rate USD to BRL and EUR to BRL on MySQL.

Start preparing Apache NiFi container with mysql connector, to insert data on mysql. Use `docker cp utils/mysql-connector-j-8.0.33.jar nifi:/opt/nifi/nifi-current/lib`

#### Process used to ingest data

![NiFi](/assets/nifi_process.png)
> ImportExchangeRate

InvokeHTTP - Request data from API. </br>
PutMongoRecord - Save raw data on MongoDB. </br>
EvaluateJsonPath - Load attributes with Json data. </br>
PutSQL - Save attributes data on MySQL. </br>