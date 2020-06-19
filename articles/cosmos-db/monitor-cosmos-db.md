---
title: Monitoraggio di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità di Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 5056762dab18ae23980c7d3b3ebfbb3c3014fa56
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798703"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitoraggio di Azure Cosmos DB

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati dai database Azure Cosmos e come è possibile utilizzare le funzionalità di Monitoraggio di Azure per analizzare e creare avvisi su tali dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Azure Cosmos DB crea i dati di monitoraggio usando [Monitoraggio di Azure](../azure-monitor/overview.md), un servizio di monitoraggio completo dello stack di Azure che offre un set completo di funzionalità per monitorare le risorse di Azure e le risorse che si trovano in altri cloud e in locale.

Se non si ha già familiarità con il monitoraggio dei servizi di Azure, iniziare con l'articolo [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md), che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Azure Cosmos DB e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Monitoraggio di Azure per Azure Cosmos DB

Monitoraggio di Azure per Azure Cosmos DB è basato sulla [funzionalità delle cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md) e usa gli stessi dati di monitoraggio raccolti per Cosmos DB descritti nelle sezioni seguenti. Usare Monitoraggio di Azure per una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse Azure Cosmos DB in un'esperienza interattiva unificata e sfruttare le altre funzionalità di Monitoraggio di Azure per un'analisi dettagliata e la creazione di avvisi. Per altre informazioni, vedere l'articolo [Esplorare Monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche a livello di operazione per Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel menu di spostamento a sinistra selezionare **Monitoraggio** e quindi selezionare **Metriche**.

   ![Riquadro Metriche in Monitoraggio di Azure](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica**.

   ![Scegliere un account Cosmos DB per visualizzare le metriche](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare **Unità richiesta** e **Media** come valore di aggregazione.

   Oltre a questi dettagli, è anche possibile selezionare l'**Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   ![Scegliere una metrica dal portale di Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È anche possibile filtrare le metriche e il grafico visualizzato in base a un elemento specifico: **CollectionName**, **DatabaseName**, **OperationType**, **Region** e **StatusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà richiesta, ad esempio **OperationType**, e selezionare un valore, ad esempio **Query**. Il grafico visualizza quindi le unità richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite stored procedure non vengono registrate e quindi non sono disponibili nella metrica OperationType.

![Aggiungere un filtro per selezionare la granularità della metrica](./media/monitor-cosmos-db/add-metrics-filter.png)

È possibile raggruppare le metriche usando l'opzione **Applicare separazione**. È ad esempio possibile raggruppare le unità richiesta per tipo di operazione e visualizzare contemporaneamente il grafico per tutte le operazioni, come illustrato nell'immagine seguente:

![Aggiungere il filtro Applicare separazione](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitoraggio dei dati raccolti da Azure Cosmos DB

Azure Cosmos DB raccoglie gli stessi tipi di dati di monitoraggio di altre risorse di Azure, che sono descritti in [Monitoraggio dei dati dalle risorse di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Per informazioni di riferimento dettagliate sui log e sulle metriche create da Azure Cosmos DB, vedere [Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB](monitor-cosmos-db-reference.md).

La pagina **Panoramica** nel portale di Azure per ogni database Azure Cosmos include una breve visualizzazione dell'utilizzo del database, inclusa la relativa richiesta e l'utilizzo della fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente e sono disponibili per l'analisi non appena si crea il database, mentre è possibile abilitare la raccolta dati aggiuntiva con alcune configurazioni.

![Pagina di panoramica](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche

Azure Cosmos DB offre un'esperienza personalizzata per lavorare con le metriche. Per informazioni dettagliate sull'uso di questa esperienza e per l'analisi di diversi scenari di Azure Cosmos DB, vedere [Eseguire il monitoraggio e il debug delle metriche di Azure Cosmos DB da Monitoraggio di Azure](cosmos-db-azure-monitor-metrics.md).

È possibile analizzare le metriche per Azure Cosmos DB con le metriche di altri servizi di Azure usando Esplora metriche aprendo **Metriche** dal menu **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../azure-monitor/platform/metrics-getting-started.md). Tutte le metriche per Azure Cosmos DB si trovano nello spazio dei nomi **Metriche standard di Cosmos DB**. Quando si aggiunge un filtro a un grafico, è possibile usare le dimensioni seguenti con queste metriche:

- CollectionName
- DatabaseName
- Tipo operazione
- Region
- StatusCode

## <a name="analyzing-log-data"></a>Analisi dei dati di log
I dati nei log di Monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche. Azure Cosmos DB archivia i dati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
| AzureDiagnostics | Tabella comune usata da più servizi per archiviare i log delle risorse. I log delle risorse di Azure Cosmos DB possono essere identificati con `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabella comune in cui sono archiviati tutti i record del log attività. 


> [!IMPORTANT]
> Quando si seleziona **Log** dal menu di Azure Cosmos DB, viene aperto Log Analytics con l'ambito della query impostato sul database corrente di Azure Cosmos. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa dati di altri database o dati di altri servizi di Azure, selezionare **Log** dal menu di **Monitoraggio di Azure**. Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../azure-monitor/log-query/scope.md).

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Query di Log Analytics per Azure Cosmos DB in Monitoraggio di Azure

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca **Ricerca log** per consentire il monitoraggio dei contenitori Azure Cosmos. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md).

Di seguito sono riportate le query che è possibile usare per monitorare i database Azure Cosmos.

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

- Per informazioni di riferimento sui log e sulle metriche create da Azure Cosmos DB, vedere [Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB](monitor-cosmos-db-reference.md).
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md).
