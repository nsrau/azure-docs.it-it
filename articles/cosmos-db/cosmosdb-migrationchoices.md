---
title: Opzioni di migrazione Cosmos DB
description: Questo documento descrive le varie opzioni per eseguire la migrazione dei dati locali o cloud a Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984896"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opzioni per la migrazione dei dati locali o cloud a Azure Cosmos DB

È possibile caricare dati da diverse origini dati in Azure Cosmos DB. Inoltre, poiché Azure Cosmos DB supporta più API, le destinazioni possono essere qualsiasi API esistente. Per supportare i percorsi di migrazione dalle diverse origini alle diverse API di Azure Cosmos DB, sono disponibili più soluzioni che forniscono una gestione specializzata per ogni percorso di migrazione. Questo documento elenca le soluzioni disponibili e ne descrive i vantaggi e le limitazioni.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fattori che influiscono sulla scelta dello strumento di migrazione

Di seguito sono riportati i fattori che determinano la scelta dello strumento di migrazione:
* **Migrazione in linea**e non in linea: molti strumenti di migrazione forniscono un percorso per eseguire solo una migrazione unica. Ciò significa che le applicazioni che accedono al database potrebbero riscontrare un periodo di inattività. Alcune soluzioni di migrazione forniscono un modo per eseguire una migrazione in tempo reale in cui è configurata una pipeline di replica tra l'origine e la destinazione.

* **Origine dati**: i dati esistenti possono trovarsi in varie origini dati, ad esempio Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL e così via. I dati possono anche trovarsi in un account di Azure Cosmos DB esistente e lo scopo della migrazione può essere modificare il modello di dati o ripartizionare i dati in un contenitore con una chiave di partizione diversa.

* **Azure Cosmos DB API**: per l'API SQL in Azure Cosmos DB, sono disponibili numerosi strumenti sviluppati dal team Azure Cosmos DB che facilitano i diversi scenari di migrazione. Tutte le altre API hanno un proprio set specializzato di strumenti sviluppati e gestiti dalla community. Poiché Azure Cosmos DB supporta queste API a livello di protocollo wire, questi strumenti dovrebbero funzionare così come sono durante la migrazione dei dati anche Azure Cosmos DB. Tuttavia, potrebbero richiedere una gestione personalizzata per le limitazioni, perché questo concetto è specifico per Azure Cosmos DB.

* **Dimensioni dei dati**: la maggior parte degli strumenti di migrazione funziona molto bene per i set di dati più piccoli. Quando il set di dati supera alcune centinaia di Gigabyte, le scelte degli strumenti di migrazione sono limitate. 

* **Durata della migrazione prevista**: le migrazioni possono essere configurate in modo da avere un ritmo lento e incrementale che consuma meno velocità effettiva o può utilizzare l'intera velocità effettiva di cui è stato effettuato il provisioning sul contenitore Azure Cosmos DB di destinazione e completare la migrazione in meno tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Facile da configurare e supportare più origini <br/>&bull;Non adatto per set di impostazioni di grandi dimensioni|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Facile da configurare e supportare più origini <br/>&bull;Usa la libreria Executor Azure Cosmos DB bulk <br/>&bull;Adatto per set di impostazioni di grandi dimensioni <br/>&bull;Mancanza di Checkpoint: significa che se si verifica un problema durante la migrazione, è necessario riavviare l'intero processo di migrazione<br/>&bull;La mancanza di una coda di messaggi non recapitabili significa che alcuni file non corretti possono arrestare l'intero processo di migrazione.|
|Offline|[Connettore Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Usa la libreria Executor Azure Cosmos DB bulk <br/>&bull;Adatto per set di impostazioni di grandi dimensioni <br/>&bull;Richiede un'installazione personalizzata di Spark <br/>&bull;Spark è sensibile alle incoerenze dello schema e questo può costituire un problema durante la migrazione |
|Offline|[Strumento personalizzato con Cosmos DB libreria Executor bulk](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Fornisce funzionalità di checkpoint e messaggi non recapitabili che aumentano la resilienza della migrazione <br/>&bull;Adatto per set di impostazioni di grandi dimensioni (10 TB +)  <br/>&bull;Richiede l'installazione personalizzata di questo strumento in esecuzione come servizio app |
|Online|[Funzioni Cosmos DB + API offre](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Facile da configurare <br/>&bull;Funziona solo se l'origine è un contenitore di Azure Cosmos DB <br/>&bull;Non adatto per set di impostazioni di grandi dimensioni <br/>&bull;Non acquisisce le eliminazioni dal contenitore di origine |
|Online|[Servizio di migrazione personalizzato con offre](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Fornisce il rilevamento dello stato di avanzamento <br/>&bull;Funziona solo se l'origine è un contenitore di Azure Cosmos DB <br/>&bull;Funziona anche per set di impostazioni di grandi dimensioni <br/>&bull;Richiede all'utente di configurare un servizio app per ospitare il processore del feed delle modifiche <br/>&bull;Non acquisisce le eliminazioni dal contenitore di origine|
|Online|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funziona con una vasta gamma di origini come Oracle, DB2 SQL Server <br/>&bull;Semplifica la creazione di pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull;Supporta set di impostazioni di grandi dimensioni <br/>&bull;Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB l'API Mongo
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Facile da configurare e supportare più origini <br/>&bull;Non adatto per set di impostazioni di grandi dimensioni|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Facile da configurare e supportare più origini <br/>&bull;Usa la libreria Executor Azure Cosmos DB bulk <br/>&bull;Adatto per set di impostazioni di grandi dimensioni <br/>&bull;La mancanza di checkpoint significa che qualsiasi problema durante il corso della migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull;La mancanza di una coda di messaggi non recapitabili significa che alcuni file errati potrebbero arrestare l'intero processo di migrazione <br/>&bull;Richiede codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati|
|Offline|[Strumenti di Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Facile da configurare e integrare <br/>&bull;Richiede la gestione personalizzata per le limitazioni|
|Online|[Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Usa la libreria Executor Azure Cosmos DB bulk <br/>&bull;Adatto per set di impostazioni di grandi dimensioni e si occupa della replica delle modifiche in tempo reale <br/>&bull;Funziona solo con altre origini MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra di Azure Cosmos DB
|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[comando di copia cqlsh](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Facile da configurare <br/>&bull;Non adatto per set di impostazioni di grandi dimensioni <br/>&bull;Funziona solo quando l'origine è una tabella Cassandra|
|Offline|[Copiare una tabella con Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Può usare le funzionalità Spark per parallelizzare la trasformazione e l'inserimento <br/>&bull;Necessità di configurazione con criteri di ripetizione personalizzati per gestire le limitazioni|
|Online|[StriIm (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funziona con una vasta gamma di origini come Oracle, DB2 SQL Server <br/>&bull;Semplifica la creazione di pipeline ETL e fornisce un dashboard per il monitoraggio <br/>&bull;Supporta set di impostazioni di grandi dimensioni <br/>&bull;Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Supporta set di impostazioni di grandi dimensioni <br/>&bull;Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="other-apis"></a>Other APIs
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
