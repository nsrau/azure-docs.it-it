---
title: Monitorare i dati Azure Cosmos DB usando le impostazioni di diagnostica di Azure
description: Informazioni su come usare le impostazioni di diagnostica di Azure per monitorare le prestazioni e la disponibilità dei dati archiviati in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sngun
ms.openlocfilehash: 9284fca6a96441ad5e6c23f9c6920ba184e03086
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801419"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorare i dati Azure Cosmos DB usando le impostazioni di diagnostica in Azure

Le impostazioni di diagnostica in Azure vengono usate per raccogliere i log delle risorse. I log delle risorse di Azure vengono generati da una risorsa e forniscono dati avanzati e frequenti sul funzionamento di tale risorsa. Questi log vengono acquisiti per ogni richiesta e sono detti anche "log del piano dati". Alcuni esempi di operazioni del piano dati includono Delete, INSERT e readFeed. Il contenuto di questi log varia in base al tipo di risorsa.

Le metriche della piattaforma e i log attività vengono raccolti automaticamente, mentre è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o inviarli all'esterno di monitoraggio di Azure. È possibile attivare l'impostazione di diagnostica per gli account Azure Cosmos usando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Accedere all'account Azure Cosmos. Aprire il riquadro **impostazioni di diagnostica** e quindi selezionare l'opzione **Aggiungi impostazione di diagnostica** .

1. Nel riquadro **impostazioni di diagnostica** compilare il form con i dettagli seguenti: 

    * **Nome**: immettere un nome per i log da creare.

    * È possibile archiviare i log da **archiviare in un account di archiviazione**, **eseguire il flusso in un hub eventi** o **inviarli a log Analytics**

1. Quando si crea un'impostazione di diagnostica, è necessario specificare la categoria di log da raccogliere. Le categorie di log supportate da Azure Cosmos DB sono elencate di seguito insieme al log di esempio raccolto da essi:

 * **DataPlaneRequests**: selezionare questa opzione per registrare le richieste back-end per tutte le API, che includono gli account SQL, Graph, MongoDB, Cassandra e API Tabella in Azure Cosmos DB. Le proprietà chiave da notare sono: `Requestcharge` ,, `statusCode` `clientIPaddress` e `partitionID` .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: selezionare questa opzione per registrare le richieste avviate dall'utente dal front-end per gestire le richieste all'API Azure Cosmos DB per MongoDB. Questo tipo di log non è disponibile per altri account API. Le proprietà chiave da notare sono: `Requestcharge` , `opCode` . Quando si Abilita MongoRequests nei log di diagnostica, assicurarsi di disattivare il DataPlaneRequests. Verrà visualizzato un log per ogni richiesta effettuata sull'API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: selezionare questa opzione per registrare le richieste avviate dall'utente dal front-end per soddisfare le richieste all'API Azure Cosmos DB per Cassandra. Questo tipo di log non è disponibile per altri account API. Le proprietà chiave da notare sono `operationName` , `requestCharge` , `piiCommandText` . Quando si Abilita CassandraRequests nei log di diagnostica, assicurarsi di disattivare il DataPlaneRequests. Verrà visualizzato un log per ogni richiesta effettuata sull'API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: selezionare questa opzione per registrare il testo della query eseguito. Questo tipo di log è disponibile solo per gli account API SQL.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: selezionare questa opzione per registrare le statistiche delle chiavi di partizione. Attualmente rappresentata con le dimensioni di archiviazione (KB) delle chiavi di partizione. Vedere la sezione [risoluzione dei problemi usando le query di diagnostica di Azure](#diagnostic-queries) di questo articolo. Per query di esempio che usano "PartitionKeyStatistics". Il log viene emesso con le prime tre chiavi di partizione che occupano la maggior parte dell'archivio dati. Questo log contiene dati quali l'ID sottoscrizione, il nome dell'area, il nome del database, il nome della raccolta, la chiave di partizione e le dimensioni di archiviazione in KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: questo log indica il consumo di unità richiesta/sec aggregato per le chiavi di partizione. Attualmente, Azure Cosmos DB segnala solo le chiavi di partizione per gli account dell'API SQL e per le operazioni di lettura/scrittura e stored procedure dei punti. altre API e tipi di operazione non sono supportati. Per le altre API, la colonna chiave di partizione nella tabella del log di diagnostica sarà vuota. Questo log contiene dati quali l'ID sottoscrizione, il nome dell'area, il nome del database, il nome della raccolta, la chiave di partizione, il tipo di operazione e l'addebito della richiesta. Vedere la sezione [risoluzione dei problemi usando le query di diagnostica di Azure](#diagnostic-queries) di questo articolo. Per query di esempio che usano "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: questo log contiene informazioni dettagliate sulle operazioni del piano di controllo, come la creazione di un account, l'aggiunta o la rimozione di un'area, l'aggiornamento delle impostazioni di replica dell'account Questo tipo di log è disponibile per tutti i tipi di API che includono SQL (Core), MongoDB, Gremlin, Cassandra, API Tabella.

* **Richieste**: selezionare questa opzione per raccogliere i dati delle metriche da Azure Cosmos DB alle destinazioni nell'impostazione di diagnostica. Si tratta degli stessi dati raccolti automaticamente in metriche di Azure. Raccogliere i dati delle metriche con i log delle risorse per analizzare entrambi i tipi di dati e inviare i dati delle metriche all'esterno di monitoraggio di Azure.

Per informazioni dettagliate su come creare un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell, vedere l'articolo su come [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Risolvere i problemi relativi alle query di diagnostica

1. Come eseguire una query per le operazioni che richiedono più di 3 millisecondi per l'esecuzione:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Come eseguire una query per l'agente utente che esegue le operazioni:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Come eseguire una query per le operazioni a esecuzione prolungata:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Come ottenere le statistiche della chiave di partizione per valutare l'asimmetria tra le 3 partizioni principali per un account di database:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. Come ricevere addebiti per le query costose?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Come individuare le operazioni che assumono la maggior parte delle UR/sec?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. Come ottenere tutte le query che utilizzano più di 100 ur/sec Uniti con dati da **DataPlaneRequests** e **QueryRunTimeStatistics**.

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Come ottenere gli addebiti per le richieste e la durata di esecuzione di una query?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. Come ottenere la distribuzione per diverse operazioni

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual è la velocità effettiva massima utilizzata da una partizione?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Come ottenere le informazioni relative al consumo di chiavi di partizione ur/s al secondo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Come ottenere il costo della richiesta per una chiave di partizione specifica

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Come ottenere le chiavi di partizione top con la maggior parte delle UR/s utilizzate in un periodo specifico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Come ottenere i log per le chiavi di partizione le cui dimensioni di archiviazione sono maggiori di 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Come ottenere le latenze di replica P99 o P50 per le operazioni, la richiesta di addebito o la lunghezza della risposta?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Come ottenere i log Controlplane?
 
   Ricordarsi di attivare il flag come descritto nell'articolo [disabilitare l'accesso in scrittura ai metadati basati su chiave](audit-control-plane-logs.md#disable-key-based-metadata-write-access) ed eseguire le operazioni usando Azure PowerShell, l'interfaccia della riga di comando di Azure o Azure Resource Manager.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md)
