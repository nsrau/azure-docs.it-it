---
title: Opzioni di migrazione Cosmos DB
description: Questo documento descrive le varie opzioni per eseguire la migrazione dei dati locali o cloud a Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: bharathb
ms.openlocfilehash: 0a2423421c6dfda02646546a6e071c8c78396f2c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170685"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opzioni per la migrazione dei dati locali o cloud a Azure Cosmos DB

È possibile caricare dati da diverse origini dati in Azure Cosmos DB. Inoltre, poiché Azure Cosmos DB supporta più API, le destinazioni possono essere qualsiasi API esistente. Per supportare i percorsi di migrazione dalle diverse origini alle diverse API di Azure Cosmos DB, sono disponibili più soluzioni che forniscono una gestione specializzata per ogni percorso di migrazione. Questo documento elenca le soluzioni disponibili e ne descrive i vantaggi e le limitazioni.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fattori che influiscono sulla scelta dello strumento di migrazione

Di seguito sono riportati i fattori che determinano la scelta dello strumento di migrazione:
* **Migrazione in linea**e non in linea: Molti strumenti di migrazione forniscono un percorso per eseguire solo una migrazione unica. Ciò significa che le applicazioni che accedono al database potrebbero riscontrare un periodo di inattività. Alcune soluzioni di migrazione forniscono un modo per eseguire una migrazione in tempo reale in cui è configurata una pipeline di replica tra l'origine e la destinazione.

* **Origine dati**: I dati esistenti possono trovarsi in varie origini dati, ad esempio Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL e così via. I dati possono anche trovarsi in un account di Azure Cosmos DB esistente e lo scopo della migrazione può essere modificare il modello di dati o ripartizionare i dati in un contenitore con una chiave di partizione diversa.

* **API Azure Cosmos DB**: Per l'API SQL in Azure Cosmos DB, è disponibile un'ampia gamma di strumenti sviluppati dal team Azure Cosmos DB che facilitano i diversi scenari di migrazione. Tutte le altre API hanno un proprio set specializzato di strumenti sviluppati e gestiti dalla community. Poiché Azure Cosmos DB supporta queste API a livello di protocollo wire, questi strumenti dovrebbero funzionare così come sono durante la migrazione dei dati anche Azure Cosmos DB. Tuttavia, potrebbero richiedere una gestione personalizzata per le limitazioni, perché questo concetto è specifico per Azure Cosmos DB.

* **Dimensioni dei dati**: La maggior parte degli strumenti di migrazione funziona molto bene per i set di impostazioni più piccoli. Quando il set di dati supera alcune centinaia di Gigabyte, le scelte degli strumenti di migrazione sono limitate. 

* **Durata prevista della migrazione**: Le migrazioni possono essere configurate in modo da avere un ritmo lento e incrementale che consuma meno velocità effettiva o può utilizzare l'intera velocità effettiva di cui è stato effettuato il provisioning sul contenitore Azure Cosmos DB di destinazione e completare la migrazione in meno tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; è facile da configurare e supporta più origini <br/>&bull; non è adatto per i set di impostazioni di grandi dimensioni|
|Offline|[Data factory di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; è facile da configurare e supporta più origini <br/>&bull; utilizza la libreria Azure Cosmos DB Executor bulk <br/>&bull; adatto per set di impostazioni di grandi dimensioni <br/>Mancanza di checkpoint &bull;: significa che se si verifica un problema durante la migrazione, è necessario riavviare l'intero processo di migrazione<br/>&bull; mancanza di una coda di messaggi non recapitabili, significa che alcuni file non corretti possono arrestare l'intero processo di migrazione.|
|Offline|[Connettore Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; utilizza la libreria Azure Cosmos DB Executor bulk <br/>&bull; adatto per set di impostazioni di grandi dimensioni <br/>&bull; necessita di un'installazione personalizzata di Spark <br/>&bull; Spark è sensibile alle incoerenze dello schema e questo può costituire un problema durante la migrazione |
|Offline|[Strumento personalizzato con Cosmos DB libreria Executor bulk](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; fornisce funzionalità di checkpoint e messaggi non recapitabili che aumentano la resilienza della migrazione <br/>&bull; adatto per set di impostazioni di grandi dimensioni (10 TB +)  <br/>&bull; richiede la configurazione personalizzata di questo strumento in esecuzione come servizio app |
|Online|[Funzioni Cosmos DB + API offre](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; facile da configurare <br/>&bull; funziona solo se l'origine è un contenitore di Azure Cosmos DB <br/>&bull; non è adatto per i set di impostazioni di grandi dimensioni <br/>&bull; non acquisisce le eliminazioni dal contenitore di origine |
|Online|[Servizio di migrazione personalizzato con offre](https://aka.ms/CosmosDBMigrationSample)|&bull; fornisce il rilevamento dello stato di avanzamento <br/>&bull; funziona solo se l'origine è un contenitore di Azure Cosmos DB <br/>&bull; funziona anche per set di impostazioni di grandi dimensioni <br/>&bull; richiede all'utente di configurare un servizio app per ospitare il processore del feed delle modifiche <br/>&bull; non acquisisce le eliminazioni dal contenitore di origine|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; funziona con una vasta gamma di origini, ad esempio Oracle, DB2 SQL Server <br/>&bull; è facile creare pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull; supporta set di impostazioni di grandi dimensioni <br/>&bull; poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB l'API Mongo
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; è facile da configurare e supporta più origini <br/>&bull; non è adatto per i set di impostazioni di grandi dimensioni|
|Offline|[Data factory di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; è facile da configurare e supporta più origini <br/>&bull; utilizza la libreria Azure Cosmos DB Executor bulk <br/>&bull; adatto per set di impostazioni di grandi dimensioni <br/>la mancanza di checkpoint &bull; significa che qualsiasi problema durante il corso della migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull; la mancanza di una coda di messaggi non recapitabili significherebbe che alcuni file non corretti avrebbero potuto arrestare l'intero processo di migrazione <br/>&bull; necessita di codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati|
|Offline|[Strumenti di Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; è facile da configurare e integrare <br/>&bull; necessita di una gestione personalizzata per le limitazioni|
|Online|[Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; utilizza la libreria Azure Cosmos DB Executor bulk <br/>&bull; adatto per set di impostazioni di grandi dimensioni e si occupa della replica delle modifiche in tempo reale <br/>&bull; funziona solo con altre origini MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra di Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[comando di copia cqlsh](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; facile da configurare <br/>&bull; non è adatto per i set di impostazioni di grandi dimensioni <br/>&bull; funziona solo quando l'origine è una tabella Cassandra|
|Offline|[Copiare una tabella con Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; può usare le funzionalità Spark per parallelizzare la trasformazione e l'inserimento <br/>&bull; richiede la configurazione con criteri di ripetizione personalizzati per gestire le limitazioni|
|Online|[StriIm (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; funziona con una vasta gamma di origini, ad esempio Oracle, DB2 SQL Server <br/>&bull; è facile creare pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull; supporta set di impostazioni di grandi dimensioni <br/>&bull; poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; supporta set di impostazioni di grandi dimensioni <br/>&bull; poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="other-apis"></a>Altre API
Per le API diverse dall'API SQL, dall'API Mongo e dal API Cassandra, sono disponibili diversi strumenti supportati da ogni ecosistema esistente dell'API. 

**API di tabella** 
* [Strumento di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**API Gremlin**
* [Libreria esecutore bulk del grafo](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Spark Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, provare le applicazioni di esempio che utilizzano la libreria Executor in blocco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* Per altre informazioni, [Azure Cosmos DB](spark-connector.md) vedere l'articolo relativo al connettore Spark per la libreria di esecuzioni bulk in Cosmos DB.  
* Per ulteriori informazioni sulle migrazioni su larga scala, contattare il team del prodotto Azure Cosmos DB aprendo un ticket di supporto con il tipo di problema "generale consultivo" e "migrazioni di grandi dimensioni (TB +)". 
* Usare il [programma Bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/) per accelerare la compilazione o la migrazione delle applicazioni su Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Programma di bootstrap Cosmos DB](https://azurecosmosdb.github.io/CosmosBootstrap/)
