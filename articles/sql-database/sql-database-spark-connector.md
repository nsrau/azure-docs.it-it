---
title: Connettore Spark con database SQL di Azure e SQL Server | Documentazione Microsoft
description: Informazioni su come usare il connettore Spark per Database SQL di Azure e SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: a422f65097466e4bbe5740c449d3ccf88701802b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650163"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Consente di accelerare il processo di big data analytics in tempo reale con connettore Spark per database SQL di Azure e SQL Server

Il connettore Spark per database SQL di Azure e SQL Server consente ai database SQL, tra cui database SQL di Azure e SQL Server, come origine dati in entrata o come sink di dati in uscita per i processi di Spark. È possibile utilizzare dati transazionali in tempo reale nel processo di big data analytics e mantenere i risultati per query ad hoc o per la generazione di report. Rispetto al connettore JDBC incorporato, questo connettore offre la possibilità di inserire dati in grandi quantità nei database SQL. È possibile ottenere eccezionali prestazioni di inserimento di una riga alla volta con una velocità maggiore di 10 o 20 volte. Il connettore Spark per Database SQL di Azure e SQL Server supporta anche l’autenticazione AAD. Consente la connessione sicura al database SQL di Azure da Azure Databricks utilizzando il proprio account AAD. Fornisce interfacce simili al connettore JDBC incorporato. È facile eseguire la migrazione dei processi Spark esistenti per l'utilizzo di questo nuovo connettore.

## <a name="download"></a>Download
Per iniziare, scaricare il connettore Spark per database SQL dal repository [repository azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) in GitHub.

## <a name="official-supported-versions"></a>Versioni supportate ufficialmente

| Componente                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 o versione successiva           |
| Scala                                |2.10 o versione successiva            |
| Microsoft JDBC Driver per SQL Server |6.2 o versione successiva             |
| Microsoft SQL Server                 |SQL Server 2008 o versione successiva |
| database SQL di Azure                   |Supportato                |

Il connettore Spark per database SQL di Azure e SQL Server utilizza Microsoft JDBC Driver per SQL Server per trasferire dati tra i nodi di lavoro Spark e i database SQL:
 
Il flusso di dati è il seguente:
1. Il nodo principale Spark si connette a SQL Server o al database SQL di Azure e carica i dati da una tabella specifica o utilizzando una query SQL specifica
2. Il nodo principale Spark distribuisce i dati ai nodi di lavoro per la trasformazione. 
3. Il nodo di lavoro si connette a SQL Server o al database SQL di Azure e scrive i dati sul database. L'utente può scegliere se utilizzare l'inserimento riga per riga o l'inserimento in blocco.

Il diagramma seguente illustra il flusso di dati.

   ![architettura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Creare il connettore Spark per il database SQL
Attualmente il progetto del connettore usa Maven. Per creare il connettore senza le dipendenze, è possibile eseguire:

- mvn clean package
- Scaricare le versioni più recenti del file JAR dalla cartella release
- Includere il JAR Spark del database SQL

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Connettere Spark al database SQL con il connettore
È possibile connettersi al database SQL di Azure o SQL Server dai processi di Spark, leggere o scrivere dati. È anche possibile eseguire una query DML o DDL in un database SQL di Azure o SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Leggere dati dal database SQL di Azure o SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>La lettura dei dati dal Database SQL di Azure o SQL Server con query SQL specificata
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
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Scrivere dati nel database SQL di Azure o SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Eseguire una query DML o DDL nel database SQL di Azure o SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Connettere Spark a un database SQL di Azure con l'autenticazione AAD
È possibile connettersi al database SQL di Azure mediante l'autenticazione di Azure Active Directory (AAD). Utilizzare l'autenticazione AAD per gestire centralmente le identità degli utenti del database e come alternativa all'autenticazione di SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Connessione tramite la modalità di autenticazione ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisiti di installazione
Se si utilizza la modalità di autenticazione ActiveDirectoryPassword, è necessario scaricare [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e le relative dipendenze e includerli nel percorso di compilazione Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Connessione tramite token di accesso
#### <a name="setup-requirement"></a>Requisiti di installazione
Se si utilizza la modalità di autenticazione basata su token di accesso, è necessario scaricare [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e le relative dipendenze e includerli nel percorso di compilazione Java.

Vedere [Utilizzare l’autenticazione di Azure Active Directory per l'autenticazione con il database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) per informazioni su come ottenere token di accesso per il database SQL di Azure.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Scrivere dati nel database SQL di Azure o SQL Server tramite inserimento in blocco
Il connettore jdbc tradizionali scrive dati nel database SQL di Azure o SQL Server tramite l'inserimento riga per riga. È possibile utilizzare il connettore da Spark al database SQL per scrivere dati nel database SQL tramite l’inserimento in blocco. Migliora significativamente le prestazioni di scrittura durante il caricamento di grandi set di dati o il caricamento dei dati in tabelle in cui viene utilizzato un indice ColumnStore, quindi orientato alle colonne.

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
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Passaggi successivi
Se ancora non lo si è fatto, scaricare il connettore Spark per il database Azure SQL dal [repository GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) ed esplorare le risorse aggiuntive nel repository:

-   [Esempi di notebook Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Script di esempio (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

È possibile anche esaminare la [guida ad Apache Spark SQL, DataFrame e set di dati](http://spark.apache.org/docs/latest/sql-programming-guide.html) e la [documentazione Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

