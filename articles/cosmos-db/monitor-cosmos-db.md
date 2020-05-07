---
title: Azure Cosmos DB di monitoraggio | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità dei Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: eef6ece115afc41fd30d77747eb3e368cf95719c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780182"
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

Monitoraggio di Azure per Azure Cosmos DB si basa sulla [funzionalità cartelle di lavoro di monitoraggio di Azure](../azure-monitor/app/usage-workbooks.md) e usa gli stessi dati di monitoraggio raccolti per Cosmos DB descritti nelle sezioni seguenti. USA monitoraggio di Azure per visualizzare le prestazioni complessive, gli errori, la capacità e l'integrità operativa di tutte le risorse di Azure Cosmos DB in un'esperienza interattiva unificata e sfruttare le altre funzionalità di monitoraggio di Azure per l'analisi e l'invio di avvisi dettagliati. Per altre informazioni, vedere l'articolo [esplorare monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con maiuscole e minuscole diverse. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/collisione dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche a livello di operazione per Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **monitoraggio** nella barra di spostamento a sinistra e selezionare **metrica**.

   ![Riquadro metriche in monitoraggio di Azure](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Dal riquadro **metriche** > **selezionare una risorsa** > scegliere la **sottoscrizione**e il **gruppo di risorse**richiesti. Per il **tipo di risorsa**selezionare **account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **applica**.

   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [metriche per categoria](monitor-cosmos-db-reference.md) . In questo esempio, selezionare **unità richiesta** e **AVG** come valore di aggregazione.

   Oltre a questi dettagli, è anche possibile selezionare l' **intervallo di tempo** e la **granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche negli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dalla portale di Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È anche possibile filtrare le metriche e il grafico visualizzato da uno specifico **CollectionName**, **DatabaseName**, **OperationType**, **Region**e **statusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà obbligatoria, ad esempio **OperationType** , e selezionare un valore, ad esempio **query**. Il grafico Visualizza quindi le unità richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite la stored procedure non vengono registrate in modo che non siano disponibili nella metrica OperationType.

![Aggiungere un filtro per selezionare la granularità della metrica](./media/monitor-cosmos-db/add-metrics-filter.png)

È possibile raggruppare le metriche usando l'opzione **applica suddivisione** . È ad esempio possibile raggruppare le unità richiesta per tipo di operazione e visualizzare il grafico per tutte le operazioni in una sola volta, come illustrato nell'immagine seguente:

![Aggiungi filtro di suddivisione applica](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitoraggio dei dati raccolti da Azure Cosmos DB

Azure Cosmos DB raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Per un riferimento dettagliato dei log e delle metriche creati da Azure Cosmos DB, vedere [Azure Cosmos DB riferimento ai dati di monitoraggio](monitor-cosmos-db-reference.md) .

La pagina **Panoramica** nel portale di Azure per ogni database di Azure Cosmos include una breve visualizzazione dell'utilizzo del database, inclusa la richiesta e l'utilizzo della fatturazione oraria. Si tratta di informazioni utili, ma solo una piccola quantità di dati di monitoraggio disponibili. Alcuni di questi dati vengono raccolti automaticamente e disponibili per l'analisi non appena si crea il database, mentre è possibile abilitare la raccolta dati aggiuntiva con alcune configurazioni.

![Pagina di panoramica](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche

Azure Cosmos DB offre un'esperienza personalizzata per lavorare con le metriche. Per informazioni dettagliate sull'uso di questa esperienza e per l'analisi di diversi scenari di Azure Cosmos DB, vedere [monitorare ed eseguire il debug di Azure Cosmos DB metriche da monitoraggio di Azure](cosmos-db-azure-monitor-metrics.md) .

È possibile analizzare le metriche per Azure Cosmos DB con le metriche di altri servizi di Azure usando Esplora metriche aprendo le **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione ad Azure Esplora metriche](../azure-monitor/platform/metrics-getting-started.md) . Tutte le metriche per Azure Cosmos DB si trovano nello spazio dei nomi **Cosmos DB metriche standard**. Quando si aggiunge un filtro a un grafico, è possibile utilizzare le dimensioni seguenti con queste metriche:

- CollectionName
- DatabaseName
- Tipo operazione
- Region
- StatusCode

## <a name="analyzing-log-data"></a>Analisi dei dati di log
I dati nei log di monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche. Azure Cosmos DB archivia i dati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
| AzureDiagnostics | Tabella comune utilizzata da più servizi per archiviare i log delle risorse. I log delle risorse da Azure Cosmos DB possono essere `MICROSOFT.DOCUMENTDB`identificati con.   |
| AzureActivity    | Tabella comune in cui sono archiviati tutti i record del log attività. 


> [!IMPORTANT]
> Quando si selezionano i **log** dal menu Azure Cosmos DB, log Analytics viene aperto con l'ambito della query impostato sul database corrente di Azure Cosmos. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Per eseguire una query che includa i dati di altri database o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per informazioni dettagliate, vedere [ambito di query di log e intervallo di tempo in monitoraggio di Azure log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics query in monitoraggio di Azure

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca **Log Search (Ricerca log** ) per monitorare i contenitori di Azure Cosmos. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md).

Di seguito sono riportate le query che è possibile usare per monitorare i database di Azure Cosmos.

* Per eseguire una query per tutti i log di diagnostica da Azure Cosmos DB per un periodo di tempo specificato:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Per eseguire una query per tutte le operazioni, raggruppate per risorsa:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Per eseguire una query per tutte le attività utente, raggruppate per risorsa:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
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
