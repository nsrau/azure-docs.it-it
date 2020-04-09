---
title: Opzioni di migrazione di Cosmos DB
description: Questo documento descrive le varie opzioni per eseguire la migrazione dei dati locali o cloud in Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984896"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opzioni per eseguire la migrazione dei dati locali o cloud in Azure Cosmos DBOptions to migrate your on-premises or cloud data to Azure Cosmos DB

È possibile caricare dati da varie origini dati in Azure Cosmos DB.You can load data from various data sources to Azure Cosmos DB. Inoltre, poiché il database Cosmos di Azure supporta più API, gli obiettivi possono essere una qualsiasi delle API esistenti. Per supportare i percorsi di migrazione dalle varie origini alle diverse API del database Cosmos di Azure, sono disponibili più soluzioni che forniscono una gestione specializzata per ogni percorso di migrazione. Questo documento elenca le soluzioni disponibili e ne descrive i vantaggi e le limitazioni.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fattori che influiscono sulla scelta dello strumento di migrazione

I seguenti fattori determinano la scelta dello strumento di migrazione:
* **Migrazione online e offline:** molti strumenti di migrazione forniscono un percorso per eseguire una migrazione una sola tantera. Ciò significa che le applicazioni che accedono al database potrebbero verificarsi un periodo di inattività. Alcune soluzioni di migrazione consentono di eseguire una migrazione in tempo reale in cui è presente una pipeline di replica impostata tra l'origine e la destinazione.

* **Origine dati**: I dati esistenti possono trovarsi in varie origini dati come Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL e così via. I dati possono anche trovarsi in un account di database Cosmos di Azure esistente e l'intento della migrazione può essere quello di modificare il modello di dati o ripartizionare i dati in un contenitore con una chiave di partizione diversa.

* **API del database Cosmos**di Azure: per l'API SQL in Azure Cosmos DB, sono disponibili una varietà di strumenti sviluppati dal team di Azure Cosmos DB che facilitano i diversi scenari di migrazione. Tutte le altre API hanno un proprio set specializzato di strumenti sviluppati e gestiti dalla community. Poiché il database Cosmos di Azure supporta queste API a livello di protocollo wire, questi strumenti dovrebbero funzionare così com'è durante la migrazione dei dati anche in Database Cosmos di Azure.Since Azure Cosmos DB supports these APIs at a wire protocol level, these tools should work as-is while migrating data into Azure Cosmos DB too. Tuttavia, potrebbero richiedere una gestione personalizzata per le limitazioni poiché questo concetto è specifico di Azure Cosmos DB.

* **Dimensioni dei dati**: La maggior parte degli strumenti di migrazione funziona molto bene per set di dati più piccoli. Quando il set di dati supera alcune centinaia di gigabyte, le scelte degli strumenti di migrazione sono limitate. 

* **Durata prevista della migrazione:** le migrazioni possono essere configurate in modo che vengano eseguite a un ritmo lento e incrementale che consuma meno velocità effettiva o può utilizzare l'intera velocità effettiva di cui è stato eseguito il provisioning nel contenitore di database Cosmos di Azure di destinazione e completare la migrazione in meno tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dei dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Non adatto per set di dati di grandi dimensioni|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Utilizza la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull;Adatto per set di dati di grandi dimensioni <br/>&bull;Mancanza di checkpoint - Significa che se si verifica un problema durante il corso della migrazione, è necessario riavviare l'intero processo di migrazione<br/>&bull;Mancanza di una coda di messaggi non recapitabili - Significa che alcuni file errati possono interrompere l'intero processo di migrazione.|
|Offline|[Connettore Spark db di Azure CosmosAzure Cosmos DB Spark connector](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Utilizza la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull;Adatto per set di dati di grandi dimensioni <br/>&bull;È necessaria una configurazione Spark personalizzata <br/>&bull;Spark è sensibile alle incoerenze dello schema e questo può essere un problema durante la migrazione |
|Offline|[Strumento personalizzato con libreria dell'esecutore bulk Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Fornisce funzionalità di checkpoint e caratteri inevii che aumentano la resilienza della migrazione <br/>&bull;Adatto per set di dati di grandi dimensioni (10 TB e versioni successive)  <br/>&bull;Richiede l'installazione personalizzata di questo strumento in esecuzione come servizio appRequires custom setup of this tool running as an App Service |
|Online|[Funzioni cosmo DB - API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Facile da configurare <br/>&bull;Funziona solo se l'origine è un contenitore di database Cosmos di Azure <br/>&bull;Non adatto per set di dati di grandi dimensioni <br/>&bull;Non acquisisce le eliminazioni dal contenitore di origine |
|Online|[Servizio di migrazione personalizzato con ChangeFeedCustom Migration Service using ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Fornisce il monitoraggio dello stato di avanzamento <br/>&bull;Funziona solo se l'origine è un contenitore di database Cosmos di Azure <br/>&bull;Funziona anche per set di dati di dimensioni maggiori <br/>&bull;Richiede all'utente di configurare un servizio app per ospitare il processore del feed di modifiche <br/>&bull;Non acquisisce le eliminazioni dal contenitore di origine|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funziona con un'ampia gamma di origini come Oracle, DB2, SQL Server <br/>&bull;Facile da costruire pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull;Supporta set di dati più grandiSupports larger datasets <br/>&bull;Poiché si tratta di uno strumento di terze parti, deve essere acquistato dal marketplace e installato nell'ambiente dell'utente|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dei dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Non adatto per set di dati di grandi dimensioni|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Utilizza la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull;Adatto per set di dati di grandi dimensioni <br/>&bull;La mancanza di checkpoint significa che qualsiasi problema durante il corso della migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull;La mancanza di una coda di messaggi non recapitabili significherebbe che alcuni file errati potrebbero interrompere l'intero processo di migrazione <br/>&bull;Richiede codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati|
|Offline|[Strumenti Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Facile da configurare e integrazione <br/>&bull;Ha bisogno di una gestione personalizzata per le limitazioni|
|Online|[Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Utilizza la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull;Adatto per set di dati di grandi dimensioni e si occupa della replica delle modifiche in tempo reale <br/>&bull;Funziona solo con altre fonti MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra di Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Comando copia cqlsh](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Facile da configurare <br/>&bull;Non adatto per set di dati di grandi dimensioni <br/>&bull;Funziona solo quando la fonte è una tabella Cassandra|
|Offline|[Copia tabella con Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Può utilizzare le funzionalità Spark per parallelizzare la trasformazione e l'inserimento <br/>&bull;Necessaria configurazione con un criterio di ripetizione dei tentativi personalizzato per gestire le limitazioni|
|Online|[Striim (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funziona con un'ampia gamma di origini come Oracle, DB2, SQL Server <br/>&bull;Facile da costruire pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull;Supporta set di dati più grandiSupports larger datasets <br/>&bull;Poiché si tratta di uno strumento di terze parti, deve essere acquistato dal marketplace e installato nell'ambiente dell'utente|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Supporta set di dati più grandiSupports larger datasets <br/>&bull;Poiché si tratta di uno strumento di terze parti, deve essere acquistato dal marketplace e installato nell'ambiente dell'utente|

## <a name="other-apis"></a>Other APIs
Per le API diverse dall'API SQL, dall'API Mongo e dall'API Cassandra, sono disponibili vari strumenti supportati da ognuno degli ecosistemi esistenti dell'API. 

**API di tabella** 
* [Strumento di migrazione dei dati](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [Copia Azzurra](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**API Gremlin**
* [Libreria dell'esecutore in blocco del graficoGraph bulk executor library](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Scintilla](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, provare le applicazioni di esempio che utilizzano la libreria dell'esecutore in blocco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* La libreria dell'executor bulk è integrata nel connettore Cosmos DB Spark, per altre informazioni, vedere l'articolo connettore Azure Cosmos DB Spark.The bulk executor library is integrated into the Cosmos DB Spark connector, to learn more, see [Azure Cosmos DB Spark connector](spark-connector.md) article.  
* Contattare il team del prodotto Azure Cosmos DB aprendo un ticket di supporto nel sottotipo di errore "Advisory General Advisory" e nel sottotipo di errore "Migrazioni di grandi dimensioni) per ulteriore assistenza con migrazioni su larga scala.
