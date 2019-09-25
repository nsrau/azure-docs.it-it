---
title: Creare un'impostazione di diagnostica per raccogliere log e metriche in Azure | Microsoft Docs
description: Creare le impostazioni di diagnostica per l'invio dei log della piattaforma Azure ai log di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262518"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure
I [log della piattaforma](resource-logs-overview.md) in Azure forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Questo articolo fornisce informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per raccogliere i log della piattaforma in destinazioni diverse.

Ogni risorsa di Azure richiede una propria impostazione di diagnostica. L'impostazione di diagnostica definisce quanto segue per la risorsa:

- Categorie di log e dati di metrica inviati alle destinazioni definite nell'impostazione. Le categorie disponibili variano a seconda del tipo di risorsa.
- Una o più destinazioni per inviare i log. Le destinazioni correnti includono Log Analytics area di lavoro, Hub eventi e archiviazione di Azure.
- Criteri di conservazione per i dati archiviati in archiviazione di Azure.
 
Una singola impostazione di diagnostica può definire una delle destinazioni. Se si desidera inviare dati a più di un tipo di destinazione specifico, ad esempio due aree di lavoro Log Analytics diverse, creare più impostazioni. Ogni risorsa può avere fino a 5 impostazioni di diagnostica.

> [!NOTE]
> Il log attività può essere inviato alle stesse destinazioni degli altri log della piattaforma, ma non è configurato con le impostazioni di diagnostica. Per informazioni dettagliate, vedere [Panoramica dei log della piattaforma in Azure](platform-logs-overview.md#destinations) .

> [!NOTE]
> Le [metriche della piattaforma](metrics-supported.md) vengono raccolte automaticamente nelle metriche di monitoraggio di [Azure](data-platform-metrics.md). È possibile usare le impostazioni di diagnostica per raccogliere le metriche per determinati servizi di Azure nei log di monitoraggio di Azure per l'analisi con altri dati di monitoraggio usando le [query di log](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinazioni 
I log della piattaforma possono essere inviati alle destinazioni nella tabella seguente. La configurazione per ogni destinazione viene eseguita usando lo stesso processo per la creazione di impostazioni di diagnostica descritte in questo articolo. Per informazioni dettagliate sull'invio di dati a tale destinazione, seguire ogni collegamento nella tabella seguente.

| Destination | Descrizione |
|:---|:---|
| [area di lavoro Log Analytics](resource-logs-collect-workspace.md) | La raccolta di log in un'area di lavoro di Log Analytics consente di analizzarli con altri dati di monitoraggio raccolti da monitoraggio di Azure con potenti query di log e anche per sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni. |
| [Hub eventi](resource-logs-stream-event-hubs.md) | L'invio di log a hub eventi consente di trasmettere i dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di log Analytics. |
| [Account di archiviazione di Azure](resource-logs-collect-storage.md) | L'archiviazione dei log in un account di archiviazione di Azure è utile per il controllo, l'analisi statica o il backup. |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Creare impostazioni di diagnostica in portale di Azure
È possibile configurare le impostazioni di diagnostica nella portale di Azure dal menu monitoraggio di Azure o dal menu della risorsa.

1. Dal menu monitoraggio di Azure nella portale di Azure fare clic su **impostazioni di diagnostica** in **Impostazioni** e quindi fare clic sulla risorsa.

    ![Impostazioni di diagnostica](media/diagnostic-settings/menu-monitor.png)

    In alternativa, dal menu delle risorse nella portale di Azure fare clic su **impostazioni di diagnostica** in **monitoraggio**.

    ![Impostazioni di diagnostica](media/diagnostic-settings/menu-resource.png)

2. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su **Attiva diagnostica**.

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-settings/add-setting.png)

   Se nella risorsa sono presenti impostazioni esistenti, verrà visualizzato un elenco di impostazioni già configurate. Fare clic su **Aggiungi impostazione di diagnostica** per aggiungere una nuova impostazione o **modificare** un'impostazione per modificarne una esistente. Ogni impostazione può avere non più di uno dei tipi di destinazione.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/edit-setting.png)

3. Assegnare un nome all'impostazione se non ne è già presente uno.
4. Selezionare la casella per ogni destinazione per inviare i log. Fare clic su **Configura** per specificare le impostazioni come descritto nella tabella seguente.

    | Impostazione | Descrizione |
    |:---|:---|
    | Area di lavoro Log Analytics | Nome dell'area di lavoro. |
    | Account di archiviazione | Nome dell'account di archiviazione. |
    | Spazio dei nomi dell'hub eventi | Lo spazio dei nomi in cui viene creato l'hub eventi, se è la prima volta che si esegue lo streaming dei log, o se sono già presenti risorse che eseguono il flusso di tale categoria di log a questo spazio dei nomi.
    | Nome hub eventi | Facoltativamente, specificare un nome per l'hub eventi per inviare tutti i dati nell'impostazione. Se non si specifica un nome, viene creato un hub eventi per ogni categoria di log. Se si inviano più categorie, potrebbe essere necessario specificare un nome per limitare il numero di hub eventi creati. Per informazioni dettagliate, vedere [quote e limiti di hub eventi di Azure](../../event-hubs/event-hubs-quotas.md) . |
    | Nome criteri hub eventi | Definisce le autorizzazioni di cui dispone il meccanismo di streaming. |

    !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/setting-details.png)

5. Selezionare la casella per ogni categoria di dati da inviare alle destinazioni specificate. Se è stata selezionata l'opzione per l'archiviazione **in un account di archiviazione**, sarà necessario specificare anche il [periodo di conservazione](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.

4. Fare clic su **Salva**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log vengono trasmessi alle destinazioni specificate quando vengono generati nuovi dati degli eventi. Si noti che potrebbero essere necessari fino a 15 minuti tra il momento in cui viene generato un evento e il momento in cui viene [visualizzato in un'area di lavoro log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Creare impostazioni di diagnostica tramite PowerShell
Usare il cmdlet [set-AzDiagnosticSetting](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting) per creare un'impostazione di diagnostica con [Azure PowerShell](powershell-quickstart-samples.md). Per una descrizione dei parametri, vedere la documentazione relativa a questo cmdlet.

Di seguito è riportato un esempio di cmdlet di PowerShell per la creazione di un'impostazione di diagnostica con le tre destinazioni.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Creare le impostazioni di diagnostica usando l'interfaccia della riga di comando
Usare il comando [AZ monitor Diagnostic-Settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) per creare un'impostazione di diagnostica con l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Per una descrizione dei parametri, vedere la documentazione relativa a questo comando.

Di seguito è riportato un esempio di comando dell'interfaccia della riga di comando per creare un'impostazione di diagnostica con le tre destinazioni



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configurare le impostazioni di diagnostica usando l'API REST
Vedere [impostazioni di diagnostica](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) per creare o aggiornare le impostazioni di diagnostica usando l' [API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configurare le impostazioni di diagnostica usando il modello di Gestione risorse
Vedere [abilitare automaticamente le impostazioni di diagnostica durante la creazione di risorse usando un modello di gestione risorse](diagnostic-settings-template.md) per creare o aggiornare le impostazioni di diagnostica con un modello di gestione risorse.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più sui log della piattaforma Azure](resource-logs-overview.md)