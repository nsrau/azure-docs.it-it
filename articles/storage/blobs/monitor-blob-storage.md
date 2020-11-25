---
title: Monitoraggio dell'archiviazione BLOB di Azure | Microsoft Docs
description: Informazioni su come monitorare le prestazioni e la disponibilità dell'archiviazione BLOB di Azure. Monitora i dati di archiviazione BLOB di Azure, informazioni sulla configurazione e analizza i dati di metrica e di log.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7918361826eac10822b8b155d6b47dd89011f683
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908932"
---
# <a name="monitoring-azure-blob-storage"></a>Monitoraggio dell'archiviazione BLOB di Azure

Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati dall'archiviazione BLOB di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare gli avvisi relativi a questi dati.

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima Abilita i log per i BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Panoramica di Monitoraggio

La pagina **Panoramica** nel portale di Azure per ogni risorsa di archiviazione BLOB include una breve visualizzazione dell'utilizzo delle risorse, ad esempio le richieste e la fatturazione oraria. Queste informazioni sono utili, ma è disponibile solo una piccola quantità di dati di monitoraggio. Alcuni di questi dati vengono raccolti automaticamente ed è disponibile per l'analisi non appena si crea la risorsa. Con alcune configurazioni è possibile abilitare altri tipi di raccolta dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Archiviazione BLOB di Azure consente di creare dati di monitoraggio tramite [monitoraggio di Azure](../../azure-monitor/overview.md), un servizio di monitoraggio completo dello stack in Azure. Monitoraggio di Azure offre un set completo di funzionalità per monitorare le risorse e di Azure e le risorse che si trovano in altri cloud e in locale. 

Iniziare con l'articolo [monitoraggio delle risorse di Azure con monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md) , che descrive quanto segue:

- Informazioni su Monitoraggio di Azure
- Costi associati al monitoraggio
- Dati di monitoraggio raccolti in Azure
- Configurazione della raccolta dati
- Strumenti standard di Azure per l'analisi e la notifica sui dati di monitoraggio

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti da Archiviazione di Azure. Gli esempi mostrano come configurare la raccolta dati e analizzare tali dati con gli strumenti di Azure.

## <a name="monitoring-data"></a>Dati di monitoraggio

Archiviazione BLOB di Azure raccoglie gli stessi tipi di dati di monitoraggio delle altre risorse di Azure, descritti in [monitoraggio dei dati dalle risorse di Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Vedere informazioni di [riferimento sui dati di monitoraggio dell'archiviazione BLOB di Azure](monitor-blob-storage-reference.md) per informazioni dettagliate sulle metriche di metrica e log create dall'archiviazione BLOB di Azure.

Metriche e log di Monitoraggio di Azure supportano solo gli account di archiviazione di Azure Resource Manager. Monitoraggio di Azure non supporta gli account di archiviazione della versione classica. Se si vogliono usare le metriche o i log con gli account di archiviazione della versione classica, è necessario eseguire la migrazione a un account di archiviazione di Azure Resource Manager. Vedere [Migrate to Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md) (Eseguire la migrazione ad Azure Resource Manager).

Se si vuole, è possibile continuare a usare le metriche e i log della versione classica. In realtà, le metriche e i log della versione classica sono disponibili in parallelo con le metriche e i log di Monitoraggio di Azure. Il supporto rimane invariato fino al termine del servizio relativo alle metriche e ai log legacy da parte di Archiviazione di Azure.

## <a name="collection-and-routing"></a>Raccolta e routing

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma possono essere indirizzati ad altre posizioni usando un'impostazione di diagnostica. 

Per raccogliere i log delle risorse, è necessario creare un'impostazione di diagnostica. Quando si crea l'impostazione, scegliere **BLOB** come tipo di archiviazione per cui si vuole abilitare i log. Specificare quindi una delle seguenti categorie di operazioni per le quali si desidera raccogliere i log. 

| Category | Descrizione |
|:---|:---|
| StorageRead | Operazioni di lettura sugli oggetti. |
| StorageWrite | Operazioni di scrittura su oggetti. |
| StorageDelete | Operazioni DELETE sugli oggetti. |

> [!NOTE]
> Data Lake Storage Gen2 non viene visualizzato come tipo di archiviazione. Questo perché Data Lake Storage Gen2 è un set di funzionalità disponibili per l'archiviazione BLOB. 

## <a name="creating-a-diagnostic-setting"></a>Creazione di un'impostazione di diagnostica

È possibile creare un'impostazione di diagnostica usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager. 

Per istruzioni generali, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../../azure-monitor/platform/diagnostic-settings.md).

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima Abilita i log per i BLOB (che includono Azure Data Lake Storage Gen2), file, code e tabelle. Questa funzionalità è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Portale di Azure](#tab/azure-portal)

1. Accedere al portale di Azure.

2. Passare all'account di archiviazione.

3. Nella sezione **monitoraggio** fare clic su **impostazioni di diagnostica (anteprima)**.

   > [!div class="mx-imgBorder"]
   > ![portale - Log di diagnostica](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Scegliere **BLOB** come tipo di archiviazione per cui si vuole abilitare i log.

5. Fare clic su **Aggiungi impostazione di diagnostica**.

   > [!div class="mx-imgBorder"]
   > ![Portale-log delle risorse-Aggiungi impostazione di diagnostica](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   Verrà visualizzata la pagina **impostazioni di diagnostica** .

   > [!div class="mx-imgBorder"]
   > ![Pagina log delle risorse](media/monitor-blob-storage/diagnostic-logs-page.png)

6. Nel campo **nome** della pagina immettere un nome per questa impostazione del registro risorse. Selezionare quindi le operazioni che si desidera registrare (operazioni di lettura, scrittura ed eliminazione) e il percorso in cui si desidera che vengano inviati i log.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

1. Selezionare la casella **di controllo archivia in un account di archiviazione** e quindi fare clic sul pulsante **Configura** .

   > [!div class="mx-imgBorder"]   
   > ![Archiviazione archivio pagina impostazioni di diagnostica](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Nell'elenco a discesa **account di archiviazione** selezionare l'account di archiviazione in cui si vogliono archiviare i log, fare clic sul pulsante **OK** , quindi fare clic sul pulsante **Salva** .

   > [!NOTE]
   > Prima di scegliere un account di archiviazione come destinazione di esportazione, vedere [archiviare i log delle risorse di Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) per informazioni sui prerequisiti nell'account di archiviazione.

#### <a name="stream-logs-to-azure-event-hubs"></a>Trasmettere i log a hub eventi di Azure

1. Selezionare la casella **di controllo flusso a hub eventi** e quindi fare clic sul pulsante **Configura** .

2. Nel riquadro **selezionare un hub eventi** scegliere lo spazio dei nomi, il nome e il nome del criterio dell'hub eventi in cui si desidera trasmettere i log. 

   > [!div class="mx-imgBorder"]
   > ![Hub eventi della pagina impostazioni di diagnostica](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Fare clic sul pulsante **OK** , quindi fare clic sul pulsante **Salva** .

#### <a name="send-logs-to-azure-log-analytics"></a>Inviare i log ad Azure Log Analytics

1. Selezionare la casella **di controllo Invia a log Analytics** , selezionare un'area di lavoro di log Analytics e quindi fare clic sul pulsante **Salva** .

   > [!div class="mx-imgBorder"]   
   > ![Log Analytics pagina impostazioni di diagnostica](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Aprire una finestra di comando di Windows PowerShell e accedere alla sottoscrizione di Azure usando il `Connect-AzAccount` comando. Quindi, seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

2. Impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vuole abilitare la registrazione.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Abilitare i log usando il cmdlet di PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) insieme al `StorageAccountId` parametro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento con l'ID risorsa del servizio BLOB. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile utilizzare `StorageRead` , `StorageWrite` e `StorageDelete` per il valore del parametro **Category** .

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Per una descrizione di ogni parametro, vedere [archiviare i log delle risorse di Azure tramite Azure PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Abilitare i log usando il cmdlet di PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `EventHubAuthorizationRuleId` parametro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Per una descrizione di ogni parametro, vedere [trasmettere i dati a hub eventi tramite i cmdlet di PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il cmdlet di PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il `WorkspaceId` parametro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operatons-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Ecco un esempio:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Per altre informazioni, vedere [trasmettere log delle risorse di Azure all'area di lavoro log Analytics in monitoraggio di Azure](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Prima di tutto aprire [Azure Cloud Shell](../../cloud-shell/overview.md) oppure un'applicazione console dei comandi come Windows PowerShell, se si dispone dell’interfaccia della riga di comando di Azure [installata](/cli/azure/install-azure-cli) in locale.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione per cui si vuole abilitare i log.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

#### <a name="archive-logs-to-a-storage-account"></a>Archiviare i log in un account di archiviazione

Abilitare i log usando il comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Sostituire il `<storage-service-resource--id>` segnaposto in questo frammento con il servizio di archiviazione BLOB con ID risorsa. L'ID della risorsa si trova nel portale di Azure, nella **pagina delle proprietà** del proprio account di archiviazione.

È possibile utilizzare `StorageRead` , `StorageWrite` e `StorageDelete` per il valore del parametro **Category** .

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Per una descrizione di ogni parametro, vedere [archiviare i log delle risorse tramite l'interfaccia della](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage)riga di comando di Azure.

#### <a name="stream-logs-to-an-event-hub"></a>Trasmettere i log a un hub eventi

Abilitare i log usando il comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Per una descrizione di ogni parametro, vedere [trasmettere dati a hub eventi tramite l'interfaccia](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)della riga di comando di Azure.

#### <a name="send-logs-to-log-analytics"></a>Inviare log a Log Analytics

Abilitare i log usando il comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Ecco un esempio:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Per altre informazioni, vedere [trasmettere log delle risorse di Azure all'area di lavoro log Analytics in monitoraggio di Azure](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Modello](#tab/template)

Per visualizzare un modello di Azure Resource Manager che crea un'impostazione di diagnostica, vedere [impostazione di diagnostica per archiviazione di Azure](../../azure-monitor/samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analisi delle metriche

È possibile analizzare le metriche di Archiviazione di Azure con metriche di altri servizi di Azure usando Esplora metriche. Aprire Esplora metriche selezionando **Metrica** dal menu di **Monitoraggio di Azure**. Per informazioni dettagliate sull'uso di questo strumento, vedere [Introduzione a Esplora metriche di Azure](../../azure-monitor/platform/metrics-getting-started.md). 

L'esempio seguente mostra come visualizzare le **transazioni** a livello di account.

![Screenshot dell'accesso alle metriche nel portale di Azure](./media/monitor-blob-storage/access-metrics-portal.png)

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato. L'esempio seguente mostra come visualizzare le **transazioni** a livello di account su un'operazione specifica selezionando valori per la dimensione **API Name**.

![Screenshot dell'accesso alle metriche con dimensioni nel portale di Azure](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Per un elenco completo delle dimensioni supportate da Archiviazione di Azure, vedere [Dimensioni delle metriche](monitor-blob-storage-reference.md#metrics-dimensions).

Le metriche per l'archiviazione BLOB di Azure sono disponibili in questi spazi dei nomi: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

Per un elenco di tutte le metriche di supporto di monitoraggio di Azure, che includono l'archiviazione BLOB di Azure, vedere [metriche supportate di monitoraggio](../../azure-monitor/platform/metrics-supported.md)di Azure.


### <a name="accessing-metrics"></a>Accesso alle metriche

> [!TIP]
> Per visualizzare esempi dell'interfaccia della riga di comando di Azure o di .NET, scegliere le schede corrispondenti di seguito.

### <a name="net"></a>[.NET](#tab/azure-portal)

Monitoraggio di Azure fornisce l'[SDK di .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) per consentire la lettura di definizioni e valori della metrica. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. Per le metriche di archiviazione, usare `0.18.0-preview` o una versione successiva.
 
In questi esempi sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o del servizio di archiviazione BLOB. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

Sostituire la variabile `<subscription-ID>` con l'ID della propria sottoscrizione. Per istruzioni su come ottenere i valori per `<tenant-ID>`, `<application-ID>` e `<AccessKey>`, vedere [Usare il portale per creare un'applicazione Azure Active Directory (Azure AD) e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md). 

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Elenco della definizione di metrica

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio di archiviazione BLOB. Usare il cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa del servizio di archiviazione BLOB.  Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lettura dei valori delle metriche

È possibile leggere i valori delle metriche a livello di account dell'account di archiviazione o del servizio di archiviazione BLOB. Usare il cmdlet [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Elenco della definizione di metrica a livello di account

È possibile elencare la definizione della metrica dell'account di archiviazione o del servizio di archiviazione BLOB. Usare il comando [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
In questo esempio, sostituire il `<resource-ID>` segnaposto con l'ID risorsa dell'intero account di archiviazione o l'ID risorsa del servizio di archiviazione BLOB. Gli ID delle risorse si trovano nella **pagina delle proprietà** del proprio account di archiviazione nel portale di Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Lettura dei valori di metrica a livello di account

È possibile leggere i valori delle metriche dell'account di archiviazione o del servizio di archiviazione BLOB. Usare il comando [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Modello](#tab/template)

N/D.

---

## <a name="analyzing-logs"></a>Analisi dei log

È possibile accedere ai log delle risorse come BLOB in un account di archiviazione, come dati degli eventi o tramite query di Log Analytics.

Per un riferimento dettagliato dei campi visualizzati in questi log, vedere riferimento ai [dati di monitoraggio dell'archiviazione BLOB di Azure](monitor-blob-storage-reference.md).

> [!NOTE]
> I log di Archiviazione di Azure in Monitoraggio di Azure si trovano in anteprima pubblica ed è possibile verificare l'anteprima in tutte le aree del cloud pubblico. Per registrarsi all'anteprima, vedere [questa pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Questa anteprima abilita i log per BLOB (che includono Azure Data Lake Storage Gen2), file, code, tabelle, account di archiviazione Premium negli account di archiviazione per utilizzo generico v1 e utilizzo generico v2. Gli account di archiviazione della versione classica non sono supportati.

Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se, ad esempio, un account di archiviazione presenta un'attività nell'endpoint BLOB ma non negli endpoint tabella o coda, saranno creati solo log relativi al servizio BLOB. I log di Archiviazione di Azure contengono informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione. Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo.

### <a name="log-authenticated-requests"></a>Richieste di registrazione autenticate

 Vengono registrati i seguenti tipi di richieste autenticate:

- Richieste riuscite
- Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo
- Richieste che usano una firma di accesso condiviso o OAuth, incluse le richieste riuscite e non riuscite
- Richieste ai dati di analisi (dati di log classici nel contenitore **$logs** e dati di metrica della classe nelle tabelle **$metric**)

Le richieste effettuate dal servizio di archiviazione BLOB, ad esempio la creazione o l'eliminazione di log, non vengono registrate. Per un elenco completo dei dati registrati vedere [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato del log di Analisi archiviazione](monitor-blob-storage-reference.md).

### <a name="log-anonymous-requests"></a>Richieste di registrazione anonime

 Vengono registrati i seguenti tipi di richieste anonime:

- Richieste riuscite
- Errori server
- Errori di timeout per client e server
- Richieste GET non riuscite con codice di errore 304 (non modificate)

Tutte le altre richieste anonime non riuscite non vengono registrate. Per un elenco completo dei dati registrati vedere [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato del log di Analisi archiviazione](monitor-blob-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Accesso ai log in un account di archiviazione

I log vengono visualizzati come BLOB archiviati in un contenitore nell'account di archiviazione di destinazione. I dati vengono raccolti e archiviati all'interno di un singolo BLOB come payload JSON delimitato da righe. Il nome del BLOB si basa sulla convenzione di denominazione seguente:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ad esempio:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Accesso ai log in un hub eventi

I log inviati a un hub eventi non vengono archiviati come file, ma è possibile verificare che l'hub eventi abbia ricevuto le informazioni del log. Passare all'hub eventi nel portale di Azure e verificare che il numero di **Messaggi in arrivo** sia maggiore di zero. 

![Log di controllo](media/monitor-blob-storage/event-hub-log.png)

È possibile accedere e leggere i dati di log inviati all'hub eventi usando le informazioni di sicurezza e gli strumenti di monitoraggio e gestione degli eventi. Per altre informazioni, vedere [Quali operazioni si possono eseguire con i dati di monitoraggio inviati all'hub eventi?](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Accesso ai log in un'area di lavoro Log Analytics

È possibile accedere ai log inviati a un'area di lavoro Log Analytics usando le query di log di Monitoraggio di Azure.

Per altre informazioni, vedere [Introduzione a Log Analytics in Monitoraggio di Azure](../../azure-monitor/log-query/log-analytics-tutorial.md).

I dati vengono archiviati nella tabella **StorageBlobLog** . I log per Data Lake Storage Gen2 non vengono visualizzati in una tabella dedicata. Questo perché Data Lake Storage Gen2 non è un servizio. Si tratta di un set di funzionalità che è possibile abilitare nell'account di archiviazione. Se queste funzionalità sono state abilitate, i log continueranno a essere visualizzati nella tabella StorageBlobLogs. 

#### <a name="sample-kusto-queries"></a>Query Kusto di esempio

Di seguito sono riportate alcune query che è possibile immettere nella barra di **Ricerca log** per semplificare il monitoraggio dell'archiviazione BLOB. Queste query usano il [nuovo linguaggio](../../azure-monitor/log-query/log-query-overview.md).

> [!IMPORTANT]
> Quando si seleziona **log** dal menu del gruppo di risorse dell'account di archiviazione, log Analytics viene aperto con l'ambito della query impostato sul gruppo di risorse corrente. Ciò significa che le query di log includeranno solo i dati del gruppo di risorse. Se si vuole eseguire una query che includa dati da altre risorse o dati di altri servizi di Azure, selezionare **registri** dal menu **monitoraggio di Azure** . Per i dettagli, vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](../../azure-monitor/log-query/scope.md).

Usare queste query per agevolare il monitoraggio degli account di Archiviazione di Azure:

* Per elencare i 10 errori più comuni negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Per elencare le prime 10 operazioni che hanno causato la maggior parte degli errori negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Per elencare le prime 10 operazioni con la latenza end-to-end più lunga negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Per elencare tutte le operazioni che hanno causato errori di limitazione lato server negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Per elencare tutte le richieste con accesso anonimo negli ultimi tre giorni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Per creare un grafico a torta delle operazioni utilizzate negli ultimi tre giorni.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Domande frequenti

**Archiviazione di Azure supporta le metriche per i dischi gestiti o non gestiti?**

No. Calcolo di Azure supporta le metriche relative ai dischi. Per altre informazioni, vedere [Per disk metrics for Managed and Unmanaged Disks](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) (Metriche per dischi gestiti e non gestiti).

## <a name="next-steps"></a>Passaggi successivi

- Per un riferimento dei log e delle metriche creati dall'archiviazione BLOB di Azure, vedere [informazioni di riferimento sui dati di monitoraggio dell'archiviazione BLOB di Azure](monitor-blob-storage-reference.md).
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con monitoraggio di Azure](../../azure-monitor/insights/monitor-azure-resource.md).
- Per altre informazioni sulla migrazione delle metriche, vedere [Migrazione delle metriche di Archiviazione di Azure](../common/storage-metrics-migration.md).