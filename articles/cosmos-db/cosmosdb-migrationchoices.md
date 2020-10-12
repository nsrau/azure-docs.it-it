---
title: Opzioni di migrazione Cosmos DB
description: Questo documento descrive le varie opzioni per eseguire la migrazione dei dati locali o cloud a Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 4de6d4ba019af75b0f6179b2794ddb6c1e35e0c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030073"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opzioni per la migrazione dei dati locali o cloud a Azure Cosmos DB

È possibile caricare dati da diverse origini dati in Azure Cosmos DB. Poiché Azure Cosmos DB supporta più API, le destinazioni possono essere qualsiasi API esistente. Di seguito sono riportati alcuni scenari in cui si esegue la migrazione dei dati a Azure Cosmos DB:

* Spostare i dati da un contenitore di Azure Cosmos a un altro contenitore nello stesso database o in un database diverso.
* Trasferimento di dati tra contenitori dedicati a contenitori di database condivisi.
* Spostare i dati da un account Azure Cosmos Situato in regione1 a un altro account Azure Cosmos nella stessa area o in un'area diversa.
* Spostare i dati da un'origine, ad esempio archiviazione BLOB di Azure, un file JSON, un database Oracle, Couchbase, DynamoDB a Azure Cosmos DB.

Per supportare i percorsi di migrazione dalle diverse origini alle diverse API di Azure Cosmos DB, sono disponibili più soluzioni che forniscono una gestione specializzata per ogni percorso di migrazione. Questo documento elenca le soluzioni disponibili e ne descrive i vantaggi e le limitazioni.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fattori che influiscono sulla scelta dello strumento di migrazione

Di seguito sono riportati i fattori che determinano la scelta dello strumento di migrazione:

* **Migrazione in linea**e non in linea: molti strumenti di migrazione forniscono un percorso per eseguire solo una migrazione unica. Ciò significa che le applicazioni che accedono al database potrebbero riscontrare un periodo di inattività. Alcune soluzioni di migrazione forniscono un modo per eseguire una migrazione in tempo reale in cui è configurata una pipeline di replica tra l'origine e la destinazione.

* **Origine dati**: i dati esistenti possono trovarsi in varie origini dati, ad esempio Oracle DB2, Datastax Cassanda, database SQL di Azure, PostgreSQL e così via. I dati possono anche trovarsi in un account di Azure Cosmos DB esistente e lo scopo della migrazione può essere modificare il modello di dati o ripartizionare i dati in un contenitore con una chiave di partizione diversa.

* **Azure Cosmos DB API**: per l'API SQL in Azure Cosmos DB, sono disponibili numerosi strumenti sviluppati dal team Azure Cosmos DB che facilitano i diversi scenari di migrazione. Tutte le altre API hanno un proprio set specializzato di strumenti sviluppati e gestiti dalla community. Poiché Azure Cosmos DB supporta queste API a livello di protocollo wire, questi strumenti dovrebbero funzionare così come sono durante la migrazione dei dati anche Azure Cosmos DB. Tuttavia, potrebbero richiedere una gestione personalizzata per le limitazioni, perché questo concetto è specifico per Azure Cosmos DB.

* **Dimensioni dei dati**: la maggior parte degli strumenti di migrazione funziona molto bene per i set di dati più piccoli. Quando il set di dati supera alcune centinaia di Gigabyte, le scelte degli strumenti di migrazione sono limitate. 

* **Durata della migrazione prevista**: le migrazioni possono essere configurate in modo da avere un ritmo lento e incrementale che consuma meno velocità effettiva o può utilizzare l'intera velocità effettiva di cui è stato effettuato il provisioning sul contenitore Azure Cosmos DB di destinazione e completare la migrazione in meno tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB

|Tipo di migrazione|Soluzione|Origini supportate|Destinazioni supportate|Considerazioni|
|---------|---------|---------|---------|---------|
|Offline|[Strumento di migrazione dati](import-data.md)| &bull;File JSON/CSV<br/>&bull;API SQL Azure Cosmos DB<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Archiviazione tabelle<br/>&bull;DynamoDB AWS<br/>&bull;Archiviazione BLOB di Azure|&bull;API SQL Azure Cosmos DB<br/>&bull;API Azure Cosmos DB tables<br/>&bull;File JSON |&bull; Facile da configurare e supportare più origini. <br/>&bull; Non adatto per set di dati di grandi dimensioni.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;File JSON/CSV<br/>&bull;API SQL Azure Cosmos DB<br/>&bull;API Azure Cosmos DB per MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Archiviazione tabelle<br/>&bull;Archiviazione BLOB di Azure <br/> <br/>Vedere l'articolo [Azure Data Factory](../data-factory/connector-overview.md) per altre origini supportate.|&bull;API SQL Azure Cosmos DB<br/>&bull;API Azure Cosmos DB per MongoDB<br/>&bull;File JSON <br/><br/> Vedere l'articolo [Azure Data Factory](../data-factory/connector-overview.md) per altre destinazioni supportate. |&bull; Facile da configurare e supportare più origini.<br/>&bull; Usa la libreria dell'executor Azure Cosmos DB bulk. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni. <br/>&bull; Mancanza di Checkpoint: significa che se si verifica un problema durante la migrazione, è necessario riavviare l'intero processo di migrazione.<br/>&bull; La mancanza di una coda di messaggi non recapitabili significa che alcuni file non corretti possono arrestare l'intero processo di migrazione.|
|Offline|[Connettore Azure Cosmos DB Spark](spark-connector.md)|Azure Cosmos DB API SQL. <br/><br/>È possibile usare altre origini con connettori aggiuntivi dall'ecosistema Spark.| Azure Cosmos DB API SQL. <br/><br/>È possibile usare altre destinazioni con connettori aggiuntivi dall'ecosistema Spark.| &bull; Usa la libreria dell'executor Azure Cosmos DB bulk. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni. <br/>&bull; Richiede una configurazione di Spark personalizzata. <br/>&bull; Spark è sensibile alle incoerenze dello schema e questo può costituire un problema durante la migrazione. |
|Offline|[Strumento personalizzato con Cosmos DB libreria Executor bulk](migrate-cosmosdb-data.md)| L'origine dipende dal codice personalizzato | API SQL Azure Cosmos DB| &bull; Fornisce funzionalità di checkpoint e messaggi non recapitabili che aumentano la resilienza della migrazione. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni (10 TB +).  <br/>&bull; Richiede l'installazione personalizzata di questo strumento in esecuzione come servizio app. |
|Online|[Funzioni Cosmos DB + API offre](change-feed-functions.md)| API SQL Azure Cosmos DB | API SQL Azure Cosmos DB| &bull; Facile da configurare. <br/>&bull; Funziona solo se l'origine è un contenitore Azure Cosmos DB. <br/>&bull; Non adatto per set di dati di grandi dimensioni. <br/>&bull; Non acquisisce le eliminazioni dal contenitore di origine. |
|Online|[Servizio di migrazione personalizzato con offre](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| API SQL Azure Cosmos DB | API SQL Azure Cosmos DB| &bull; Fornisce il rilevamento dello stato di avanzamento. <br/>&bull; Funziona solo se l'origine è un contenitore Azure Cosmos DB. <br/>&bull; Funziona anche per set di impostazioni di grandi dimensioni.<br/>&bull; Richiede all'utente di configurare un servizio app per ospitare il processore del feed delle modifiche. <br/>&bull; Non acquisisce le eliminazioni dal contenitore di origine.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Vedere il [sito Web StriIm](https://www.striim.com/sources-and-targets/) per altre origini supportate. |&bull;API SQL Azure Cosmos DB <br/>&bull; Azure Cosmos DB API Cassandra<br/><br/> Vedere il [sito Web StriIm](https://www.striim.com/sources-and-targets/) per altre destinazioni supportate. | &bull; Funziona con una vasta gamma di origini, ad esempio Oracle, DB2 SQL Server.<br/>&bull; Semplifica la creazione di pipeline ETL e fornisce un dashboard per il monitoraggio. <br/>&bull; Supporta set di impostazioni di grandi dimensioni. <br/>&bull; Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB l'API Mongo

|Tipo di migrazione|Soluzione|Origini supportate|Destinazioni supportate|Considerazioni|
|---------|---------|---------|---------|---------|
|Online|[Servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|API Azure Cosmos DB per MongoDB |&bull; Usa la libreria dell'executor Azure Cosmos DB bulk. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni e si occupa della replica delle modifiche in tempo reale. <br/>&bull; Funziona solo con altre origini MongoDB.|
|Offline|[Servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| API Azure Cosmos DB per MongoDB| &bull; Usa la libreria dell'executor Azure Cosmos DB bulk. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni e si occupa della replica delle modifiche in tempo reale. <br/>&bull; Funziona solo con altre origini MongoDB.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;File JSON/CSV<br/>&bull;API SQL Azure Cosmos DB<br/>&bull;API Azure Cosmos DB per MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Archiviazione tabelle<br/>&bull;Archiviazione BLOB di Azure <br/><br/> Vedere l'articolo [Azure Data Factory](../data-factory/connector-overview.md) per altre origini supportate. | &bull;API SQL Azure Cosmos DB<br/>&bull;API Azure Cosmos DB per MongoDB <br/>&bull; File JSON <br/><br/> Vedere l'articolo [Azure Data Factory](../data-factory/connector-overview.md) per altre destinazioni supportate.| &bull; Facile da configurare e supportare più origini. <br/>&bull; Usa la libreria dell'executor Azure Cosmos DB bulk. <br/>&bull; Adatto per set di impostazioni di grandi dimensioni. <br/>&bull; La mancanza di checkpoint significa che qualsiasi problema nel corso della migrazione richiederebbe un riavvio dell'intero processo di migrazione.<br/>&bull; La mancanza di una coda di messaggi non recapitabili significa che alcuni file errati potrebbero arrestare l'intero processo di migrazione. <br/>&bull; Richiede codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati.|
|Offline|[ Strumenti Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | API Azure Cosmos DB per MongoDB| &bull; Facile da configurare e integrare. <br/>&bull; Richiede la gestione personalizzata per le limitazioni.|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra di Azure Cosmos DB

|Tipo di migrazione|Soluzione|Origini supportate|Destinazioni supportate|Considerazioni|
|---------|---------|---------|---------|---------|
|Offline|[comando di copia cqlsh](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|File CSV | API Cassandra di Azure Cosmos DB| &bull; Facile da configurare. <br/>&bull; Non adatto per set di dati di grandi dimensioni. <br/>&bull; Funziona solo quando l'origine è una tabella Cassandra.|
|Offline|[Copiare una tabella con Spark](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;API Cassandra di Azure Cosmos DB| API Cassandra di Azure Cosmos DB | &bull; Consente di usare le funzionalità Spark per parallelizzare la trasformazione e l'inserimento. <br/>&bull; Richiede la configurazione con criteri di ripetizione personalizzati per gestire le limitazioni.|
|Online|[StriIm (da Oracle DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Vedere il [sito Web StriIm](https://www.striim.com/sources-and-targets/) per altre origini supportate.|&bull;API SQL Azure Cosmos DB<br/>&bull;API Cassandra di Azure Cosmos DB <br/><br/> Vedere il [sito Web StriIm](https://www.striim.com/sources-and-targets/) per altre destinazioni supportate.| &bull; Funziona con una vasta gamma di origini, ad esempio Oracle, DB2 SQL Server. <br/>&bull; Semplifica la creazione di pipeline ETL e fornisce un dashboard per il monitoraggio. <br/>&bull; Supporta set di impostazioni di grandi dimensioni. <br/>&bull; Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Vedere il [sito Web blitzz](https://www.blitzz.io/) per altre origini supportate. |Azure Cosmos DB API Cassandra. <br/><br/>Vedere il [sito Web blitzz](https://www.blitzz.io/) per altre destinazioni supportate. | &bull; Supporta set di impostazioni di grandi dimensioni. <br/>&bull; Poiché si tratta di uno strumento di terze parti, è necessario acquistarlo dal Marketplace e installarlo nell'ambiente dell'utente.|

## <a name="other-apis"></a>Other APIs

Per le API diverse dall'API SQL, dall'API Mongo e dal API Cassandra, sono disponibili diversi strumenti supportati da ogni ecosistema esistente dell'API. 

**API Tabella** 

* [Utilità di migrazione dati](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**API Gremlin**

* [Libreria esecutore bulk del grafo](bulk-executor-graph-dotnet.md)
* [Spark Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, provare le applicazioni di esempio che utilizzano la libreria Executor in blocco in [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* Per altre informazioni, [Azure Cosmos DB](spark-connector.md) vedere l'articolo relativo al connettore Spark per la libreria di esecuzioni bulk in Cosmos DB.  
* Per ulteriori informazioni sulle migrazioni su larga scala, contattare il team del prodotto Azure Cosmos DB aprendo un ticket di supporto con il tipo di problema "generale consultivo" e "migrazioni di grandi dimensioni (TB +)".
