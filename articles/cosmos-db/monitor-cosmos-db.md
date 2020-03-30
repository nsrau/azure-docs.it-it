---
title: Monitoraggio di Azure Cosmos DB Documenti Microsoft
description: Informazioni su come monitorare le prestazioni e la disponibilità di Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250321"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitoraggio di Azure Cosmos DBMonitoring Azure Cosmos DB
Quando si dispone di applicazioni e processi aziendali critici che si basano sulle risorse di Azure, si vuole monitorare tali risorse per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati dai database Cosmos di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare e avvisare questi dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Il database Cosmos di Azure crea dati di monitoraggio usando [Monitoraggio di Azure,](../azure-monitor/overview.md) che è un servizio di monitoraggio dello stack completo in Azure che offre un set completo di funzionalità per monitorare le risorse di Azure oltre alle risorse in altri cloud e in locale. 

Se non si ha già familiarità con il monitoraggio dei servizi di Azure, iniziare con l'articolo Monitoraggio delle risorse di Azure con Monitoraggio di Azure Monitor che descrive quanto segue:If you're not familiar with monitoring Azure services, start with the article [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) which describes the following:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Monitoraggio dei dati raccolti in AzureMonitoring data collected in Azure
- Configurazione della raccolta dati
- Strumenti standard in Azure per l'analisi e gli avvisi sui dati di monitoraggioStandard tools in Azure for analyzing and alerting on monitoring data

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Azure Cosmos DB e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.The following sections build on this article by describing the specific data gathered from Azure Cosmos DB and providing examples for configuring data collection and analyzing this data with Azure tools.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Monitoraggio di Azure per il database Cosmos (anteprima)Azure Monitor for Cosmos DB (Preview)
[Monitor di Azure per il](../azure-monitor/insights/cosmosdb-insights-overview.md) database Cosmos di Azure si basa sulla funzionalità delle cartelle di lavoro di Monitoraggio di [Azure](../azure-monitor/app/usage-workbooks.md) e usa gli stessi dati di monitoraggio raccolti per Cosmos DB descritti nelle sezioni seguenti. Usare questo strumento per visualizzare le prestazioni complessive, gli errori, la capacità e l'integrità operativa di tutte le risorse di Azure Cosmos DB in un'esperienza interattiva unificata e sfruttare le altre funzionalità di Monitoraggio di Azure per l'analisi dettagliata e gli avvisi. 

![Azure Monitor for Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche a livello di operazione per Azure Cosmos DBView operation level metrics for Azure Cosmos DB

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Selezionare **Monitor** nella barra di spostamento a sinistra e selezionare **Metriche**.

   ![Riquadro Metriche in Monitoraggio di Azure](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione**richiesta e il gruppo **di risorse**. Per **Tipo di risorsa**selezionare **Account di Azure Cosmos DB**, scegliere uno degli account Cosmos di Azure esistenti e selezionare **Applica**.

   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Successivamente è possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altri. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria.](monitor-cosmos-db-reference.md) In questo esempio, selezioniamo **Unità di richiesta** e Media **come** valore di aggregazione.

   Oltre a questi dettagli, puoi anche selezionare **l'intervallo** di tempo e la **granularità temporale** delle metriche. Al massimo, puoi visualizzare le metriche per gli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico basato sul filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dal portale di AzureChoose a metric from the Azure portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È inoltre possibile filtrare le metriche e il grafico visualizzato da un **oggetto CollectionName**, **DatabaseName**, **OperationType**, **Region**e **StatusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà richiesta, ad esempio **OperationType,** e selezionare un valore quale **Query**. Nel grafico vengono quindi visualizzate le unità di richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite Stored procedure non vengono registrate in modo che non siano disponibili nella metrica OperationType.

![Aggiungere un filtro per selezionare la granularità metricaAdd a filter to select the metric granularity](./media/monitor-cosmos-db/add-metrics-filter.png)

È possibile raggruppare le metriche utilizzando l'opzione **Applica divisione.** Ad esempio, è possibile raggruppare le unità di richiesta per tipo di operazione e visualizzare il grafico per tutte le operazioni contemporaneamente, come illustrato nell'immagine seguente:For example, you can group the request units per operation type and view the graph for all the operations once as shown in the following image:

![Aggiungere il filtro di divisione di applica](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Ecco un altro esempio per visualizzare le metriche di latenza lato server per un database, un contenitore o un'operazione specifica:Here is another example to view the server-side latency metrics for a specific database, container, or an operation:

![Metriche di latenza lato serverServer-side latency metrics](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitoraggio dei dati raccolti da Azure Cosmos DBMonitoring data collected from Azure Cosmos DB

Il database Cosmos di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritti in Monitoraggio dei dati dalle risorse di Azure.Azure Cosmos DB collects the same kinds of monitoring data as other Azure resources which are described in [Monitoring data from Azure resources](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Per informazioni di riferimento dettagliate sui log e sulle metriche create da Azure Cosmos DB, vedere Informazioni di riferimento sui dati di monitoraggio di [Azure Dbs.See Azure Cosmos DB monitoring data reference](monitor-cosmos-db-reference.md) for a detailed reference of the logs and metrics created by Azure Cosmos DB.

La pagina **Panoramica** nel portale di Azure per ogni database Cosmos di Azure include una breve visualizzazione dell'utilizzo del database, tra cui la richiesta e l'utilizzo della fatturazione oraria. Si tratta di informazioni utili, ma solo una piccola quantità dei dati di monitoraggio disponibili. Alcuni di questi dati vengono raccolti automaticamente e disponibili per l'analisi non appena si crea il database, mentre è possibile abilitare la raccolta di dati aggiuntivi con alcune configurazioni.

![Pagina di panoramica](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche

Azure Cosmos DB offre un'esperienza personalizzata per l'utilizzo delle metriche. Per informazioni dettagliate sull'uso di questa esperienza e sull'analisi di diversi scenari di database Cosmos di Azure, vedere Monitor ed eseguire il debug delle [metriche di Azure Cosmos DB da Monitoraggio di Azure.See Monitor](cosmos-db-azure-monitor-metrics.md) and debug Azure Cosmos DB metrics from Azure Monitor for details on using this experience and for analyzing different Azure Cosmos DB scenarios.

È possibile analizzare le metriche per il database Cosmos di Azure con metriche di altri servizi di Azure usando Esplora metriche aprendo **Metriche** dal menu **Monitoraggio di Azure.You** can analyze metrics for Azure Cosmos DB with metrics from other Azure services using Metrics explorer by opening Metrics from the Azure Monitor menu. Per informazioni dettagliate sull'uso di questo strumento, [vedere Introduzione a Azure Metrics Explorer.See Getting started with Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) for details on using this tool. Tutte le metriche per Azure Cosmos DB si trovano nello spazio dei nomi **Cosmos DB standard metrics**. È possibile utilizzare le dimensioni seguenti con queste metriche quando si aggiunge un filtro a un grafico:You can use the following dimensions with these metrics when adding a filter to a chart:

- CollectionName
- DatabaseName
- Tipo operazione
- Region
- StatusCode


## <a name="analyzing-log-data"></a>Analisi dei dati di logAnalyzing log data
I dati nei log di Monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella ha il proprio set di proprietà univoche. Azure Cosmos DB archivia i dati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
| AzureDiagnostics | Tabella comune utilizzata da più servizi per archiviare i log delle risorse.Common table used by multiple services to store Resource logs. I log delle risorse da Azure `MICROSOFT.DOCUMENTDB`Cosmos DB possono essere identificati con .   |
| AzureActivity    | Tabella comune in cui vengono archiviati tutti i record del log attività. 


> [!IMPORTANT]
> Quando si seleziona **Registri** dal menu Database cosmo di Azure, Log Analytics viene aperto con l'ambito di query impostato sul database Cosmos di Azure corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che include dati di altri database o dati di altri servizi di Azure, selezionare Log dal menu **Monitoraggio di Azure.If** you want to run a query that includes data from other databases or data from other Azure services, select **Logs** from the Azure Monitor menu. Per informazioni dettagliate, vedere [Registrare l'ambito e l'intervallo di tempo in Azure Monitor Log Analytics.See Log query scope](../azure-monitor/log-query/scope.md) and time range in Azure Monitor Log Analytics for details.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics queries in Azure Monitor

Ecco alcune query che è possibile immettere nella barra di ricerca Ricerca log per monitorare i contenitori di Azure Cosmos.Here are some queries that you can enter into the **Log search** search bar to help you monitor your Azure Cosmos containers. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md).

Di seguito sono riportate le query che è possibile usare per monitorare i database Cosmos di Azure.Following are queries that you can help to help you monitor your Azure Cosmos databases.

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
* Per ottenere tutte le query superiori a 100 RU unite con i dati **di DataPlaneRequests** e **QueryRunTimeStatistics**.

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
    
* Per ottenere statistiche sulla chiave di partizione per valutare l'asimmetria tra le prime 3 partizioni per l'account del database:To get Partition Key statistics to evaluate skew across top 3 partitions for database account:

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

- Vedere Informazioni di riferimento sui dati di monitoraggio di [Azure Cosmos DB](monitor-cosmos-db-reference.md) per un riferimento ai log e alle metriche create da Azure Cosmos DB.
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere Monitoraggio delle risorse di [Azure con Monitoraggio di Azure.See Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
