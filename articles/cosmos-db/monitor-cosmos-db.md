---
title: Azure Cosmos DB di monitoraggio | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità di Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f7cc9d4062ad9a49ff84db6ac035ff53934ae0a0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532058"
---
# <a name="monitor-azure-cosmos-db"></a>Monitorare Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati dai database Azure Cosmos e come è possibile utilizzare le funzionalità di Monitoraggio di Azure per analizzare e creare avvisi su tali dati.

È possibile monitorare i dati con le metriche lato client e lato server. Quando si usano le metriche lato server, è possibile monitorare i dati archiviati in Azure Cosmos DB con le opzioni seguenti:

* **Monitoraggio dal portale di Azure Cosmos DB:** È possibile monitorare con le metriche disponibili nella scheda **metriche** dell'account Azure Cosmos. Le metriche in questa scheda includono velocità effettiva, archiviazione, disponibilità, latenza, coerenza e metriche a livello di sistema. Per impostazione predefinita, queste metriche hanno un periodo di conservazione di 7 giorni. Per altre informazioni, vedere la sezione [monitoraggio dei dati raccolti dalla Azure Cosmos DB](#monitoring-from-azure-cosmos-db) di questo articolo.

* **Monitorare con le metriche in monitoraggio di Azure:** È possibile monitorare le metriche dell'account Azure Cosmos e creare dashboard dal monitoraggio di Azure. Monitoraggio di Azure raccoglie le metriche Azure Cosmos DB per impostazione predefinita, non è necessario configurare nulla in modo esplicito. Queste metriche vengono raccolte con una granularità di un minuto, la granularità può variare in base alla metrica scelta. Per impostazione predefinita, queste metriche hanno un periodo di conservazione di 30 giorni. La maggior parte delle metriche disponibili dalle opzioni precedenti è disponibile anche in queste metriche. I valori della dimensione per le metriche, ad esempio il nome del contenitore, non fanno distinzione tra maiuscole e minuscole. Pertanto, è necessario utilizzare il confronto senza distinzione tra maiuscole e minuscole quando si esegue confronti tra stringhe su questi valori di dimensione. Per altre informazioni, vedere la sezione [analizzare i dati delle metriche](#analyze-metric-data) in questo articolo.

* **Monitorare con i log di diagnostica in monitoraggio di Azure:** È possibile monitorare i log dell'account Azure Cosmos e creare dashboard dal monitoraggio di Azure. I dati di telemetria, ad esempio eventi e tracce che si verificano a una seconda granularità, vengono archiviati come log. Se, ad esempio, la velocità effettiva di un contenitore è modificata, le proprietà di un account Cosmos vengono modificate. questi eventi vengono acquisiti all'interno dei log. È possibile analizzare questi log eseguendo query sui dati raccolti. Per altre informazioni, vedere la sezione [analizzare i dati dei log](#analyze-log-data) di questo articolo.

* **Monitorare a livello di codice con gli SDK:** È possibile monitorare l'account Azure Cosmos a livello di codice usando gli SDK .NET, Java, Python, Node.js e le intestazioni nell'API REST. Per altre informazioni, vedere la sezione [monitoraggio Azure Cosmos DB a livello di codice](#monitor-cosmosdb-programmatically) di questo articolo.

Nella figura seguente sono illustrate diverse opzioni disponibili per monitorare Azure Cosmos DB account tramite portale di Azure:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opzioni di monitoraggio disponibili in portale di Azure" border="false":::

Quando si usa Azure Cosmos DB, sul lato client è possibile raccogliere i dettagli relativi a costo richiesta, ID attività, informazioni di analisi dello stack o dell'eccezione, stato HTTP/codice di stato secondario, stringa di diagnostica per eseguire il debug di eventuali problemi che potrebbero verificarsi. Queste informazioni sono necessarie anche se è necessario contattare il team di supporto di Azure Cosmos DB. 

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La pagina **Panoramica** nel portale di Azure per ogni account Azure Cosmos DB include una breve visualizzazione dell'utilizzo delle risorse, ad esempio le richieste totali, le richieste che hanno generato un codice di stato HTTP specifico e la fatturazione oraria. Queste informazioni sono utili, ma da questo riquadro è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Azure Cosmos DB crea i dati di monitoraggio usando [Monitoraggio di Azure](../azure-monitor/overview.md), un servizio di monitoraggio completo dello stack di Azure che offre un set completo di funzionalità per monitorare le risorse di Azure e le risorse che si trovano in altri cloud e in locale.

Se non si ha già familiarità con il monitoraggio dei servizi di Azure, iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md) , che descrive i concetti seguenti:

* Informazioni su Monitoraggio di Azure
* Costi associati al monitoraggio
* Dati di monitoraggio raccolti in Azure
* Configurazione della raccolta dati
* Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Azure Cosmos DB e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Monitoraggio di Azure per Azure Cosmos DB

Monitoraggio di Azure per Azure Cosmos DB si basa sulla [funzionalità cartelle di lavoro di monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md) e usa gli stessi dati di monitoraggio raccolti per Azure Cosmos DB descritti nelle sezioni seguenti. Usare Monitoraggio di Azure per una visualizzazione delle prestazioni complessive, degli errori, della capacità e dell'integrità operativa di tutte le risorse Azure Cosmos DB in un'esperienza interattiva unificata e sfruttare le altre funzionalità di Monitoraggio di Azure per un'analisi dettagliata e la creazione di avvisi. Per altre informazioni, vedere l'articolo [Esplorare Monitoraggio di Azure per Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Monitoraggio dei dati 

Azure Cosmos DB raccoglie gli stessi tipi di dati di monitoraggio di altre risorse di Azure, che sono descritti in [Monitoraggio dei dati dalle risorse di Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Per informazioni di riferimento dettagliate sui log e sulle metriche create da Azure Cosmos DB, vedere [Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB](monitor-cosmos-db-reference.md).

La pagina **Panoramica** nel portale di Azure per ogni database Azure Cosmos include una breve visualizzazione dell'utilizzo del database, inclusa la relativa richiesta e l'utilizzo della fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente e sono disponibili per l'analisi non appena si crea il database, mentre è possibile abilitare la raccolta dati aggiuntiva con alcune configurazioni.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Pagina di panoramica":::

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti e archiviati automaticamente, ma possono essere indirizzati ad altri percorsi tramite un'impostazione di diagnostica.  

I log delle risorse non vengono raccolti e archiviati fino a quando non si crea un'impostazione di diagnostica e la si instrada a una o più posizioni.

Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](cosmosdb-monitor-resource-logs.md) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure e alcuni esempi di query di diagnostica. Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere.

Le metriche e i log che è possibile raccogliere sono descritti nelle sezioni seguenti.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Analisi delle metriche

Azure Cosmos DB offre un'esperienza personalizzata per lavorare con le metriche. È possibile analizzare le metriche per Azure Cosmos DB con le metriche di altri servizi di Azure usando Esplora metriche aprendo **Metriche** dal menu **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../azure-monitor/platform/metrics-getting-started.md). È anche possibile eseguire il checkout come monitorare la [latenza lato server](monitor-server-side-latency.md), l' [utilizzo delle unità richiesta](monitor-request-unit-usage.md)e l' [utilizzo normalizzato delle unità richiesta](monitor-normalized-request-units.md) per le risorse Azure Cosmos DB.

Per un elenco delle metriche della piattaforma raccolte per Azure Cosmos DB, vedere l'articolo [monitoraggio delle metriche di riferimento ai dati Azure Cosmos DB](monitor-cosmos-db-reference.md#metrics) .

Tutte le metriche per Azure Cosmos DB si trovano nello spazio dei nomi **Metriche standard di Cosmos DB**. Quando si aggiunge un filtro a un grafico, è possibile usare le dimensioni seguenti con queste metriche:

* CollectionName
* DatabaseName
* Tipo operazione
* Region
* StatusCode

Come riferimento, è possibile visualizzare un elenco di [tutte le metriche delle risorse supportate in monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md).

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Visualizzare le metriche a livello di operazione per Azure Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel menu di spostamento a sinistra selezionare **Monitoraggio** e quindi selezionare **Metriche**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Riquadro Metriche in Monitoraggio di Azure":::

1. Nel riquadro **Metriche** > **Selezionare una risorsa** > scegliere la **sottoscrizione** e il **gruppo di risorse** richiesti. Per **Tipo di risorsa** selezionare **Account Azure Cosmos DB**, scegliere uno degli account Azure Cosmos esistenti e selezionare **Applica**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Scegliere un account Cosmos DB per visualizzare le metriche":::

1. È quindi possibile selezionare una metrica dall'elenco delle metriche disponibili. È possibile selezionare metriche specifiche per unità richiesta, archiviazione, latenza, disponibilità, Cassandra e altre. Per informazioni dettagliate su tutte le metriche disponibili in questo elenco, vedere l'articolo [Metriche per categoria](monitor-cosmos-db-reference.md). In questo esempio, selezionare **Unità richiesta** e **Media** come valore di aggregazione.

   Oltre a questi dettagli, è anche possibile selezionare l'**Intervallo di tempo** e la **Granularità temporale** delle metriche. Al massimo, è possibile visualizzare le metriche degli ultimi 30 giorni.  Dopo aver applicato il filtro, viene visualizzato un grafico in base al filtro. È possibile visualizzare il numero medio di unità richiesta utilizzate al minuto per il periodo selezionato.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Scegliere una metrica dal portale di Azure":::

### <a name="add-filters-to-metrics"></a>Aggiungere filtri alle metriche

È anche possibile filtrare le metriche e il grafico visualizzato in base a un elemento specifico: **CollectionName**, **DatabaseName**, **OperationType**, **Region** e **StatusCode**. Per filtrare le metriche, selezionare **Aggiungi filtro** e scegliere la proprietà richiesta, ad esempio **OperationType**, e selezionare un valore, ad esempio **Query**. Il grafico visualizza quindi le unità richiesta utilizzate per l'operazione di query per il periodo selezionato. Le operazioni eseguite tramite stored procedure non vengono registrate e quindi non sono disponibili nella metrica OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Aggiungere un filtro per selezionare la granularità della metrica":::

È possibile raggruppare le metriche usando l'opzione **Applicare separazione**. È ad esempio possibile raggruppare le unità richiesta per tipo di operazione e visualizzare contemporaneamente il grafico per tutte le operazioni, come illustrato nell'immagine seguente:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Aggiungere il filtro Applicare separazione":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Analisi dei log

I dati nei log di Monitoraggio di Azure vengono archiviati in tabelle in cui ogni tabella dispone di un proprio set di proprietà univoche.

Tutti i log delle risorse in monitoraggio di Azure hanno gli stessi campi seguiti dai campi specifici del servizio. Lo schema comune è illustrato nello schema del [log delle risorse di monitoraggio di Azure](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Per un elenco dei tipi di log delle risorse raccolti per Azure Cosmos DB, vedere [monitoraggio Azure Cosmos DB dati di riferimento](monitor-cosmos-db-reference.md#resource-logs).

Il [log attività](/azure/azure-monitor/platform/activity-log) è un account di accesso della piattaforma Azure che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione. È possibile visualizzarlo in modo indipendente o instradarlo ai log di monitoraggio di Azure, in cui è possibile eseguire query molto più complesse usando Log Analytics.  

Azure Cosmos DB archivia i dati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
| AzureDiagnostics | Tabella comune usata da più servizi per archiviare i log delle risorse. I log delle risorse di Azure Cosmos DB possono essere identificati con `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabella comune in cui sono archiviati tutti i record del log attività.

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

> [!IMPORTANT]
> Quando si seleziona **log** dal menu Azure Cosmos DB, log Analytics viene aperto con l'ambito della query impostato sull'account di Azure Cosmos DB corrente. Ciò significa che le query di log includeranno solo i dati di tale risorsa. Se si vuole eseguire una query che includa i dati di altri account o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../azure-monitor/log-query/scope.md).

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca dei **log** per semplificare il monitoraggio delle risorse di Azure Cosmos. Queste query usano il [nuovo linguaggio](../azure-monitor/log-query/log-query-overview.md).

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

## <a name="alerts"></a>Avvisi

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi su [metriche](../azure-monitor/platform/alerts-metric-overview.md), [log](../azure-monitor/platform/alerts-unified-log.md)e [log attività](../azure-monitor/platform/activity-log-alerts.md). Tipi diversi di avvisi hanno vantaggi e svantaggi

Ad esempio, nella tabella seguente sono elencate alcune regole di avviso per le risorse. È possibile trovare un elenco dettagliato delle regole di avviso dalla portale di Azure. Per altre informazioni, vedere [How to Configure Alerts](create-alerts.md) article.  

| Tipo di avviso | Condizione | Descrizione  |
|:---|:---|:---|
|Limitazione della frequenza sulle unità richiesta (avviso della metrica) |Nome Dimensione: StatusCode, operatore: uguale a, valori Dimensione: 429  | Avvisa se il contenitore o un database ha superato il limite di velocità effettiva di cui è stato effettuato il provisioning. |
|Area sottoposta a failover |Operatore: maggiore di, tipo di aggregazione: conteggio, valore soglia: 1 | Quando viene eseguito il failover di una singola area. Questo avviso è utile se non è stato abilitato il failover automatico. |
| Ruotare le chiavi (avviso del log attività)| Livello evento: informativo, stato: avviato| Genera un avviso quando vengono ruotate le chiavi dell'account. È possibile aggiornare l'applicazione con le nuove chiavi. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitorare Azure Cosmos DB a livello di codice

Le metriche a livello di account disponibili nel portale, ad esempio l'uso delle risorse di archiviazione dell'account e il numero totale di richieste, non sono disponibili tramite le API SQL. È tuttavia possibile recuperare dati di utilizzo a livello di raccolta tramite le API SQL. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

* Per usare l'API REST, [eseguire il comando GET sulla raccolta](/rest/api/cosmos-db/get-a-collection). Nelle intestazioni x-ms-resource-quota e x-ms-resource-usage della risposta verranno restituite le informazioni relative alla quota e all'utilizzo per la raccolta.

* Per usare .NET SDK, usare il metodo [DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync), che restituisce un oggetto [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) contenente alcune proprietà d'uso, ad esempio **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e altro ancora.

Per accedere a metriche aggiuntive, usare l'[SDK di Monitoraggio di Azure](https://www.nuget.org/packages/Microsoft.Azure.Insights). Le definizioni delle metriche disponibili possono essere recuperate chiamando:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Per recuperare le metriche singole, usare il formato seguente:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Per altre informazioni, vedere l'articolo sull' [API REST di monitoraggio di Azure](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni di riferimento sui log e sulle metriche create da Azure Cosmos DB, vedere [Informazioni di riferimento sui dati di monitoraggio di Azure Cosmos DB](monitor-cosmos-db-reference.md).
* Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md).
