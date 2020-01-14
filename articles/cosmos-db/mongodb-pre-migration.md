---
title: Passaggi di pre-migrazione per la migrazione dei dati all'API Azure Cosmos DB per MongoDB
description: Questo documento fornisce una panoramica dei prerequisiti per la migrazione dei dati da MongoDB a Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: ef3d56b4ec7e4dbe5f6f4097fdd5d8d125b074dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932297"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passaggi di pre-migrazione per la migrazione dei dati da MongoDB all'API di Azure Cosmos DB per MongoDB

Prima di eseguire la migrazione dei dati da MongoDB (in locale o nel cloud) a Azure Cosmos DB API per MongoDB, è necessario:

1. [Leggere le considerazioni principali sull'uso dell'API di Azure Cosmos DB per MongoDB](#considerations)
2. [Scegliere un'opzione per eseguire la migrazione dei dati](#options)
3. [Stimare la velocità effettiva necessaria per i carichi di lavoro](#estimate-throughput)
4. [Scegliere una chiave di partizione ottimale per i dati](#partitioning)
5. [Informazioni sui criteri di indicizzazione che è possibile impostare sui dati](#indexing)

Se sono già stati completati i prerequisiti precedenti per la migrazione, è possibile [eseguire la migrazione dei dati di MongoDB all'API di Azure Cosmos DB per MongoDB usando il servizio migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db.md). Inoltre, se non è stato creato un account, è possibile esplorare tutte le [guide introduttive](create-mongodb-dotnet.md).

## <a id="considerations"></a>Considerazioni principali sull'uso dell'API di Azure Cosmos DB per MongoDB

Di seguito sono riportate alcune caratteristiche specifiche sull'API Azure Cosmos DB per MongoDB:
- **Modello di capacità**: la capacità del Database in Azure Cosmos DB si basa su un modello basato sulla velocità effettiva. Questo modello è basato su [unità richiesta al secondo](request-units.md), ovvero un'unità che rappresenta il numero di operazioni di database che possono essere eseguite su una raccolta in base al secondo. Questa capacità può essere allocata a [livello di database o di raccolta](set-throughput.md)ed è possibile eseguirne il provisioning in un modello di allocazione o utilizzando il [modello Autopilot](provision-throughput-autopilot.md).
- **Unità richiesta**: a ogni operazione di database è associato un costo delle unità richiesta (UR) in Azure Cosmos DB. Quando viene eseguito, questo viene sottratto dal livello delle unità richiesta disponibili su un determinato secondo. Se per una richiesta sono richieste più unità richiesta rispetto a quelle attualmente allocate, le due opzioni aumentano la quantità di ur o attendono fino al successivo avvio, quindi riprovare a eseguire l'operazione.
- **Capacità elastica**: la capacità di una raccolta o di un database specifico può cambiare in qualsiasi momento. Ciò consente al database di adattarsi in modo elastico ai requisiti di velocità effettiva del carico di lavoro.
- Partizionamento **orizzontale automatico**: Azure Cosmos DB fornisce un sistema di partizionamento automatico che richiede solo una chiave di partizione (o partizionamento). Il [meccanismo di partizionamento automatico](partition-data.md) è condiviso tra tutte le API Azure Cosmos DB e consente di eseguire facilmente i dati e la scalabilità tramite la distribuzione orizzontale.

## <a id="options"></a>Opzioni di migrazione per l'API di Azure Cosmos DB per MongoDB

Il [servizio migrazione del database di Azure per l'API Azure Cosmos DB per MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornisce un meccanismo che semplifica la migrazione dei dati offrendo una piattaforma di hosting completamente gestita, opzioni di monitoraggio della migrazione e gestione automatica delle limitazioni. L'elenco completo delle opzioni è il seguente:

|**Tipo di migrazione**|**Soluzione**|**Considerazioni**|
|---------|---------|---------|
|Offline|[Utilità di migrazione dati](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; facile da configurare e supportare più origini <br/>&bull; non è adatto per i set di impostazioni di grandi dimensioni.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; facile da configurare e supportare più origini <br/>&bull; usa la libreria Azure Cosmos DB Executor bulk <br/>&bull; appropriato per set di impostazioni di grandi dimensioni <br/>&bull; mancanza di checkpoint significa che qualsiasi problema durante il corso della migrazione richiederebbe il riavvio dell'intero processo di migrazione<br/>&bull; mancanza di una coda di messaggi non recapitabili significherebbe che alcuni file non corretti avrebbero potuto arrestare l'intero processo di migrazione <br/>&bull; necessita di codice personalizzato per aumentare la velocità effettiva di lettura per determinate origini dati|
|Offline|[Strumenti di Mongo esistenti (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; facile da configurare e integrare <br/>&bull; necessita di una gestione personalizzata per le limitazioni|
|Online|[Servizio Migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; servizio di migrazione completamente gestito.<br/>&bull; fornisce soluzioni di hosting e monitoraggio per l'attività di migrazione. <br/>&bull; adatto per set di impostazioni di grandi dimensioni e si occupa della replica delle modifiche in tempo reale <br/>&bull; funziona solo con altre origini MongoDB|


## <a id="estimate-throughput"></a>Stimare la velocità effettiva necessaria per i carichi di lavoro

In Azure Cosmos DB, il provisioning della velocità effettiva viene eseguito in anticipo e viene misurato in unità richiesta (UR) al secondo. Diversamente dalle macchine virtuali o dai server locali, le UR sono facili da scalare in qualsiasi momento. È possibile modificare immediatamente il numero di ur con provisioning. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](request-units.md).

È possibile utilizzare il [calcolatore della capacità di Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) per determinare la quantità di unità richiesta in base alla configurazione dell'account del database, alla quantità di dati, alle dimensioni del documento e alle letture e scritture richieste al secondo.

Di seguito sono riportati i fattori chiave che influiscono sul numero di ur richieste:
- **Dimensioni del documento**: con l'aumentare delle dimensioni di un elemento o di un documento, aumenta anche il numero di ur utilizzate per la lettura o la scrittura dell'elemento o del documento.
- **Conteggio proprietà documento**: il numero di ur utilizzate per creare o aggiornare un documento è correlato al numero, alla complessità e alla lunghezza delle relative proprietà. È possibile ridurre il consumo delle unità richiesta per le operazioni di scrittura [limitando il numero di proprietà indicizzate](mongodb-indexing.md).
- **Modelli di query**: la complessità di una query influiscono sul numero di unità richiesta utilizzate dalla query. 

Il modo migliore per comprendere il costo delle query consiste nell'usare i dati di esempio in Azure Cosmos DB [ed eseguire query di esempio dalla shell di MongoDB](connect-mongodb-account.md) usando il comando `getLastRequestStastistics` per ottenere l'addebito della richiesta, che restituirà il numero di ur utilizzate:

`db.runCommand({getLastRequestStatistics: 1})`

Tramite questo comando verrà restituito un documento JSON simile al seguente:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

È anche possibile usare [le impostazioni di diagnostica](cosmosdb-monitor-resource-logs.md) per comprendere la frequenza e i modelli delle query eseguite su Azure Cosmos DB. I risultati dei log di diagnostica possono essere inviati a un account di archiviazione, a un'istanza di EventHub o a un [log Analytics di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a id="partitioning"></a>Scegliere la chiave di partizione
Il partizionamento, noto anche come partizionamento orizzontale, è un punto chiave di considerazione prima della migrazione dei dati. Azure Cosmos DB usa il partizionamento completamente gestito per aumentare la capacità di un database per soddisfare i requisiti di archiviazione e velocità effettiva. Questa funzionalità non richiede l'hosting o la configurazione dei server di routing.   

Analogamente, la funzionalità di partizionamento aggiunge automaticamente la capacità e bilancia nuovamente i dati di conseguenza. Per informazioni dettagliate e consigli sulla scelta della chiave di partizione corretta per i dati, vedere l' [articolo scelta di una chiave di partizione](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indicizzare i dati
Per impostazione predefinita, Azure Cosmos DB fornisce l'indicizzazione automatica su tutti i dati inseriti. Le funzionalità di indicizzazione fornite da Azure Cosmos DB includono l'aggiunta di indici compositi, indici univoci e indici TTL (time-to-Live). Viene eseguito il mapping dell'interfaccia di gestione degli indici al comando `createIndex()`. Per altre informazioni, vedere [indicizzazione nell'API Azure Cosmos DB per MongoDB](mongodb-indexing.md).

Il [servizio migrazione del database di Azure](../dms/tutorial-mongodb-cosmos-db.md) esegue automaticamente la migrazione delle raccolte MongoDB con indici univoci. Tuttavia, gli indici univoci devono essere creati prima della migrazione. Azure Cosmos DB non supporta la creazione di indici univoci, quando sono già presenti dati nelle raccolte. Per altre informazioni, vedere [Chiavi univoche in Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire la migrazione dei dati di MongoDB a Cosmos DB usando il servizio migrazione del database.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisioning della velocità effettiva nei contenitori e nei database di Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partizionamento in Azure Cosmos DB)
* [Distribuzione globale in Azure Cosmos DB](distribute-data-globally.md)
* [Indicizzazione in Azure Cosmos DB](index-overview.md)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
