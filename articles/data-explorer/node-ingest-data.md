---
title: Inserire dati usando la libreria Node di Esplora dati di Azure
description: In questo articolo descrive come inserire (caricare) i dati in Esplora dati di Azure con Node. js.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 19da42437cfe1d7b63dfed4bd2b30716d691a0e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494490"
---
# <a name="ingest-data-using-the-azure-data-explorer-node-library"></a>Inserire dati usando la libreria Node di Esplora dati di Azure

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora dati di Azure offre due librerie client per Node: una [libreria di inserimento](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) e [una libreria di dati](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Queste librerie consentono di inserire (caricare) i dati in un cluster ed eseguire una query di dati dal codice. In questo articolo, è innanzitutto necessario creare una tabella e i mapping dei dati in un cluster di test. Quindi viene accodato l'inserimento nel cluster e vengono convalidati i risultati.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Oltre a una sottoscrizione di Azure, è necessario quanto segue per completare questo articolo:

* [Un cluster e un database di test](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) installato nel computer di sviluppo

## <a name="install-the-data-and-ingest-libraries"></a>Installare le librerie di dati e di inserimento

Installare *azure-kusto-ingest* e *azure-kusto-data*

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Aggiungere le costanti e le istruzioni import

Importare le classi dalle librerie

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Per autenticare un'applicazione, Esplora dati di Azure usa l'ID tenant di Azure Active Directory. Per trovare l'ID tenant, vedere [Trovare l'ID tenant di Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Impostare i valori per `authorityId`, `kustoUri`, `kustoIngestUri` e `kustoDatabase` prima di eseguire questo codice.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Costruire ora la stringa di connessione. Questo esempio usa l'autenticazione del dispositivo per accedere al cluster. È anche possibile usare il certificato dell'applicazione, la chiave dell'applicazione e l'utente e la password di Azure Active Directory.

Creare la tabella di destinazione e il mapping in un passaggio successivo.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Impostare le informazioni sul file di origine

Importare le classi aggiuntive e impostare le costanti per il file di origine dati. Questo esempio usa un file di esempio ospitato nell'archiviazione BLOB di Azure. Il set di dati di esempio **StormEvents** contiene dati relativi al meteo del [National Center for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Creare una tabella nel cluster di prova

Creare una tabella che corrisponda allo schema dei dati nel file `StormEvents.csv`. Quando viene eseguito, questo codice restituisce un messaggio simile al seguente: *Per accedere, usare un Web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice XXXXXXXXX per eseguire l'autenticazione*. Seguire i passaggi per l'accesso, quindi tornare a eseguire il blocco di codice successivo. I blocchi di codice successivi che stabiliscono una connessione richiederanno di eseguire nuovamente l'accesso.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Definire il mapping di inserimento

Eseguire il mapping dei dati CSV in ingresso sui nomi di colonna e tipi di dati usati durante la creazione della tabella.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Accodare un messaggio per l'inserimento

Accodare un messaggio per eseguire il pull dei dati dall'archiviazione BLOB e inserire i dati in Esplora dati di Azure.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Verificare che la tabella contenga dati

Verificare che i dati siano stati inseriti nella tabella. Attendere da cinque a dieci minuti per l'operazione di inserimento in coda per pianificare l'inserimento e caricare i dati in Esplora dati di Azure. Eseguire quindi il codice seguente per ottenere il numero di record nella tabella `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Eseguire query sulla risoluzione dei problemi

Accedere al [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e connettersi al cluster. Eseguire il comando seguente nel database per verificare la presenza di eventuali errori di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Eseguire il comando seguente per visualizzare lo stato di tutte le operazioni di inserimento nelle ultime quattro ore. Sostituire il nome del database prima dell'esecuzione.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di eseguire gli altri articoli, mantenere le risorse create. In caso contrario, eseguire il comando seguente nel database per pulire la tabella `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query](write-queries.md)
