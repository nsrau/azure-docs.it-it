---
title: Copiare i dati di Collegamento a Synapse per Azure Cosmos DB in un pool SQL con Apache Spark
description: Caricare i dati in un dataframe Spark, curare i dati e caricarli in una tabella del pool SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 88962d63519cfeb78be694c4f702b05ed4e7d3df
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658344"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Copiare dati da Azure Cosmos DB in un pool SQL con Apache Spark

Collegamento ad Azure Synapse per Azure Cosmos DB consente agli utenti di eseguire analisi quasi in tempo reale su dati operativi in Azure Cosmos DB. Talvolta, tuttavia, è necessario aggregare e arricchire alcuni dati per gli utenti dei data warehouse. Per curare ed esportare i dati di Collegamento a Synapse sono sufficienti poche celle in un notebook.

## <a name="prerequisites"></a>Prerequisiti
* [Effettuare il provisioning di un'area di lavoro di Synapse](../quickstart-create-workspace.md) con:
    * [Pool Spark](../quickstart-create-apache-spark-pool-studio.md)
    * [Pool SQL](../quickstart-create-sql-pool-studio.md)
* [Effettuare il provisioning di un account Cosmos DB con un contenitore HTAP con dati](../../cosmos-db/configure-synapse-link.md)
* [Connettere il contenitore HTAP di Azure Cosmos DB all'area di lavoro](./how-to-connect-synapse-link-cosmos-db.md)
* [Avere la configurazione corretta per importare i dati da Spark a un pool SQL](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Passaggi
In questa esercitazione si effettuerà la connessione all'archivio analitico, in modo da evitare qualsiasi impatto sull'archivio transazionale (non verranno utilizzate unità richiesta). Verranno eseguiti i passaggi seguenti:
1. Leggere il contenitore HTAP di Cosmos DB in un dataframe Spark
2. Aggregare i risultati in un nuovo dataframe
3. Inserire i dati in un pool SQL

[![Passaggi da Spark a SQL](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Dati
Nell'esempio si usa un contenitore HTAP denominato **RetailSales**, che fa parte di un servizio collegato denominato **ConnectedData** e presenta lo schema seguente:
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

Si aggregheranno le vendite (*quantity*, *revenue* (price x quantity) per *productCode* e *weekStarting* a scopo di report. Infine si esporteranno i dati in una tabella del pool SQL denominata **dbo.productsales**.

## <a name="configure-a-spark-notebook"></a>Configurare un notebook Spark
Creare un notebook Spark con Scala (Scala as Spark) come linguaggio principale. Per la sessione si usa l'impostazione predefinita del notebook.

## <a name="read-the-data-in-spark"></a>Leggere i dati in Spark
Nella prima cella leggere il contenitore HTAP di Cosmos DB con Spark in un dataframe.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Aggregare i risultati in un nuovo dataframe

Nella seconda cella si eseguono la trasformazione e le aggregazioni necessarie per il nuovo dataframe prima di caricarlo in un database del pool SQL.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>Caricare i risultati in un pool SQL

Nella terza cella si caricano i dati in un pool SQL. Verranno creati automaticamente una tabella esterna, un'origine dati esterna e un formato di file esterno temporanei che verranno eliminati al termine del processo.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Eseguire query sui risultati con SQL

È possibile eseguire query sul risultato usando una semplice query SQL, ad esempio lo script SQL seguente:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

La query presenterà i risultati seguenti in modalità grafico: [![Passaggi da Spark a SQL](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire query sull'archivio analitico di Azure Cosmos DB con Apache Spark](./how-to-query-analytical-store-spark.md)