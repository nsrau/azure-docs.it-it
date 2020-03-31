---
title: Creare un'impostazione di diagnostica per raccogliere log e metriche in Azure
description: Creare impostazioni di diagnostica per inoltrare i log della piattaforma Azure ai log di monitoraggio di Azure, all'archiviazione di Azure o agli hub eventi di Azure.Create diagnostic settings to forward Azure platform logs to Azure Monitor Logs, Azure storage, or Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672413"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Creare l'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in AzureCreate diagnostic setting to collect platform logs and metrics in Azure
[I log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività di Azure e i log delle risorse, forniscono informazioni dettagliate su diagnostica e controllo per le risorse di Azure e la piattaforma di Azure da cui dipendono. In questo articolo vengono fornite informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per l'invio di log della piattaforma a destinazioni diverse.

> [!IMPORTANT]
> Prima di creare un'impostazione di diagnostica per raccogliere il log attività, è necessario disabilitare qualsiasi configurazione legacy. Per informazioni dettagliate, vedere Raccogliere il log attività di [Azure con le impostazioni legacy.](diagnostic-settings-legacy.md)

Ogni risorsa di Azure richiede la propria impostazione di diagnostica, che definisce quanto segue:Each Azure resource requires its own diagnostic setting, which defines the following:

- Categorie di log e dati di metriche inviati alle destinazioni definite nell'impostazione. Le categorie disponibili variano per i diversi tipi di risorsa.
- una o più destinazioni a cui inviare i log. Le destinazioni correnti includono l'area di lavoro di Log Analytics, Hub eventi e Archiviazione di Azure.
 
Una singola impostazione di diagnostica può definire non più di una delle destinazioni. Se si vogliono inviare dati a più tipi specifici di destinazione (ad esempio, due diverse aree di lavoro di Log Analytics), creare più impostazioni. Ogni risorsa può avere al massimo 5 impostazioni di diagnostica.


> [!NOTE]
> [Le metriche della piattaforma](metrics-supported.md) vengono raccolte automaticamente nelle metriche di [Monitoraggio di Azure.Platform metrics](data-platform-metrics.md)are collected automatically to Azure Monitor Metrics . Le impostazioni di diagnostica possono essere usate per raccogliere metriche per determinati servizi di Azure nei log di monitoraggio di Azure per l'analisi con altri dati di monitoraggio tramite query di [log.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Destinations 
I log della piattaforma possono essere inviati alle destinazioni nella tabella seguente. La configurazione per ogni destinazione viene eseguita utilizzando lo stesso processo per la creazione di impostazioni di diagnostica descritte in questo articolo. Seguire ogni collegamento nella tabella seguente per informazioni dettagliate sull'invio di dati a tale destinazione.

| Destination | Descrizione |
|:---|:---|
| [Area di lavoro di Log Analytics](resource-logs-collect-workspace.md) | La raccolta di log in un'area di lavoro di Log Analytics consente di analizzarli con altri dati di monitoraggio raccolti da Monitoraggio di Azure usando potenti query di log e anche di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni. |
| [Hub eventi](resource-logs-stream-event-hubs.md) | L'invio di log agli hub eventi consente di trasmettere dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di analisi dei log. |
| [Account di archiviazione di AzureAzure storage account](resource-logs-collect-storage.md) | L'archiviazione dei log in un account di archiviazione di Azure è utile per il controllo, l'analisi statica o il backup. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Creare impostazioni di diagnostica nel portale di AzureCreate diagnostic settings in Azure portal
È possibile configurare le impostazioni di diagnostica nel portale di Azure dal menu Monitoraggio di Azure o dal menu della risorsa.

1. La posizione in cui si configurano le impostazioni di diagnostica nel portale di Azure dipendono dalla risorsa.

   - Per una singola risorsa, fare clic su **Impostazioni di diagnostica** in **Monitoraggio** nel menu della risorsa.

        ![Impostazioni di diagnostica](media/diagnostic-settings/menu-resource.png)

    - Per una o più risorse, fare clic su **Impostazioni di diagnostica** in **Impostazioni** nel menu Monitoraggio di Azure e quindi fare clic sulla risorsa.
    
        ![Impostazioni di diagnostica](media/diagnostic-settings/menu-monitor.png)

    - Per il log attività, fare clic su **Log attività** nel menu Monitoraggio **di Azure** e quindi Impostazioni **di diagnostica**. Assicurarsi di disabilitare qualsiasi configurazione legacy per il log attività. Per informazioni [dettagliate, vedere Disabilitare le impostazioni esistenti.](diagnostic-settings-legacy.md#disable-existing-settings)

        ![Impostazioni di diagnostica](media/diagnostic-settings/menu-activity-log.png)

2. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su **Aggiungi impostazione di diagnostica**.

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-settings/add-setting.png)

   Se nella risorsa sono presenti impostazioni esistenti, verrà visualizzato un elenco di impostazioni già configurate. Fare clic su **Aggiungi impostazione diagnostica** per aggiungere una nuova impostazione o **su Modifica impostazione** per modificarne una esistente. Ogni impostazione non può avere più di uno dei tipi di destinazione.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/edit-setting.png)

3. Assegna un nome alla tua impostazione se non ne ha già uno.
4. Selezionare la casella per ogni destinazione per inviare i registri. Fare clic su **Configura** per specificarne le impostazioni come descritto nella tabella seguente.

    | Impostazione | Descrizione |
    |:---|:---|
    | Area di lavoro Log Analytics | Nome dell'area di lavoro. |
    | Account di archiviazione | Nome dell'account di archiviazione. |
    | Spazio dei nomi dell'hub eventi | Spazio dei nomi in cui viene creato l'hub eventi (se questa è la prima volta che si esegue il log di streaming) o trasmesso in streaming (se sono già presenti risorse che eseguono il streaming di tale categoria di log a questo spazio dei nomi).
    | Nome hub eventi | Facoltativamente, specificare un nome dell'hub eventi per inviare tutti i dati nell'impostazione. Se non si specifica un nome, viene creato un hub eventi per ogni categoria di log. Se si inviano più categorie, è possibile specificare un nome per limitare il numero di hub eventi creati. Per informazioni dettagliate, vedere Quote e limiti di Hub eventi di [Azure.See Azure Event Hubs quotas and limits](../../event-hubs/event-hubs-quotas.md) s. |
    | Nome criteri hub eventi | Definisce le autorizzazioni di cui dispone il meccanismo di streaming. |

    !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/setting-details.png)

5. Selezionare la casella per ciascuna delle categorie di dati da inviare alle destinazioni specificate. L'elenco delle categorie varia per ogni servizio di Azure.The list of categories will vary for each Azure service.

   > [!NOTE]
   > L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
   >
   > *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.

6. Fare clic su **Salva**.

Dopo alcuni istanti, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log vengono trasmessi alle destinazioni specificate man mano che vengono generati nuovi dati dell'evento. Si noti che possono essere presenti fino a quindici minuti tra il momento in cui viene generato un evento e la [visualizzazione in un'area](data-ingestion-time.md)di lavoro di Log Analytics .



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

* [Altre informazioni sui log della piattaforma AzureRead more about Azure platform Logs](platform-logs-overview.md)
