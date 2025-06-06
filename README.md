#### Create Debezium network and pull mssql images 
docker network create debezium-network
docker volume create volume1
docker run -e 'ACCEPT_EULA=Y' \
           -e 'SA_PASSWORD=YourStrong!Passw0rd' \
           -e 'SQLAGENT_ENABLED=true' \
           -v volume1:/var/opt/mssql \
           -p 1433:1433 \
           --name mssql_db \
           --network debezium-network \
           -d mcr.microsoft.com/mssql/server:2019-lastest

#### Create table and enable change data capture

``` sql 
EXEC sys.sp_cdc_enable_db;

EXEC sys.sp_cdc_enable_table  
    @source_schema = 'dbo',
    @source_name = 'table_name',
    @role_name = NULL; 

EXEC sys.sp_cdc_enable_table  
    @source_schema = 'dbo',
    @source_name = 'table_name',
    @role_name = NULL;```
```
#### docker compose up
####
