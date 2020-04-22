---
title: Usare le impostazioni di diagnostica per raccogliere le metriche e i log della piattaforma e in AzureUse diagnostic settings to collect platform metrics and logs and in Azure
description: Inviare le metriche e i log della piattaforma di Monitoraggio di Azure ai log di monitoraggio di Azure, all'archiviazione di Azure o agli hub eventi di Azure usando un'impostazione di diagnostica.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681231"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Creare l'impostazione di diagnostica per raccogliere log delle risorse e metriche in AzureCreate diagnostic setting to collect resource logs and metrics in Azure

[I log della piattaforma](platform-logs-overview.md) in Azure, inclusi il log attività di Azure e i log delle risorse, forniscono informazioni dettagliate su diagnostica e controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono. [Le metriche](data-platform-metrics.md) della piattaforma vengono raccolte per impostazione predefinita e in genere archiviate nel database delle metriche di Monitoraggio di Azure.Platform metrics are collected by default and typically stored in the Azure Monitor metrics database.

Questo articolo fornisce informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per l'invio di metriche della piattaforma e log della piattaforma a destinazioni diverse.

> [!IMPORTANT]
> Prima di creare un'impostazione di diagnostica per raccogliere il log attività, è necessario disabilitare qualsiasi configurazione legacy. Per informazioni dettagliate, vedere Raccogliere il log attività di [Azure con le impostazioni legacy.](diagnostic-settings-legacy.md)

Ogni risorsa di Azure richiede la propria impostazione di diagnostica, che definisce i criteri seguenti:Each Azure resource requires its own diagnostic setting, which defines the following criteria:

- Categorie di log e dati di metriche inviati alle destinazioni definite nell'impostazione. Le categorie disponibili variano per i diversi tipi di risorsa.
- una o più destinazioni a cui inviare i log. Le destinazioni correnti includono l'area di lavoro di Log Analytics, Hub eventi e Archiviazione di Azure.

Una singola impostazione di diagnostica può definire non più di una delle destinazioni. Se si vogliono inviare dati a più tipi specifici di destinazione (ad esempio, due diverse aree di lavoro di Log Analytics), creare più impostazioni. Ogni risorsa può avere al massimo 5 impostazioni di diagnostica.

> [!NOTE]
> [Le metriche della piattaforma](metrics-supported.md) vengono raccolte automaticamente nelle metriche di [Monitoraggio di Azure.Platform metrics](data-platform-metrics.md)are collected automatically to Azure Monitor Metrics . Le impostazioni di diagnostica possono essere usate per raccogliere metriche per determinati servizi di Azure nei log di monitoraggio di Azure per l'analisi con altri dati di monitoraggio tramite query di [log.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Destinations

I log e le metriche della piattaforma possono essere inviati alle destinazioni nella tabella seguente. Seguire ogni collegamento nella tabella seguente per informazioni dettagliate sull'invio di dati a tale destinazione.

| Destination | Descrizione |
|:---|:---|
| [Area di lavoro Log Analytics](resource-logs-collect-workspace.md) | La raccolta di log e metriche in un'area di lavoro di Log Analytics consente di analizzarli con altri dati di monitoraggio raccolti da Monitoraggio di Azure usando potenti query di log e anche di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni. |
| [Hub eventi](resource-logs-stream-event-hubs.md) | L'invio di log e metriche agli hub eventi consente di trasmettere dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di analisi dei log. |
| [Account di archiviazione di AzureAzure storage account](resource-logs-collect-storage.md) | L'archiviazione di log e metriche in un account di archiviazione di Azure è utile per il controllo, l'analisi statica o il backup. Rispetto ai log di Monitoraggio di Azure e a un'area di lavoro di Log Analytics, Archiviazione di Azure è meno costosa e i log possono essere conservati in tale posizione a tempo indeterminato. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Creare impostazioni di diagnostica nel portale di AzureCreate diagnostic settings in Azure portal

È possibile configurare le impostazioni di diagnostica nel portale di Azure dal menu Monitoraggio di Azure o dal menu della risorsa.

1. La posizione in cui si configurano le impostazioni di diagnostica nel portale di Azure dipendono dalla risorsa.

   - Per una singola risorsa, fare clic su **Impostazioni di diagnostica** in **Monitoraggio** nel menu della risorsa.

        ![Impostazioni di diagnostica](media/diagnostic-settings/menu-resource.png)

   - Per una o più risorse, fare clic su **Impostazioni di diagnostica** in **Impostazioni** nel menu Monitoraggio di Azure e quindi fare clic sulla risorsa.

      ![Impostazioni di diagnostica](media/diagnostic-settings/menu-monitor.png)

   - Per il log attività, fare clic su **Log attività** nel menu Monitoraggio **di Azure** e quindi Impostazioni **di diagnostica**. Assicurarsi di disabilitare qualsiasi configurazione legacy per il log attività. Per informazioni [dettagliate, vedere Disabilitare le impostazioni esistenti.](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)

        ![Impostazioni di diagnostica](media/diagnostic-settings/menu-activity-log.png)

2. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su **Aggiungi impostazione di diagnostica**.

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-settings/add-setting.png)

   Se nella risorsa sono presenti impostazioni esistenti, viene visualizzato un elenco di impostazioni già configurate. Fare clic su **Aggiungi impostazione diagnostica** per aggiungere una nuova impostazione o **su Modifica impostazione** per modificarne una esistente. Ogni impostazione non può avere più di uno dei tipi di destinazione.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/edit-setting.png)

3. Assegna un nome alla tua impostazione se non ne ha già uno.

    ![Aggiungi impostazione diagnostica](media/diagnostic-settings/setting-new-blank.png)

4. **Dettagli categoria (cosa instradare):** selezionare la casella per ogni categoria di dati che si desidera inviare alle destinazioni specificate in un secondo momento. L'elenco delle categorie varia per ogni servizio di Azure.The list of categories varies for each Azure service.

     - **AllMetrics** indirizza le metriche della piattaforma di una risorsa nell'archivio dei log di Azure, ma in forma di log. Queste metriche vengono in genere inviate solo al database della serie temporale delle metriche di Monitoraggio di Azure.These metrics are usually sent only to the Azure Monitor metrics time-series database. Inviandoli all'archivio log di Monitoraggio di Azure (ricercabile tramite Log Analytics) per integrarli nelle query che eseguono ricerche in altri log. Questa opzione potrebbe non essere disponibile per tutti i tipi di risorse. Quando è supportato, [le metriche supportate](metrics-supported.md) da Monitoraggio di Azure elencano le metriche raccolte per i tipi di risorse.

       > [!NOTE]
       > L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
       >
       > *Ad esempio:* la metrica 'IOReadBytes' su una Blockchain può essere esplorata e tracciata a livello di nodo. Tuttavia, quando viene esportata tramite impostazioni di diagnostica, la metrica esportata rappresenta come tutti i byte di lettura per tutti i nodi.

     - **Registri** elenca le diverse categorie disponibili a seconda del tipo di risorsa. Selezionare le categorie che si desidera instradare a una destinazione.

5. **Dettagli destinazione:** selezionare la casella per ogni destinazione. Quando si seleziona ogni casella, vengono visualizzate opzioni che consentono di aggiungere ulteriori informazioni.

      ![Invia a Log Analytics o Hub eventi](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics:** immettere la sottoscrizione e l'area di lavoro.  Se non si dispone di un'area di lavoro, è necessario [crearne una prima](../learn/quick-create-workspace.md)di procedere.

    1. **Hub eventi:** specificare i criteri seguenti:Event hubs - Specify the following criteria:
       - La sottoscrizione di cui fa parte l'hub eventi
       - Lo spazio dei nomi dell'hub eventi: se non ne è ancora presente uno, è necessario [crearne uno](../../event-hubs/event-hubs-create.md)
       - Un nome dell'hub eventi (facoltativo) a cui inviare tutti i dati. Se non si specifica un nome, viene creato un hub eventi per ogni categoria di log. Se si inviano più categorie, è possibile specificare un nome per limitare il numero di hub eventi creati. Per informazioni dettagliate, vedere Quote e limiti di Hub eventi di [Azure.See Azure Event Hubs quotas and limits](../../event-hubs/event-hubs-quotas.md) s.
       - Un criterio dell'hub eventi (facoltativo) Un criterio definisce le autorizzazioni di cui dispone il meccanismo di streaming. Per ulteriori informazioni, consultate [Eventi-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Archiviazione:** scegliere la sottoscrizione, l'account di archiviazione e i criteri di conservazione.

        ![Invia a storage](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Valutare la possibilità di impostare i criteri di conservazione su 0 e di eliminare manualmente i dati dall'archiviazione utilizzando un processo pianificato per evitare possibili confusioni in futuro.
        >
        > In primo luogo, se si utilizza l'archiviazione per l'archiviazione, in genere si desidera che i dati siano in giro per più di 365 giorni. In secondo luogo, se si sceglie un criterio di conservazione maggiore di 0, la data di scadenza viene collegata ai registri al momento dell'archiviazione. Non è possibile modificare la data per tali registri una volta archiviati.
        >
        > Ad esempio, se si imposta il criterio di conservazione per *WorkflowRuntime* su 180 giorni e quindi 24 ore dopo lo si imposta su 365 giorni, i registri archiviati durante le prime 24 ore verranno eliminati automaticamente dopo 180 giorni, mentre tutti i registri successivi di quel tipo verranno eliminati automaticamente dopo 365 giorni. La modifica dei criteri di conservazione in un secondo momento non consente di mantenere le prime 24 ore di log per 365 giorni.

6. Fare clic su **Salva**.

Dopo alcuni istanti, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log vengono trasmessi alle destinazioni specificate man mano che vengono generati nuovi dati dell'evento. Possono essere decollati fino a 15 minuti tra il momento in cui un evento viene generato e il momento in cui [viene visualizzato in un'area di lavoro](data-ingestion-time.md)di Log Analytics .

## <a name="create-diagnostic-settings-using-powershell"></a>Creare impostazioni di diagnostica tramite PowerShellCreate diagnostic settings using PowerShell

Utilizzare il cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) per creare un'impostazione di diagnostica con [Azure PowerShell.](powershell-quickstart-samples.md) Vedere la documentazione di questo cmdlet per le descrizioni dei relativi parametri.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure.You cannot use this method for the Azure Activity log. Usare invece [Crea impostazione di diagnostica in Monitoraggio di Azure usando un modello](diagnostic-settings-template.md) di Resource Manager per creare un modello di Resource Manager e distribuirlo con PowerShell.Instead, use Create diagnostic setting in Azure Monitor using a Resource Manager template to create a Resource Manager template and deploy it with PowerShell.

Di seguito è riportato un cmdlet di PowerShell di esempio per creare un'impostazione di diagnostica usando tutte e tre le destinazioni.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Creare impostazioni di diagnostica usando l'interfaccia della riga di comando di AzureCreate diagnostic settings using Azure CLI

Usare il comando [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) per creare un'impostazione di diagnostica con [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)di comando di Azure. Vedere la documentazione di questo comando per le descrizioni dei relativi parametri.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure.You cannot use this method for the Azure Activity log. Usare invece [Crea impostazione di diagnostica in Monitoraggio di Azure usando un modello](diagnostic-settings-template.md) di Resource Manager per creare un modello di Resource Manager e distribuirlo con l'interfaccia della riga di comando.

Di seguito è riportato un comando dell'interfaccia della riga di comando di esempio per creare un'impostazione di diagnostica usando tutte e tre le destinazioni.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configurare le impostazioni di diagnostica usando l'API RESTConfigure diagnostic settings using REST API

Vedere [Impostazioni di diagnostica](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) per creare o aggiornare le impostazioni di diagnostica usando l'API REST di Azure Monitor.See Diagnostic Settings to create or update diagnostic settings using the Azure Monitor REST [API](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configurare le impostazioni di diagnostica usando il modello di Resource ManagerConfigure diagnostic settings using Resource Manager template

Vedere [Creare impostazioni di diagnostica in Monitoraggio di Azure usando un modello](diagnostic-settings-template.md) di Resource Manager per creare o aggiornare le impostazioni di diagnostica con un modello di Resource Manager.See Create diagnostic setting in Azure Monitor using a Resource Manager template to create or update diagnostic settings with a Resource Manager template.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui log della piattaforma AzureRead more about Azure platform Logs](platform-logs-overview.md)
