---
title: Passaggi preliminari alla migrazione per la migrazione dei dati all'API di Azure Cosmos DB per MongoDBPre-migration steps for data migration to Azure Cosmos DB's API for MongoDB
description: In questo documento viene fornita una panoramica dei prerequisiti per una migrazione dei dati da MongoDB a Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942086"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi preliminari alla migrazione per le migrazioni dei dati da MongoDB all'API di Azure Cosmos DB per MongoDBPre-migration steps for data migrations from MongoDB to Azure Cosmos DB's API for MongoDB

Prima di eseguire la migrazione dei dati da MongoDB (locale o nel cloud) all'API di Azure Cosmos DB per MongoDB, è necessario:Before you migrate your data from MongoDB (on-premises or in the cloud) to Azure Cosmos DB's API for MongoDB, you should:

1. [Leggere le considerazioni chiave sull'utilizzo dell'API di Azure Cosmos DB per MongoDBRead the key considerations about using Azure Cosmos DB's API for MongoDB](#considerations)
2. [Scegliere un'opzione per eseguire la migrazione dei dati](#options)
3. [Stimare la velocità effettiva necessaria per i carichi di lavoroStima the throughput needed for your workloads](#estimate-throughput)
4. [Scegliere una chiave di partizione ottimale per i dati](#partitioning)
5. [Comprendere i criteri di indicizzazione che è possibile impostare sui dati](#indexing)

Se sono già stati completati i prerequisiti precedenti per la migrazione, è possibile eseguire la migrazione dei [dati MongoDB all'API di Azure Cosmos DB per MongoDB usando il servizio Migrazione del database](../dms/tutorial-mongodb-cosmos-db.md)di Azure. Inoltre, se non è stato creato un account, è possibile sfogliare le [guide introduttive](create-mongodb-dotnet.md) che illustrano la procedura per creare un account.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerazioni sull'uso dell'API di Azure Cosmos DB per MongoDBConsiderations when using Azure Cosmos DB's API for MongoDB

Di seguito sono riportate caratteristiche specifiche sull'API di Azure Cosmos DB per MongoDB:The following are specific characteristics about Azure Cosmos DB's API for MongoDB:

- **Modello di capacità:** la capacità del database in Azure Cosmos DB si basa su un modello basato sulla velocità effettiva. Questo modello è basato su Unità di [richiesta al secondo,](request-units.md)ovvero un'unità che rappresenta il numero di operazioni di database che possono essere eseguite su una raccolta in base al secondo. Questa capacità può essere allocata a livello di [database o di raccolta](set-throughput.md)e può essere sottoposta a provisioning in un modello di allocazione o utilizzando il modello [AutoPilot](provision-throughput-autopilot.md).

- **Unità richiesta:** ogni operazione di database ha un costo associato di unità di richiesta (RU) in Azure Cosmos DB. Quando viene eseguito, questo viene sottratto dal livello di unità di richiesta disponibili in un determinato secondo. Se una richiesta richiede più RU rispetto alle RU/s attualmente allocate, sono disponibili due opzioni per risolvere il problema: aumentare la quantità di RU oppure attendere l'avvio del secondo successivo e quindi ripetere l'operazione.

- **Capacità elastica:** la capacità di una determinata raccolta o database può cambiare in qualsiasi momento. Ciò consente al database di adattarsi in modo elastico ai requisiti di velocità effettiva del carico di lavoro.

- **Partizionamento automatico:** Azure Cosmos DB fornisce un sistema di partizionamento automatico che richiede solo una partizione (o una chiave di partizione). Il meccanismo di [partizionamento automatico](partition-data.md) è condiviso tra tutte le API del database Cosmos di Azure e consente di utilizzare dati senza soluzione di continuità e di scalare orizzontalmente tramite la distribuzione orizzontale.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opzioni di migrazione per l'API di Azure Cosmos DB per MongoDBMigration options for Azure Cosmos DB's API for MongoDB

Il servizio di migrazione del database di [Azure per l'API per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) di Azure Cosmos DB offre un meccanismo che semplifica la migrazione dei dati fornendo una piattaforma di hosting completamente gestita, opzioni di monitoraggio della migrazione e gestione automatica delle limitazioni. L'elenco completo delle opzioni è il seguente:

|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Strumento di migrazione dei dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Non adatto per set di dati di grandi dimensioni.|
|Offline|[Data Factory di AzureAzure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Facile da configurare e supporta più fonti <br/>&bull;Utilizza la libreria dell'executor bulk di Azure Cosmos DB <br/>&bull;Adatto per set di dati di grandi dimensioni <br/>&bull;La mancanza di checkpoint significa che qualsiasi problema durante il corso della migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull;La mancanza di una coda di messaggi non recapitabili significherebbe che alcuni file errati potrebbero interrompere l'intero processo di migrazione <br/>&bull;Richiede codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati|
|Offline|[Strumenti Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Facile da configurare e integrazione <br/>&bull;Ha bisogno di una gestione personalizzata per le limitazioni|
|Online|[Servizio di migrazione del database di AzureAzure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Servizio di migrazione completamente gestito.<br/>&bull;Fornisce soluzioni di hosting e monitoraggio per l'attività di migrazione. <br/>&bull;Adatto per set di dati di grandi dimensioni e si occupa della replica delle modifiche in tempo reale <br/>&bull;Funziona solo con altre fonti MongoDB|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Stimare la velocità effettiva necessaria per i carichi di lavoro

In Azure Cosmos DB, il provisioning della velocità effettiva viene eseguito in anticipo e viene misurato in unità di richiesta (RU) al secondo. A differenza delle macchine virtuali o dei server locali, le RU sono facilmente scalabili e verticali in qualsiasi momento. È possibile modificare immediatamente il numero di RU di cui è stato eseguito il provisioning. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](request-units.md).

È possibile usare [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) per determinare la quantità di unità richiesta in base alla configurazione dell'account del database, alla quantità di dati, alle dimensioni del documento e alle letture e scritture necessarie al secondo.

Di seguito sono riportati i fattori chiave che influiscono sul numero di RU richieste:
- **Dimensioni del documento**: con l'aumentare delle dimensioni di un elemento/documento, aumenta anche il numero di RU utilizzate per leggere o scrivere l'elemento/documento.

- **Conteggio proprietà documento**: Il numero di RU utilizzate per creare o aggiornare un documento è correlato al numero, alla complessità e alla lunghezza delle relative proprietà. È possibile ridurre il consumo di unità richiesta per le operazioni di scrittura [limitando il numero di proprietà indicizzate.](mongodb-indexing.md)

- **Modelli di query:** la complessità di una query influisce sul numero di unità richiesta utilizzate dalla query. 

Il modo migliore per comprendere il costo delle query consiste nell'usare dati di esempio in Azure Cosmos DB [ed eseguire query di esempio da MongoDB Shell](connect-mongodb-account.md) usando il `getLastRequestStastistics` comando per ottenere l'addebito della richiesta, che genererà il numero di RU usate:

`db.runCommand({getLastRequestStatistics: 1})`

Questo comando genererà un documento JSON simile al seguente:This command will output a JSON document similar to the following:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

È anche possibile usare [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) per comprendere la frequenza e i modelli delle query eseguite su Azure Cosmos DB. I risultati dei log di diagnostica possono essere inviati a un account di archiviazione, a un'istanza EventHub o ad [Azure Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Scegliere la chiave di partizione
Il partizionamento, noto anche come Partizionamento(sharding), è un punto chiave di considerazione prima della migrazione dei dati. Azure Cosmos DB usa il partizionamento completamente gestito per aumentare la capacità in un database per soddisfare i requisiti di archiviazione e velocità effettiva. Questa funzionalità non necessita dell'hosting o della configurazione dei server di routing.   

In modo analogo, la funzionalità di partizionamento aggiunge automaticamente capacità e ribilancia i dati di conseguenza. Per informazioni dettagliate e consigli sulla scelta della chiave di partizione corretta per i dati, vedere l'articolo Scelta di una chiave di [partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indicizzare i dati
Per impostazione predefinita, Azure Cosmos DB fornisce l'indicizzazione automatica su tutti i dati inseriti. Le funzionalità di indicizzazione fornite da Azure Cosmos DB includono l'aggiunta di indici compositi, indici univoci e indici TTL (Time-to-Live). L'interfaccia di gestione dell'indice è mappata al `createIndex()` comando. Per altre informazioni, vedere [Indicizzazione nell'API di Azure Cosmos DB per MongoDB](mongodb-indexing.md).

[Il servizio Migrazione del database](../dms/tutorial-mongodb-cosmos-db.md) di Azure esegue automaticamente la migrazione delle raccolte MongoDB con indici univoci. Tuttavia, gli indici univoci devono essere creati prima della migrazione. Azure Cosmos DB non supporta la creazione di indici univoci, quando sono già presenti dati nelle raccolte. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione dei dati MongoDB a Cosmos DB utilizzando il servizio di migrazione del database.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Partizionamento in Azure Cosmos DB](partition-data.md)
* [Distribuzione globale in Azure Cosmos DBGlobal Distribution in Azure Cosmos DB](distribute-data-globally.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità di richiesta in Azure Cosmos DBRequest Units in Azure Cosmos DB](request-units.md)
