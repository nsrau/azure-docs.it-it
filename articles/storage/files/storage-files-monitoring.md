---
title: File di Azure di monitoraggio | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità dei File di Azure. Monitorare File di Azure dati, ottenere informazioni sulla configurazione e analizzare i dati di metrica e di log.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772669"
---
# <a name="monitoring-azure-files"></a>File di Azure di monitoraggio

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati da File di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare gli avvisi relativi a questi dati.

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima Abilita i log per i BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La pagina **Panoramica** nel portale di Azure per ogni risorsa file di Azure include una breve visualizzazione dell'utilizzo delle risorse, ad esempio le richieste e la fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
File di Azure crea dati di monitoraggio tramite [monitoraggio di Azure](../../azure-monitor/overview.md), un servizio di monitoraggio dello stack completo in Azure. Monitoraggio di Azure offre un set completo di funzionalità per monitorare le risorse e di Azure e le risorse che si trovano in altri cloud e in locale. 

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da File di Azure. Gli esempi mostrano come configurare la raccolta dati e analizzare tali dati con gli strumenti di Azure.

## <a name="monitoring-data"></a>Dati di monitoraggio

File di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure, descritti in [monitoraggio dei dati dalle risorse di Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Vedere informazioni di [riferimento sui dati di monitoraggio file di Azure](storage-files-monitoring-reference.md) per informazioni dettagliate sulle metriche di metrica e log create da file di Azure.

Metriche e log di Monitoraggio di Azure supportano solo gli account di archiviazione di Azure Resource Manager. Monitoraggio di Azure non supporta gli account di archiviazione della versione classica. Se si vogliono usare le metriche o i log con gli account di archiviazione della versione classica, è necessario eseguire la migrazione a un account di archiviazione di Azure Resource Manager. Vedere [Migrate to Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) (Eseguire la migrazione ad Azure Resource Manager).

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma possono essere indirizzati ad altre posizioni usando un'impostazione di diagnostica. È necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse. 

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. Gli account di archiviazione della versione classica non sono supportati.

Per creare un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Per visualizzare un modello di Azure Resource Manager che crea un'impostazione di diagnostica, vedere [impostazione di diagnostica per archiviazione di Azure](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Quando si crea un'impostazione di diagnostica, scegliere il tipo di archiviazione per il quale abilitare i log, ad esempio BLOB, coda, tabella o file. Per File di Azure scegliere **file**. 

Una volta creata l'impostazione di diagnostica nel portale di Azure è possibile selezionare la risorsa da un elenco. Se si usa PowerShell o l'interfaccia della riga di comando di Azure, è necessario usare l'ID risorsa dell'endpoint File di Azure. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È inoltre necessario specificare una delle seguenti categorie di operazioni per le quali si desidera raccogliere i log. 

| Category | Descrizione |
|:---|:---|
| StorageRead | Operazioni di lettura sugli oggetti. |
| StorageWrite | Operazioni di scrittura su oggetti. |
| StorageDelete | Operazioni DELETE sugli oggetti. |

Per ottenere l'elenco delle operazioni SMB e REST registrate, vedere [operazioni registrate di archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [informazioni di riferimento sui dati di monitoraggio file di Azure](storage-files-monitoring-reference.md).

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche di Archiviazione di Azure con metriche di altri servizi di Azure usando Esplora metriche. Aprire Esplora metriche selezionando **Metrica** dal menu di **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../../azure-monitor/platform/metrics-getting-started.md). 

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato.  Per un elenco completo delle dimensioni supportate da Archiviazione di Azure, vedere [Dimensioni delle metriche](storage-files-monitoring-reference.md#metrics-dimensions). Le metriche per File di Azure si trovano in questi spazi dei nomi: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Per un elenco di tutte le metriche di supporto di monitoraggio di Azure, incluse File di Azure, vedere [metriche supportate di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Accesso alle metriche

> [!TIP]
> Per visualizzare esempi dell'interfaccia della riga di comando di Azure o di .NET, scegliere le schede corrispondenti di seguito.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Elenco della definizione di metrica

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio File di Azure. Usare il cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition).

In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa del servizio file di Azure.  Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lettura dei valori delle metriche

È possibile leggere i valori delle metriche a livello di account dell'account di archiviazione o del servizio File di Azure. Usare il cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio File di Azure. Usare il comando [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa del servizio file di Azure. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori di metrica a livello di account

È possibile leggere i valori delle metriche dell'account di archiviazione o del servizio File di Azure. Usare il comando [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Monitoraggio di Azure fornisce l'[SDK di .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) per consentire la lettura di definizioni e valori della metrica. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. Per le metriche di archiviazione, usare `0.18.0-preview` o una versione successiva.
 
In questi esempi sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o del servizio file di Azure. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

Sostituire la variabile `<subscription-ID>` con l'ID della propria sottoscrizione. Per istruzioni su come ottenere i valori per `<tenant-ID>`, `<application-ID>` e `<AccessKey>`, vedere [Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

L'esempio seguente mostra come elencare le definizioni delle metriche a livello di account:

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

#### <a name="reading-account-level-metric-values"></a>Lettura dei valori delle metriche a livello di account

L'esempio seguente mostra come leggere i dati di `UsedCapacity` a livello di account:

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

#### <a name="reading-multidimensional-metric-values"></a>Lettura di valori di metrica multidimensionali

Per leggere i dati di metriche multidimensionali in valori di dimensioni specifici è necessario definire i filtri dei metadati.

L'esempio seguente mostra come leggere i dati di metrica per le metriche che supportano più dimensioni:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

## <a name="analyzing-logs"></a>Analisi dei log

È possibile accedere ai log delle risorse come BLOB in un account di archiviazione, come dati degli eventi o tramite query di Log Analytics.

Per ottenere l'elenco delle operazioni SMB e REST registrate, vedere [operazioni registrate di archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [informazioni di riferimento sui dati di monitoraggio file di Azure](storage-files-monitoring-reference.md).

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. Gli account di archiviazione della versione classica non sono supportati.

Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Ad esempio, se un account di archiviazione ha un'attività nell'endpoint di file ma non negli endpoint della tabella o della coda, vengono creati solo i log che riguardano il servizio file. I log di Archiviazione di Azure contengono informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

### <a name="log-authenticated-requests"></a>Richieste di registrazione autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste che usano una firma di accesso condiviso o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi (dati di log classici nel contenitore **$logs** e dati di metrica della classe nelle tabelle **$metric**)

Le richieste effettuate dal servizio File di Azure stesso, ad esempio la creazione o l'eliminazione di log, non vengono registrate. Per un elenco completo delle richieste SMB e REST registrate, vedere [operazioni registrate di archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [file di Azure riferimento ai dati di monitoraggio](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Richieste di registrazione anonime

 Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori del server
- Errori di timeout per client e server
- Richieste GET non riuscite con codice di errore 304 (non modificate)

Tutte le altre richieste anonime non riuscite non vengono registrate. Per un elenco completo delle richieste SMB e REST registrate, vedere [operazioni registrate di archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [file di Azure riferimento ai dati di monitoraggio](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Accesso ai log in un account di archiviazione

I log vengono visualizzati come BLOB archiviati in un contenitore nell'account di archiviazione di destinazione. I dati vengono raccolti e archiviati all'interno di un singolo BLOB come payload JSON delimitato da righe. Il nome del BLOB si basa sulla convenzione di denominazione seguente:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ad esempio:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Accesso ai log in un hub eventi

I log inviati a un hub eventi non vengono archiviati come file, ma è possibile verificare che l'hub eventi abbia ricevuto le informazioni del log. Passare all'hub eventi nel portale di Azure e verificare che il numero di **Messaggi in arrivo** sia maggiore di zero. 

![Log di controllo](media/storage-files-monitoring/event-hub-log.png)

È possibile accedere e leggere i dati di log inviati all'hub eventi usando le informazioni di sicurezza e gli strumenti di monitoraggio e gestione degli eventi. Per altre informazioni, vedere [Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Accesso ai log in un'area di lavoro Log Analytics

È possibile accedere ai log inviati a un'area di lavoro Log Analytics usando le query di log di Monitoraggio di Azure. I dati vengono archiviati nella tabella **StorageFileLogs** . 

Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Query Kusto di esempio

Di seguito sono riportate alcune query che è possibile immettere nella barra di **Ricerca log** per semplificare il monitoraggio del file di Azure. Queste query usano il [nuovo linguaggio](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> Quando si seleziona **log** dal menu del gruppo di risorse dell'account di archiviazione, log Analytics viene aperto con l'ambito della query impostato sul gruppo di risorse corrente. Ciò significa che le query di log includeranno solo i dati del gruppo di risorse. Se si vuole eseguire una query che includa dati da altre risorse o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](/azure/azure-monitor/log-query/scope/).

Usare queste query per monitorare le condivisioni file di Azure:

- Visualizzare gli errori SMB nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Creare un grafico a torta delle operazioni SMB nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Visualizza gli errori REST nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Creare un grafico a torta delle operazioni REST nell'ultima settimana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Per visualizzare l'elenco dei nomi e delle descrizioni delle colonne per File di Azure, vedere [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Per altre informazioni su come scrivere query, vedere [esercitazione: Introduzione alle query log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Avvisi

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi nel sistema prima che i clienti li notino. È possibile impostare avvisi su [metriche](/azure/azure-monitor/platform/alerts-metric-overview), [log](/azure/azure-monitor/platform/alerts-unified-log)e [log attività](/azure/azure-monitor/platform/activity-log-alerts). 

Nella tabella seguente sono elencati alcuni scenari di esempio da monitorare e la metrica appropriata da usare per l'avviso:

| Scenario | Metrica da usare per l'avviso |
|-|-|
| La condivisione file è limitata. | Metrica: transazioni<br>Nome Dimensione: tipo di risposta <br>Nome Dimensione: FileShare (solo condivisione file Premium) |
| Le dimensioni della condivisione file sono pari al 80% della capacità. | Metrica: capacità file<br>Nome Dimensione: FileShare (solo condivisione file Premium) |
| La condivisione file in uscita ha superato 500 GiB in un giorno. | Metrica: in uscita<br>Nome Dimensione: FileShare (solo condivisione file Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Come creare avvisi per File di Azure

1. Passare all' **account di archiviazione** nell' **portale di Azure**. 

2. Fare clic su **avvisi** e quindi su **+ nuova regola di avviso**.

3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** e quindi fare clic su **fine**. 

4. Fare clic su **Seleziona condizione** e fornire le informazioni seguenti per l'avviso: 

    - **Metrica**
    - **Nome dimensione**
    - **Logica avvisi**

5. Fare clic su **Seleziona gruppo di azioni** e aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.

6. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione**e la **gravità**della **regola di avviso**.

7. Fare clic su **Crea regola di avviso** per creare l'avviso.

> [!NOTE]  
> Se si crea un avviso ed è troppo rumoroso, modificare il valore di soglia e la logica di avviso.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Come creare un avviso se una condivisione file è limitata

1. Passare all' **account di archiviazione** nell' **portale di Azure**.
2. Nella sezione **monitoraggio** fare clic su **avvisi**e quindi su **+ nuova regola di avviso**.
3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi fare clic su **fine**. Ad esempio, se il nome dell'account di archiviazione è `contoso` , selezionare la `contoso/file` risorsa.
4. Fare clic su **Seleziona condizione** per aggiungere una condizione.
5. Viene visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica **transazioni** .
6. Nel pannello **Configura logica** per i segnali fare clic sull'elenco a discesa **nome dimensione** e selezionare **tipo di risposta**.
7. Fare clic sull'elenco a discesa **valori dimensione** e selezionare **SUCCESSWITHTHROTTLING** (per SMB) o **ClientThrottlingError** (per REST).

   > [!NOTE]
   > Se il valore della dimensione SuccessWithThrottling o ClientThrottlingError non è elencato, significa che la risorsa non è stata limitata. Per aggiungere il valore della dimensione, fare clic su **Aggiungi valore personalizzato** accanto all'elenco a discesa **valori dimensione** , digitare **SuccessWithThrottling** o **ClientThrottlingError**, fare clic su **OK** , quindi ripetere il passaggio #7.

8. Fare clic sull'elenco a discesa **nome dimensione** e selezionare **condivisione file**.
9. Fare clic sull'elenco a discesa **valori dimensione** e selezionare le condivisioni file per le quali si desidera ricevere un avviso.

   > [!NOTE]
   > Se la condivisione file è una condivisione file standard, selezionare **tutti i valori correnti e futuri**. Nell'elenco a discesa valori dimensione non verranno elencate le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi di limitazione per le condivisioni file standard verranno attivati se una condivisione file all'interno dell'account di archiviazione è limitata e l'avviso non identificherà quale condivisione file è stata limitata. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione.

10. Definire i **parametri di avviso** (valore soglia, operatore, granularità aggregazione e frequenza di valutazione) e fare clic su **fine**.

    > [!TIP]
    > Se si utilizza una soglia statica, il grafico delle metriche consente di determinare un valore soglia ragionevole se la condivisione file è attualmente in fase di limitazione. Se si utilizza una soglia dinamica, nel grafico delle metriche verranno visualizzate le soglie calcolate in base ai dati recenti.

11. Fare clic su **Seleziona gruppo di azioni** per aggiungere un **gruppo di azioni** (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
12. Immettere i **Dettagli dell'avviso** , ad esempio nome della **regola di avviso**, * * descrizione e **gravità**.
13. Fare clic su **Crea regola di avviso** per creare l'avviso.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Come creare un avviso se le dimensioni della condivisione file di Azure sono pari al 80% della capacità

1. Passare all' **account di archiviazione** nell' **portale di Azure**.
2. Nella sezione **monitoraggio** fare clic su **avvisi** e quindi su **+ nuova regola di avviso**.
3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi fare clic su **fine**. Ad esempio, se il nome dell'account di archiviazione è `contoso` , selezionare la `contoso/file` risorsa.
4. Fare clic su **Seleziona condizione** per aggiungere una condizione.
5. Viene visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica della **capacità del file** .
6. Nel pannello **Configura logica** per i segnali fare clic sull'elenco a discesa **nome dimensione** e selezionare **condivisione file**.
7. Fare clic sull'elenco a discesa **valori dimensione** e selezionare le condivisioni file per le quali si desidera ricevere un avviso.

   > [!NOTE]
   > Se la condivisione file è una condivisione file standard, selezionare **tutti i valori correnti e futuri**. Nell'elenco a discesa valori dimensione non verranno elencate le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi per le condivisioni file standard sono basati su tutte le condivisioni file nell'account di archiviazione. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione.

8. Immettere il **valore soglia** in byte. Se ad esempio la dimensione della condivisione file è 100 TiB e si desidera ricevere un avviso quando la dimensione della condivisione file è 80% di capacità, il valore soglia in byte è 87960930222080.
9. Definire il resto dei **parametri di avviso** (granularità di aggregazione e frequenza di valutazione) e fare clic su **fine**.
10. Fare clic su Seleziona gruppo di azioni per aggiungere un gruppo di azioni (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
11. Immettere i **Dettagli dell'avviso** , ad esempio nome della **regola di avviso**, * * descrizione e **gravità**.
12. Fare clic su **Crea regola di avviso** per creare l'avviso.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Come creare un avviso se l'uscita dalla condivisione file di Azure ha superato 500 GiB in un giorno

1. Passare all' **account di archiviazione** nell' **portale di Azure**.
2. Nella sezione Monitoraggio fare clic su **avvisi** e quindi su **+ nuova regola di avviso**.
3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi fare clic su **fine**. Ad esempio, se il nome dell'account di archiviazione è contoso, selezionare la risorsa Contoso/file.
4. Fare clic su **Seleziona condizione** per aggiungere una condizione.
5. Viene visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica in **uscita** .
6. Nel pannello **Configura logica** per i segnali fare clic sull'elenco a discesa **nome dimensione** e selezionare **condivisione file**.
7. Fare clic sull'elenco a discesa **valori dimensione** e selezionare le condivisioni file per le quali si desidera ricevere un avviso.

   > [!NOTE]
   > Se la condivisione file è una condivisione file standard, selezionare **tutti i valori correnti e futuri**. Nell'elenco a discesa valori dimensione non verranno elencate le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi per le condivisioni file standard sono basati su tutte le condivisioni file nell'account di archiviazione. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione.

8. Immettere **536870912000** byte per valore soglia. 
9. Fare clic sull'elenco a discesa **granularità aggregazione** e selezionare **24 ore**.
10. Selezionare la **frequenza di valutazione** e **fare clic su fine**.
11. Fare clic su **Seleziona gruppo di azioni** per aggiungere un **gruppo di azioni** (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
12. Immettere i **Dettagli dell'avviso** , ad esempio nome della **regola di avviso**, * * descrizione e **gravità**.
13. Fare clic su **Crea regola di avviso** per creare l'avviso.

## <a name="next-steps"></a>Passaggi successivi

- [Riferimento ai dati di monitoraggio File di Azure](storage-files-monitoring.md)
- [Monitorare le risorse di Azure con monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md)
- [Migrazione delle metriche di archiviazione di Azure](../common/storage-metrics-migration.md)
- [Pianificazione per la distribuzione di File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Come distribuire File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Risolvere i problemi di File di Azure in Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Risolvere i problemi di File di Azure in Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
