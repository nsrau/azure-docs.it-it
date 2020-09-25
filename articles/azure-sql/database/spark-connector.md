---
title: Usare il connettore Spark con Microsoft Azure SQL e SQL Server
description: Informazioni su come usare il connettore Spark con il database SQL di Azure, Azure SQL Istanza gestita e SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 36010ff0206ddf9dae08391eb6e4c3dd7762cc10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319334"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Accelerare l'analisi Big Data in tempo reale con il connettore Spark
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A partire da Sep 2020, questo connettore non viene mantenuto attivamente. Tuttavia, [Apache Spark Connector per SQL Server e Azure SQL](https://docs.microsoft.com/sql/connect/spark/connector) è ora disponibile, con supporto per i binding Python e R, un'interfaccia più semplice da usare per l'inserimento bulk dei dati e molti altri miglioramenti. Si consiglia vivamente di valutare e usare il nuovo connettore invece di questo. Le informazioni sul connettore precedente (Questa pagina) vengono mantenute solo a scopo di archiviazione.

Il connettore Spark Abilita i database nel database SQL di Azure, Istanza gestita SQL di Azure e SQL Server per agire come origine dati di input o sink di dati di output per i processi Spark. Consente di utilizzare dati transazionali in tempo reale in Big Data Analytics e di salvare i risultati per query ad hoc o per la creazione di report. Rispetto al connettore JDBC incorporato, questo connettore consente di eseguire l'inserimento bulk dei dati nel database. Può superare l'inserimento riga per riga con prestazioni da 10 a 20 volte più veloci. Il connettore Spark supporta l'autenticazione Azure Active Directory (Azure AD) per connettersi al database SQL di Azure e al Istanza gestita SQL di Azure, consentendo di connettere il database da Azure Databricks usando l'account di Azure AD. Fornisce interfacce simili al connettore JDBC incorporato. È facile eseguire la migrazione dei processi Spark esistenti per l'utilizzo di questo nuovo connettore.

## <a name="download-and-build-a-spark-connector"></a>Scaricare e compilare un connettore Spark

Il repository GitHub per il connettore precedente precedentemente collegato a da questa pagina non viene mantenuto attivamente. Si consiglia invece di valutare e usare il [nuovo connettore](https://github.com/microsoft/sql-spark-connector).

### <a name="official-supported-versions"></a>Versioni ufficiali supportate

| Componente                             | Versione                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 o versione successiva           |
| Scala                                 | 2.10 o versione successiva            |
| Microsoft JDBC Driver per SQL Server  | 6.2 o versione successiva             |
| Microsoft SQL Server                  | SQL Server 2008 o versione successiva |
| Database SQL di Azure                    | Supportato                |
| Istanza gestita di SQL di Azure            | Supportato                |

Il connettore Spark utilizza Microsoft JDBC driver per SQL Server per spostare i dati tra i nodi di lavoro Spark e i database:

Il flusso di dati è il seguente:

1. Il nodo Master Spark si connette ai database nel database SQL o SQL Server e carica i dati da una tabella specifica o usando una query SQL specifica.
2. Il nodo principale Spark distribuisce i dati ai nodi di lavoro per la trasformazione.
3. Il nodo di lavoro si connette ai database che si connettono al database SQL e SQL Server e scrive i dati nel database. L'utente può scegliere se utilizzare l'inserimento riga per riga o l'inserimento in blocco.

Il diagramma seguente illustra il flusso di dati.

   ![Il diagramma mostra il flusso descritto, con un nodo master che si connette direttamente al database e si connette a tre nodi di lavoro, che si connettono al database.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Compilare il connettore Spark

Attualmente il progetto del connettore usa Maven. Per creare il connettore senza le dipendenze, è possibile eseguire:

- mvn clean package
- Scaricare le versioni più recenti del file JAR dalla cartella release
- Includere il file JAR di Spark del database SQL

## <a name="connect-and-read-data-using-the-spark-connector"></a>Connettere e leggere i dati usando il connettore Spark

È possibile connettersi ai database nel database SQL e SQL Server da un processo Spark per leggere o scrivere i dati. È anche possibile eseguire una query DML o DDL nei database nel database SQL e SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Leggere i dati da Azure SQL e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Leggi i dati da Azure SQL e SQL Server con la query SQL specificata

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Scrivere dati in Azure SQL e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Eseguire query DML o DDL in SQL di Azure e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Connettersi da Spark usando l'autenticazione Azure AD

È possibile connettersi al database SQL di Azure e a SQL Istanza gestita usando l'autenticazione Azure AD. Usare l'autenticazione di Azure AD per gestire centralmente le identità degli utenti del database e come alternativa all'autenticazione di SQL Server.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Connessione tramite la modalità di autenticazione ActiveDirectoryPassword

#### <a name="setup-requirement"></a>Requisito di installazione

Se si utilizza la modalità di autenticazione ActiveDirectoryPassword, è necessario scaricare [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e le relative dipendenze e includerli nel percorso di compilazione Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>Connessione tramite un token di accesso

#### <a name="setup-requirement"></a>Requisito di installazione

Se si utilizza la modalità di autenticazione basata su token di accesso, è necessario scaricare [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e le relative dipendenze e includerli nel percorso di compilazione Java.

Per informazioni su come ottenere un token di accesso al database nel database SQL di Azure o in Azure SQL Istanza gestita, vedere [usare l'autenticazione Azure Active Directory per l'autenticazione](authentication-aad-overview.md) .

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Scrittura di dati tramite BULK INSERT

Il connettore JDBC tradizionale scrive i dati nel database usando l'inserimento riga per riga. È possibile usare il connettore Spark per scrivere dati in Azure SQL e SQL Server usando BULK INSERT. Migliora significativamente le prestazioni di scrittura durante il caricamento di grandi set di dati o il caricamento dei dati in tabelle in cui viene utilizzato un indice ColumnStore, quindi orientato alle colonne.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, scaricare il connettore Spark dal [repository GitHub Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) ed esplorare le risorse aggiuntive nel repository:

- [Esempi di notebook Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Script di esempio (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

È possibile anche esaminare la [guida ad Apache Spark SQL, DataFrame e set di dati](https://spark.apache.org/docs/latest/sql-programming-guide.html) e la [documentazione Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).
