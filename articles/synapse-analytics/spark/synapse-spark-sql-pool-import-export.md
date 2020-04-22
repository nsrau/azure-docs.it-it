---
title: Importare ed esportare dati tra pool di Spark (anteprima) e pool SQL
description: Questo articolo illustra come usare il connettore personalizzato per spostare i dati tra i pool SQL e i pool di Spark (anteprima).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420425"
---
# <a name="introduction"></a>Introduzione

Il connettore di Analisi SQL per Spark è progettato per trasferire i dati in modo efficiente tra i pool di Spark (anteprima) e i pool SQL in Azure Synapse. Questo connettore funziona solo nei pool SQL, non funziona con SQL su richiesta.

## <a name="design"></a>Progettazione

Il trasferimento dei dati tra i pool di Spark e i pool SQL può essere eseguito tramite JDBC. Tuttavia, in due sistemi distribuiti come i pool SQL e Spark, JDBC tende a creare un collo di bottiglia con il trasferimento dei dati seriali.

Il connettore tra i pool di Spark e Analisi SQL è un'implementazione di origine dati per Apache Spark. Usa Azure Data Lake Storage Gen 2 e Polybase nei pool SQL per trasferire i dati in modo efficiente tra il cluster Spark e l'istanza di Analisi SQL.

![Architettura del connettore](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticazione in Azure Synapse Analytics

Azure Synapse Analytics semplifica l'autenticazione tra i sistemi. Un servizio token si connette con Azure Active Directory per ottenere i token di sicurezza da usare per accedere all'account di archiviazione o al server del data warehouse. Per questo motivo, non occorre creare le credenziali o specificarle nell'API del connettore, purché l'autenticazione di AAD sia configurata nell'account di archiviazione e nel server del data warehouse. In caso contrario, è possibile specificare l'autenticazione SQL. Per informazioni più dettagliate, vedere la sezione [Uso](#usage).

## <a name="constraints"></a>Vincoli

- Questo connettore funziona solo in Scala.

## <a name="prerequisites"></a>Prerequisiti

- È necessario il ruolo **db_exporter** nel database o nel pool SQL nel quale e dal quale si vogliono trasferire i dati.

Per creare utenti, connettersi al database e seguire questi esempi:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Per assegnare un ruolo:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Uso

Non è necessario specificare le istruzioni di importazione, in quanto sono preimportate per l'esperienza del notebook.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Trasferimento di dati in o da un pool SQL nel server logico (istanza DW) collegato all'area di lavoro

> [!NOTE]
> **Importazioni non necessarie nell'esperienza del notebook**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API di lettura

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

L'API riportata sopra funziona sia per le tabelle interne (gestite) sia per quelle esterne nel pool SQL.

#### <a name="write-api"></a>API di scrittura

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

dove TableType può essere Constants.INTERNAL o Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

L'autenticazione con Archiviazione di Azure e SQL Server è stata completata

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Se si trasferiscono dati in o da un database o un pool SQL in un server logico esterno all'area di lavoro

> [!NOTE]
> Importazioni non necessarie nell'esperienza del notebook

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API di lettura

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API di scrittura

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Uso dell'autenticazione SQL invece di quella di AAD

#### <a name="read-api"></a>API di lettura

Attualmente il connettore non supporta l'autenticazione basata su token con un pool SQL esterno all'area di lavoro. È necessario usare l'autenticazione SQL.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API di scrittura

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Uso del connettore PySpark

> [!NOTE]
> Questo esempio è valido solo per l'esperienza del notebook.

Supponiamo di avere un "pyspark_df" da scrivere nel data warehouse.

Creare una tabella temporanea usando il dataframe in PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Eseguire una cella Scala nel notebook PySpark usando i comandi magic

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Allo stesso modo, nello scenario di lettura leggere i dati usando Scala e scriverli in una tabella temporanea, quindi usare Spark SQL in PySpark per eseguire query sulla tabella temporanea in un dataframe.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un pool SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Creare un nuovo pool di Apache Spark per un'area di lavoro di Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 