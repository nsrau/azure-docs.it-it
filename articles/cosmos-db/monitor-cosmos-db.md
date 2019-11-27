---
title: Azure Cosmos DB di monitoraggio | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità dei Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 9a36b46d11657ef52051f8bf8df1e4944051da23
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454271"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB di monitoraggio
Quando le applicazioni e i processi aziendali critici si basano sulle risorse di Azure, è opportuno monitorarle per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati da Azure Cosmos database e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Azure Cosmos DB crea dati di monitoraggio tramite [monitoraggio di Azure](../azure-monitor/overview.md) , un servizio di monitoraggio dello stack completo in Azure che fornisce un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale. 

Se non si ha già familiarità con il monitoraggio dei servizi di Azure, iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md) , che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Monitoraggio dei dati raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard in Azure per l'analisi e l'invio di avvisi sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Azure Cosmos DB e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Monitoraggio di Azure per Cosmos DB (anteprima)
[Monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) si basa sulla [funzionalità cartelle di lavoro di monitoraggio di Azure](../azure-monitor/app/usage-workbooks.md) e usa gli stessi dati di monitoraggio raccolti per Cosmos DB descritti nelle sezioni seguenti. Usare questo strumento per visualizzare le prestazioni complessive, gli errori, la capacità e l'integrità operativa di tutte le risorse di Azure Cosmos DB in un'esperienza interattiva unificata e sfruttare le altre funzionalità di monitoraggio di Azure per l'analisi e l'invio di avvisi dettagliati. 

![Monitoraggio di Azure per Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitoraggio dei dati raccolti da Azure Cosmos DB
Azure Cosmos DB raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Per un riferimento dettagliato dei log e delle metriche creati da Azure Cosmos DB, vedere [Azure Cosmos DB riferimento ai dati di monitoraggio](monitor-cosmos-db-reference.md) .

La pagina **Panoramica** nel portale di Azure per ogni database di Azure Cosmos include una breve visualizzazione dell'utilizzo del database, inclusa la richiesta e l'utilizzo della fatturazione oraria. Si tratta di informazioni utili, ma solo una piccola quantità di dati di monitoraggio disponibili. Alcuni di questi dati vengono raccolti automaticamente e disponibili per l'analisi non appena si crea il database, mentre è possibile abilitare la raccolta dati aggiuntiva con alcune configurazioni.

![Pagina di panoramica](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o per inviarli all'esterno di monitoraggio di Azure. Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../azure-monitor/platform/diagnostic-settings.md) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere. Le categorie per Azure Cosmos DB sono elencate di seguito insieme ai dati di esempio.

 * **DataPlaneRequests**: selezionare questa opzione per registrare le richieste back-end per tutte le API che includono gli account SQL, Graph, MongoDB, Cassandra e API Tabella in Azure Cosmos DB. Le proprietà chiave da notare sono: Requestcharge, statusCode, clientIPaddress e partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: selezionare questa opzione per registrare le richieste avviate dall'utente dal front-end per gestire le richieste all'API Azure Cosmos DB per MongoDB. Le richieste MongoDB verranno visualizzate in MongoRequests e in DataPlaneRequests. Le proprietà chiave da notare sono: Requestcharge, opCode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: selezionare questa opzione per registrare il testo della query eseguito. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: selezionare questa opzione per registrare le statistiche delle chiavi di partizione. Attualmente rappresentata con le dimensioni di archiviazione (KB) delle chiavi di partizione. Il log viene emesso con le prime tre chiavi di partizione che occupano la maggior parte dell'archivio dati.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Richieste metrica**: selezionare questa opzione per raccogliere i dati delle metriche da Azure Cosmos DB alle destinazioni nell'impostazione di diagnostica. Si tratta degli stessi dati raccolti automaticamente in metriche di Azure. Raccogliere i dati delle metriche con i log delle risorse per analizzare entrambi i tipi di dati e inviare i dati delle metriche all'esterno di monitoraggio di Azure.

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche
Azure Cosmos DB offre un'esperienza personalizzata per lavorare con le metriche. Per informazioni dettagliate sull'uso di questa esperienza e per l'analisi di diversi scenari di Azure Cosmos DB, vedere [monitorare ed eseguire il debug di Azure Cosmos DB metriche da monitoraggio di Azure](cosmos-db-azure-monitor-metrics.md) .

È possibile analizzare le metriche per Azure Cosmos DB con le metriche di altri servizi di Azure usando Esplora metriche aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md) . Tutte le metriche per Azure Cosmos DB si trovano nello spazio dei nomi **Cosmos DB metriche standard**. Quando si aggiunge un filtro a un grafico, è possibile utilizzare le dimensioni seguenti con queste metriche:

- CollectionName
- DatabaseName
- OperationType
- Area
- StatusCode


## <a name="analyzing-log-data"></a>Analisi dei dati di log
I dati nei log di monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche. Azure Cosmos DB archivia i dati nelle tabelle seguenti.

| tabella | DESCRIZIONE |
|:---|:---|
| AzureDiagnostics | Tabella comune utilizzata da più servizi per archiviare i log delle risorse. I log delle risorse da Azure Cosmos DB possono essere identificati con `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabella comune in cui sono archiviati tutti i record del log attività. 


> [!IMPORTANT]
> Quando si selezionano i **log** dal menu Azure Cosmos DB, log Analytics viene aperto con l'ambito della query impostato sul database corrente di Azure Cosmos. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Per eseguire una query che includa i dati di altri database o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per informazioni dettagliate, vedere [ambito di query di log e intervallo di tempo in monitoraggio di Azure log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics query in monitoraggio di Azure

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca **Log Search (Ricerca log** ) per monitorare i contenitori di Azure Cosmos. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md).

Di seguito sono riportate le query che è possibile usare per monitorare i database di Azure Cosmos.

* Per eseguire una query per tutti i log di diagnostica da Azure Cosmos DB per un periodo di tempo specificato:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Per eseguire una query per i 10 eventi registrati più di recente:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Per eseguire una query per tutte le operazioni, raggruppate per tipo:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Per eseguire una query per tutte le operazioni, raggruppate per risorsa:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Per eseguire una query per tutte le attività utente, raggruppate per risorsa:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Per ottenere tutte le query maggiori di 100 ur unite con dati da **DataPlaneRequests** e **QueryRunTimeStatistics**.

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

* Per eseguire una query per le operazioni che richiedono più di 3 millisecondi:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Per eseguire una query per l'agente che esegue le operazioni:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Per eseguire una query per il momento in cui sono state eseguite le operazioni con esecuzione prolungata:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Per ottenere le statistiche della chiave di partizione per valutare l'asimmetria tra le 3 partizioni principali per l'account del database:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorare Azure Cosmos DB a livello di codice
Le metriche a livello di account disponibili nel portale, ad esempio l'uso delle risorse di archiviazione dell'account e il numero totale di richieste, non sono disponibili tramite le API SQL. È tuttavia possibile recuperare dati di utilizzo a livello di raccolta tramite le API SQL. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

* Per usare l'API REST, [eseguire il comando GET sulla raccolta](https://msdn.microsoft.com/library/mt489073.aspx). Nelle intestazioni x-ms-resource-quota e x-ms-resource-usage della risposta verranno restituite le informazioni relative alla quota e all'utilizzo per la raccolta.
* Per usare .NET SDK, usare il metodo [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), che restituisce un oggetto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenente alcune proprietà d'uso, ad esempio **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e altro ancora.

Per accedere a metriche aggiuntive, usare l'[SDK di Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Insights). Le definizioni delle metriche disponibili possono essere recuperate chiamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Le query per recuperare le singole metriche usano il formato seguente:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Cosmos DB riferimento ai dati di monitoraggio](monitor-cosmos-db-reference.md) per un riferimento dei log e delle metriche creati da Azure Cosmos DB.
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [monitoraggio delle risorse di Azure con](../azure-monitor/insights/monitor-azure-resource.md) monitoraggio di Azure.
