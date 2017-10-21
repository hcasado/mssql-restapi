# What's here?

This is a *prototype* of a simple [ASP.NET Core 2.0](https://docs.microsoft.com/en-us/aspnet/core/getting-started) Web API app that uses [SQL Server Management Objects (SMO)](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) under the covers to provide a RESTful interface for SQL Server running anywhere.

You can run this prototype on Linux, macOS, Windows or Docker and optionally use [environment variables](#environment-variables) to connect to a local or remote SQL Server instance, Azure SQL Database and Azure SQL Data Warehouse.

For fun, I've added ```GET``` REST end-points to:

- navigate from **Server** -> **Databases** -> **Tables** -> **Columns**
- generate ```CREATE DATABASE``` and ```CREATE TABLE``` T-SQL scripts
- view data in tables

Thanks to SMO, the web app dynamically updates database metadata when there are database changes.

Currently, the prototype only supports the ```GET``` verb. I hope to support additional database objects and verbs (```PUT```, ```POST```, ```UPDATE``` and ```DELETE```) in the near future.

## Try it out!

The instructions below are for a MacBook. Modify as needed if you're using Linux or Windows.

### Step 1: Run SQL Server 2017 in Docker

- Download and install Docker for your operating system: <https://www.docker.com>
- Increase Docker memory to 4 GB to run SQL Server 2017 as documented [here](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker#requirements)

Copy & paste the commands below in a ```Terminal``` window to run SQL Server 2017 in Docker.

```
docker pull microsoft/mssql-server-linux:2017-GA

docker run --cap-add SYS_PTRACE \
           -e 'ACCEPT_EULA=Y' \
           -e 'MSSQL_SA_PASSWORD=Yukon900' \
           -e 'MSSQL_PID=Developer' \
           -p 1433:1433 \
           -d microsoft/mssql-server-linux:2017-GA
```

### Step 2: Run the REST API web app in Docker

Copy & paste the commands below in a ```Terminal``` window to run the REST API web app in Docker and connect to SQL Server 2017 running in Docker.

```
docker pull sanagama/mssql-restapi

docker run -it -p 5000:5000 sanagama/mssql-restapi
```

>*NOTE:* The REST API web app uses default values for *server*, *username* and *password* as described in [environment variables](#environment-variables)

### Step 3: Play with the REST API

> *TIP:* [Google Chrome](https://www.google.com/chrome/) with the [JSON Formatter](https://github.com/callumlocke/json-formatter) extension is a great way to play with REST APIs.

- Launch your browser and browse to <http://localhost:5000/api/mssql>
- Click on the links in the JSON response to navigate databases, tables and column objects.


## Run locally with .NET Core

### Step 1: Get the source code

Download and install .NET Core for your operating system: <https://www.microsoft.com/net/core>

> *TIP:* If you have ```Git``` installed then you can do ```git clone https://github.com/sanagama/mssql-restapi.git``` instead.

- Browse to <https://github.com/sanagama/mssql-restapi>
- Click ```Clone or Download``` then click ```Download ZIP```
- Save the ZIP file to your ```HOME``` directory as ```~/mssql-restapi.zip```
- Extract the zip file to your ```HOME``` directory ```~/mssql-restapi```

### Step 2: Run SQL Server 2017 in Docker

Launch a ```Terminal``` window and type the following commands to run SQL Server 2017 in Docker:

```
cd ~/mssql-restapi
cat ./scripts/1-docker-mssql.sh
./scripts/1-docker-mssql.sh
```

### Step 3: Restore sample databases

Type the following commands in the ```Terminal``` window to restore a couple of sample databases to SQL Server 2017 running in Docker:

```
cd ~/mssql-restapi
cat ./scripts/2-docker-create-db.sh
./scripts/2-docker-create-db.sh
```

### Step 4: Run the REST API web app locally

Type the following commands in the ```Terminal``` window to run the REST API web app locally:

```
cd ~/mssql-restapi
dotnet restore
dotnet build
dotnet run
```

## Environment variables

You can pass environment variables to the REST API web app to connect to a local or remote SQL Server instance, Azure SQL Database and Azure SQL Data Warehouse.

Environment variable | Description
--------------- | ------------
**MSSQL_HOST** | Fully qualified server name. Defaults to *127.0.0.1* if not specified.
**MSSQL_PORT** | SQL Server port. Defaults to *1433* if not specified.
**MSSQL_DATABASE** | Initial catalog for the connection. Defaults to *master* if not specified.
**MSSQL_USERNAME** | Username for SQL Server authentication. Defaults to *sa* if not specified.
**MSSQL_PASSWORD** | Password for SQL Server authentication. Defaults to *Yukon900* if not specified.

## Use with Azure SQL Database or Azure SQL Data Warehouse

You can pass environment variables to the REST API web app to connect to Azure SQL Database and Azure SQL Data Warehouse.

>*TIP:* Follow instructions at [Configure a server-level firewall rule](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal#create-a-server-level-firewall-rule) to allow the computer running the REST API web app to connect to your Azure SQL Database.

>*TIP:* Replace *server*, *username* and *password* in the examples below as appropriate for your Azure SQL Database.

Type the following commands in the ```Terminal``` window if you're running the REST API web app in Docker:
```
MSSQL_HOST="<server>.database.windows.net" \
MSSQL_PORT="1433" \
MSSQL_USERNAME="<username>" \
MSSQL_PASSWORD="<password>" \
docker run -it --name 'mssql-restapi' -p 5000:5000 sanagama/mssql-restapi
```

Type the following commands in the ```Terminal``` window if you're running the REST API web app locally:
```
MSSQL_HOST="<server>.database.windows.net" \
MSSQL_PORT="1433" \
MSSQL_USERNAME="<username>" \
MSSQL_PASSWORD="<password>" \
dotnet run
```

## Motivation

My main motivation to create this prototype was to try out the [SQL Server Management Objects (SMO)](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) APIs on .NET Core 2.0.

As you've probably heard, the [SQL Server Management Objects (SMO)](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) APIs are now available on .NET Core 2.0. Developers and system administrators can finally use the nifty SMO APIs in .NET Core 2.0 client apps (like this prototype) or PowerShell cmdlets on Linux, macOS and Windows to programmatically connect to and manage SQL Server running anywhere, Azure SQL Database and Azure SQL Data Warehouse.

Take a look at the [SQL Server Management Objects (SMO) Programming Guide](https://docs.microsoft.com/en-us/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) for samples and API reference documentation.

Profit ;-)
