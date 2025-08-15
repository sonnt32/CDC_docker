# Project Setup Guide

This guide provides step-by-step instructions to set up and run the project using Docker Compose and REST API. Please follow exactly to avoid issues.

## 1. Prerequisites
Before starting, ensure you have:
- Docker (latest stable version)
- Docker Compose (bundled with Docker Desktop)
- Git
- cURL or Postman for API testing

Check installation:
docker --version
docker compose version
git --version

## 2. Directory Structure

```plaintext
project-root/
├── docker-compose.yml       # Docker Compose configuration
├── README.md                 # This setup guide
├── restapi/                  # REST API source code
│   ├── create-connector-oracle.json
│   ├── create-connector-sql-server.json
│   └── ...
└── drivers/
    └── ojdbc11
```
``` bash
directory mount driver Oracle (if you can)

RUN mkdir -p /kafka/connect/jars
ENV CONNECT_PLUGIN_PATH="/kafka/connect/jars,/usr/share/java"
```

## 3. Setup Instructions
1. Clone the repository:
git clone https://github.com/sonnt32/CDC_docker
cd CDC_docker

2. Build and start services:
docker compose up -d --build

3. Verify containers are running:
docker compose ps

4. Check logs (optional):
docker compose logs -f

## 4. Create Database SQL SERVER demo and enable CDC 

```bash
docker exec -it mssql /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'YourStrong!Passw0rd' -Q "
CREATE DATABASE Test_DB;
USE Test_DB;
CREATE TABLE dbo.employee(
  id INT IDENTITY(1001,1) PRIMARY KEY,
  first_name VARCHAR(255),
  last_name  VARCHAR(255),
  email      VARCHAR(255) UNIQUE);
EXEC sys.sp_cdc_enable_db;
EXEC sys.sp_cdc_enable_table @source_schema='dbo',@source_name='employee',@role_name=NULL;"
```



## 5. Create Oracle user

**Demo**
```bash
docker exec -it oracledb bash

source /etc/profile
sqlplus / as sysdba

ALTER SESSION SET CONTAINER = XEPDB1;
ALTER SESSION SET "_oracle_script" = TRUE;

BEGIN
  EXECUTE IMMEDIATE 'DROP USER CDC_USER CASCADE';
  EXCEPTION WHEN OTHERS THEN
    IF SQLCODE != -01918 THEN RAISE; END IF;
END;
/

CREATE USER CDC_USER IDENTIFIED BY "MyOraclePwd123"
DEFAULT TABLESPACE users
TEMPORARY TABLESPACE temp
QUOTA UNLIMITED ON users;

GRANT CONNECT, RESOURCE TO CDC_USER;
GRANT CREATE TABLE, CREATE SEQUENCE, DROP ANY TABLE TO CDC_USER;
GRANT INSERT ANY TABLE, UPDATE ANY TABLE, DELETE ANY TABLE TO CDC_USER;
GRANT SELECT ANY TABLE TO CDC_USER;
GRANT UNLIMITED TABLESPACE TO CDC_USER;

COMMIT;
EXIT;
```
``` bash
create target table
sqlplus -s CDC_USER/"MyOraclePwd123"@XEPDB1 <<'SQL2'
CREATE TABLE SQLSERVER_DBO_EMPLOYEE (
  ID         NUMBER         PRIMARY KEY,
  FIRST_NAME VARCHAR2(255),
  LAST_NAME  VARCHAR2(255),
  EMAIL      VARCHAR2(255)
);
COMMIT;
EXIT;
SQL2
EOF
```



## 6. Once all containers are up and running, you can test the REST API using **Postman**.

1. **Open Postman**.
2. Create a new request.
3. Select the appropriate HTTP method (e.g., GET, POST, PUT, DELETE).
4. Run the connector using **create-connector-oracle.json** to connect Oracle to Kafka, and **create-connector-sql-server.json** to connect MS SQL Server to Kafka.

Or can using curl to create connectors, example:

1. curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" --data-binary @connectors/mssql-source.json
2. curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" --data-binary @connectors/oracle-sink.json

## 7. Run task

```bash
docker compose up -d --build
```


## 8.Additional Notes
- Update `.env` file before starting services if needed.
- Make sure ports in `docker-compose.yml` are not used by other applications.
- If any container fails to start, check its logs with: docker compose logs <service-name>
- 
