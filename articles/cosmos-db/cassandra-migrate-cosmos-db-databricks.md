---
title: Migrare i dati da Apache Cassandra a Azure Cosmos DB API Cassandra con databricks (Spark)
description: Informazioni su come eseguire la migrazione dei dati dal database Apache Cassandra a Azure Cosmos DB API Cassandra tramite Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 827abcdb9eb2eb8b7328eb3e3a78f49d51e87e6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349181"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrare i dati da Cassandra a Azure Cosmos DB account API Cassandra usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API Cassandra in Azure Cosmos DB è diventata un'ottima scelta per i carichi di lavoro aziendali in esecuzione su Apache Cassandra per diversi motivi, ad esempio: 

* **Nessun sovraccarico di gestione e monitoraggio:** Elimina il sovraccarico di gestione e monitoraggio di una miriade di impostazioni tra i file di sistema operativo, JVM e YAML e le relative interazioni.

* **Risparmi significativi sui costi:** È possibile risparmiare sui costi con Azure Cosmos DB, che include il costo di VM, larghezza di banda ed eventuali licenze applicabili. Inoltre, non è necessario gestire i Data Center, i server, l'archiviazione SSD, la rete e i costi elettrici. 

* **Possibilità di usare il codice e gli strumenti esistenti:** Azure Cosmos DB fornisce la compatibilità a livello di protocollo wire con gli SDK e gli strumenti Cassandra esistenti. Questa compatibilità consente di usare la base di codici esistente con l'API Cassandra di Azure Cosmos DB apportando solo semplici modifiche.

Esistono diversi modi per eseguire la migrazione dei carichi di lavoro del database da una piattaforma a un'altra. [Azure Databricks](https://azure.microsoft.com/services/databricks/) è un'offerta di piattaforma distribuita come servizio per [Apache Spark](https://spark.apache.org/) che offre un modo per eseguire migrazioni offline su larga scala. Questo articolo descrive i passaggi necessari per eseguire la migrazione dei dati da spazi dei dati/tabelle nativi di Apache Cassandra per Azure Cosmos DB API Cassandra con Azure Databricks.

## <a name="prerequisites"></a>Prerequisiti

* [Effettuare il provisioning di un account API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Esaminare le informazioni di base della connessione all'API Cassandra di Azure Cosmos DB](cassandra-spark-generic.md)

* Per garantire la compatibilità, esaminare le [funzionalità supportate in Azure Cosmos DB API Cassandra](cassandra-support.md) .

* Assicurarsi di aver già creato le tabelle e lo spazio di Azure Cosmos DB vuoti nell'account di API Cassandra di destinazione

* [Usare cqlsh o la shell ospitata per la convalida](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Effettuare il provisioning di un cluster di Azure Databricks

È possibile seguire le istruzioni per eseguire il [provisioning di un cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Si noti tuttavia che Apache Spark 3. x non è attualmente supportata per il connettore Apache Cassandra. Sarà necessario eseguire il provisioning di un runtime di databricks con una versione V2. x supportata di Apache Spark. Si consiglia la versione 6,6 del runtime di databricks:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Runtime di databricks":::


## <a name="add-dependencies"></a>Aggiungere le dipendenze

È necessario aggiungere al cluster la libreria del connettore Apache Spark Cassandra per potersi connettere agli endpoint Cosmos DB Cassandra e nativi. Nel cluster selezionare librerie-> Install New-> Maven-> Search Packages:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pacchetti di ricerca di databricks":::

Digitare `Cassandra` nella casella di ricerca e selezionare il `spark-cassandra-connector` repository maven più recente disponibile, quindi selezionare Install (installa):

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Pacchetti di selezione di databricks":::

> [!NOTE]
> Assicurarsi di riavviare il cluster databricks dopo l'installazione della libreria del connettore Cassandra.

## <a name="create-scala-notebook-for-migration"></a>Creare un notebook scala per la migrazione

Creare un notebook scalare in databricks con il codice seguente. Sostituire le configurazioni Cassandra di origine e di destinazione con le credenziali corrispondenti e le tabelle e gli spazi delle risorse di origine/destinazione, quindi eseguire:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> Le `spark.cassandra.output.concurrent.writes` `connections_per_executor_max` configurazioni e sono importanti per evitare la [limitazione della frequenza](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), che si verifica quando le richieste di Cosmos DB superano la velocità effettiva con provisioning (unità di[richiesta](./request-units.md)). Potrebbe essere necessario modificare queste impostazioni in base al numero di esecutori del cluster Spark e potenzialmente alla dimensione (e quindi al costo UR) di ogni record scritto nelle tabelle di destinazione.

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md) 
* [Procedure consigliate per le chiavi di partizione](partitioning-overview.md#choose-partitionkey)
* [Stimare ur/sec usando gli articoli Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
* [Scalabilità elastica in Azure Cosmos DB API Cassandra](manage-scale-cassandra.md)