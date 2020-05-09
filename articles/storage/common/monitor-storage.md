---
title: Monitoraggio di archiviazione di Azure | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità di archiviazione di Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722921"
---
# <a name="monitoring-azure-storage"></a>Monitoraggio di archiviazione di Azure

Quando si dispone di applicazioni e processi aziendali critici che si basano sulle risorse di Azure, si desidera monitorarle per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati da archiviazione di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare gli avvisi relativi a questi dati.

> [!NOTE]
> Log di archiviazione di Azure in monitoraggio di Azure è disponibile in anteprima pubblica ed è disponibile per l'anteprima dei test in tutte le aree del cloud pubblico. Per eseguire la registrazione nell'anteprima, vedere [Questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Questa anteprima Abilita i log per i BLOB (inclusi Azure Data Lake Storage Gen2), i file, le code, le tabelle, gli account di archiviazione Premium negli account di archiviazione per utilizzo generico V1 e per utilizzo generico V2. gli account di archiviazione di tipo classico non sono supportati.

## <a name="monitor-overview"></a>Panoramica di monitoraggio

La pagina **Panoramica** nel portale di Azure per ogni risorsa di archiviazione include una breve visualizzazione dell'utilizzo delle risorse, incluse la richiesta e l'utilizzo della fatturazione oraria. Si tratta di informazioni utili, ma solo una piccola quantità di dati di monitoraggio disponibili. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa di archiviazione. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Archiviazione di Azure consente di creare dati di monitoraggio tramite [monitoraggio di Azure](../../azure-monitor/overview.md) , un servizio di monitoraggio completo dello stack in Azure che offre un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale. 

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md) , che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Monitoraggio dei dati raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard in Azure per l'analisi e l'invio di avvisi sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da archiviazione di Azure e fornendo esempi per la configurazione della raccolta di dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="monitoring-data-from-azure-storage"></a>Monitoraggio dei dati da archiviazione di Azure

Archiviazione di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure descritte in [monitoraggio dei dati dalle risorse di Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Vedere informazioni di [riferimento sui dati di monitoraggio di archiviazione di Azure](monitor-storage-reference.md) per un riferimento dettagliato dei log e delle metriche creati da archiviazione di Azure.

Le metriche e i log in monitoraggio di Azure supportano solo Azure Resource Manager account di archiviazione. Monitoraggio di Azure non supporta gli account di archiviazione classici. Se si vuole usare le metriche o i log in un account di archiviazione classico, è necessario eseguire la migrazione a Azure Resource Manager account di archiviazione. Vedere [Migrate to Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) (Eseguire la migrazione ad Azure Resource Manager).

Se si vuole, è possibile continuare a usare le metriche e i log classici. In realtà, le metriche e i log classici sono disponibili in parallelo con le metriche e i log in monitoraggio di Azure. Il supporto rimane inalterato finché archiviazione di Azure non termina il servizio in metriche e log legacy. 

### <a name="logs-in-azure-monitor-preview"></a>Log in monitoraggio di Azure (anteprima)

Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se ad esempio un account di archiviazione ha un'attività nell'endpoint BLOB ma non negli endpoint della tabella o della coda, verranno creati solo i log relativi al servizio BLOB. I log di archiviazione di Azure contengono informazioni dettagliate sulle richieste riuscite e non riuscite a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

#### <a name="logging-authenticated-requests"></a>Registrazione delle richieste autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste tramite una firma di accesso condiviso o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi (dati di log classici nel contenitore **$logs** e dati della metrica della classe nelle tabelle **$Metric** )

Le richieste effettuate dal servizio di archiviazione, ad esempio la creazione o l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [operazioni registrate archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato log di archiviazione](monitor-storage-reference.md) .

#### <a name="logging-anonymous-requests"></a>Registrazione di richieste anonime

 Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori server
- Errori di timeout lato client e lato server
- Richieste GET non riuscite con codice di errore 304 (non modificate)

Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [operazioni registrate archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [formato log di archiviazione](monitor-storage-reference.md) .

## <a name="configuration"></a>Configurazione

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o per inviarli all'esterno di monitoraggio di Azure. Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../../azure-monitor/platform/diagnostic-settings.md) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Quando si crea un'impostazione di diagnostica, è necessario scegliere il tipo di archiviazione per cui si vuole abilitare i log (BLOB, coda, tabella, file). Se si crea l'impostazione di diagnostica nella portale di Azure, è possibile selezionare la risorsa da un elenco. Se si usa PowerShell o l'interfaccia della riga di comando di Azure, sarà necessario usare l'ID risorsa del tipo di archiviazione. È possibile trovare l'ID risorsa nell'portale di Azure aprendo la pagina delle **Proprietà** dell'account di archiviazione.

Sarà inoltre necessario specificare le categorie di operazioni per cui raccogliere i log. Le categorie per archiviazione di Azure sono elencate nella tabella seguente:

| Categoria | Descrizione |
|:---|:---|
| StorageRead | Operazioni di lettura sui BLOB.  |
| StorageWrite | Operazioni di scrittura sui BLOB. |
| StorageDelete | Elimina le operazioni sui BLOB. |

## <a name="analyzing-metric-data"></a>Analisi dei dati delle metriche

È possibile analizzare le metriche per archiviazione di Azure con metriche di altri servizi di Azure usando Esplora metriche. Aprire Esplora metriche scegliendo **metriche** dal menu **monitoraggio di Azure** . Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione ad Azure Esplora metriche](../../azure-monitor/platform/metrics-getting-started.md) . 

L'esempio seguente mostra come visualizzare la metrica **Transactions** a livello di account.

![Screenshot dell'accesso alle metriche nel portale di Azure](./media/monitor-storage/access-metrics-portal.png)

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato. L'esempio seguente mostra come visualizzare **Transactions** a livello di account su un'operazione specifica selezionando valori per la dimensione **API Name**.

![Screenshot dell'accesso alle metriche con dimensioni nel portale di Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Per un elenco completo delle dimensioni supportate da archiviazione di Azure, vedere [dimensioni della metrica](monitor-storage-reference.md#metrics-dimensions).

Tutte le metriche per archiviazione di Azure si trovano negli spazi dei nomi seguenti:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Per un elenco di tutte le metriche di supporto di monitoraggio di Azure (incluso archiviazione di Azure), vedere [metriche supportate di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)di Azure.


### <a name="access-metrics"></a>Accedere alle metriche

> [!TIP]
> Per visualizzare l'interfaccia della riga di comando di Azure o gli esempi .NET, scegliere la scheda corrispondente sotto.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Elenca la definizione della metrica

È possibile elencare la definizione della metrica dell'account di archiviazione o il singolo servizio di archiviazione, ad esempio BLOB, file, tabelle o servizi di Accodamento. Usare il cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa di un singolo servizio di archiviazione come il BLOB, il file, la tabella o il servizio di Accodamento. È possibile trovare questi ID di risorsa nelle pagine delle **Proprietà** dell'account di archiviazione nell'portale di Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Lettura dei valori delle metriche

È possibile leggere i valori delle metriche a livello di account dell'account di archiviazione o del singolo servizio di archiviazione, ad esempio BLOB, file, tabelle o servizi di Accodamento. Usare il cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Elenca definizione metrica a livello di account

È possibile elencare la definizione della metrica dell'account di archiviazione o il singolo servizio di archiviazione, ad esempio BLOB, file, tabelle o servizi di Accodamento. Usare il comando [AZ monitor Metrics list-](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) Definitions.
 
In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa di un singolo servizio di archiviazione come il BLOB, il file, la tabella o il servizio di Accodamento. È possibile trovare questi ID di risorsa nelle pagine delle **Proprietà** dell'account di archiviazione nell'portale di Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori delle metriche a livello di account

È possibile leggere i valori delle metriche dell'account di archiviazione o del singolo servizio di archiviazione, ad esempio BLOB, file, tabelle o servizi di Accodamento. Usare il comando [AZ monitor Metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Monitoraggio di Azure fornisce [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) per leggere la definizione e i valori delle metriche. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. È necessario usare `0.18.0-preview` o versione successiva per le metriche di archiviazione.
 
In questi esempi, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa di un singolo servizio di archiviazione come il BLOB, il file, la tabella o il servizio di Accodamento. È possibile trovare questi ID di risorsa nelle pagine delle **Proprietà** dell'account di archiviazione nell'portale di Azure.

Sostituire la `<subscription-ID>` variabile con l'ID della sottoscrizione.  Per istruzioni su come ottenere i valori `<tenant-ID>`per, `<application-ID>`e `<AccessKey>`, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Elenca definizione metrica a livello di account

Gli esempi seguenti illustrano come elencare la definizione delle metriche a livello di account:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori delle metriche a livello di account

L'esempio seguente mostra come leggere i dati relativi a `UsedCapacity` a livello di account:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multi-dimensional-metric-values"></a>Lettura dei valori delle metriche multidimensionali

Per le metriche multidimensionali, è necessario definire il filtro dei metadati se si vuole leggere i dati delle metriche per un valore di dimensione specifico.

L'esempio seguente mostra come leggere i dati per una metrica con il supporto di più dimensioni:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>Analisi dei dati di log

È possibile accedere ai log delle risorse come BLOB in un account di archiviazione, come dati di evento o tramite query di analisi dei log.

Vedere [riferimento ai dati di monitoraggio archiviazione di Azure](monitor-storage-reference.md) per un riferimento dettagliato dei campi visualizzati in questi log.

> [!NOTE]
> Log di archiviazione di Azure in monitoraggio di Azure è disponibile in anteprima pubblica ed è disponibile per l'anteprima dei test in tutte le aree del cloud pubblico. Per eseguire la registrazione nell'anteprima, vedere [Questa pagina](https://www.microsoft.com).  Questa anteprima Abilita i log per i BLOB (inclusi Azure Data Lake Storage Gen2), i file, le code, le tabelle, gli account di archiviazione Premium negli account di archiviazione per utilizzo generico V1 e per utilizzo generico V2. gli account di archiviazione di tipo classico non sono supportati.

### <a name="access-logs-in-a-storage-account"></a>Accedere ai log in un account di archiviazione

I log vengono visualizzati come BLOB archiviati in un contenitore nell'account di archiviazione di destinazione. I dati vengono raccolti e archiviati all'interno di un singolo BLOB come payload JSON delimitato da righe. Il nome del BLOB segue la convenzione di denominazione seguente:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ad esempio:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Log di accesso nell'hub eventi

I log inviati a un hub eventi non vengono archiviati come file, ma è possibile verificare che l'hub eventi abbia ricevuto le informazioni del log. Nella portale di Azure passare all'hub eventi e quindi verificare che il numero di **messaggi in arrivo** sia maggiore di zero. 

![Log di controllo](media/monitor-storage/event-hub-log.png)

È possibile accedere e leggere i dati di log inviati all'hub eventi usando le informazioni di sicurezza e gli strumenti di monitoraggio e gestione degli eventi. Per altre informazioni, vedere [cosa è possibile fare con i dati di monitoraggio inviati a hub eventi?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Accedere ai log nell'area di lavoro Log Analytics

È possibile accedere ai log inviati a un'area di lavoro Log Analytics usando le query del log di monitoraggio di Azure.

Vedere [Introduzione a log Analytics in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

I dati vengono archiviati nelle tabelle seguenti.

| Tabella | Descrizione |
|:---|:---|
|StorageBlobLogs | Log che descrivono l'attività nell'archivio BLOB. |
|StorageFileLogs | Log che descrivono l'attività nelle condivisioni file. |
|StorageQueueLogs | Log che descrivono l'attività nelle code.|
|StorageTableLogs| Log che descrivono l'attività nelle tabelle.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Query di Log Analytics di archiviazione di Azure in monitoraggio di Azure

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca **Log Search** per monitorare gli account di archiviazione di Azure. Queste query usano il [nuovo linguaggio](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Di seguito sono riportate le query che è possibile usare per monitorare gli account di archiviazione di Azure.

* Per elencare 10 errori più comuni negli ultimi 3 giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Per elencare le prime 10 operazioni che hanno causato il maggior numero di errori negli ultimi 3 giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Per elencare le prime 10 operazioni con la latenza end-to-end più estesa negli ultimi 3 giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Per elencare tutte le operazioni che hanno causato errori di limitazione lato server negli ultimi 3 giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Per elencare tutte le richieste con accesso anonimo negli ultimi 3 giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Per creare un grafico a torta delle operazioni utilizzate negli ultimi 3 giorni.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Domande frequenti

**Archiviazione di Azure supporta le metriche per i dischi gestiti o non gestiti?**

No, le metriche relative ai dischi sono supportate da Calcolo di Azure. Per informazioni più dettagliate, vedere [questo articolo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Passaggi successivi

- [Riferimento ai dati di monitoraggio di archiviazione di Azure](monitor-storage-reference.md) per un riferimento dei log e delle metriche creati da archiviazione di Azure.
- [Monitoraggio delle risorse di Azure con](../../azure-monitor/insights/monitor-azure-resource.md) monitoraggio di Azure per informazioni dettagliate sul monitoraggio delle risorse di Azure.
- [Migrazione delle metriche di archiviazione di Azure](./storage-metrics-migration.md)

