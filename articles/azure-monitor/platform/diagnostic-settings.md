---
title: Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse
description: Inviare le metriche e i log della piattaforma di monitoraggio di Azure ai log di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure usando un'impostazione di diagnostica.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 950fcdc5cd6a5bbf3fa61ebd5e23be89691c4370
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95535793"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse
I [log della piattaforma](platform-logs-overview.md) in Azure, inclusi i log attività e i log delle risorse di Azure, forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Le [metriche della piattaforma](data-platform-metrics.md) vengono raccolte per impostazione predefinita e vengono in genere archiviate nel database di metriche di monitoraggio di Azure. Questo articolo fornisce informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per inviare le metriche della piattaforma e i log della piattaforma a destinazioni diverse.

> [!IMPORTANT]
> Prima di creare un'impostazione di diagnostica per il log attività, è necessario disabilitare prima di tutto la configurazione legacy. Per informazioni dettagliate, vedere [metodi di raccolta legacy](activity-log.md#legacy-collection-methods) .

Ogni risorsa di Azure richiede una propria impostazione di diagnostica, che definisce i criteri seguenti:

- Categorie di log e dati di metriche inviati alle destinazioni definite nell'impostazione. Le categorie disponibili variano per i diversi tipi di risorsa.
- una o più destinazioni a cui inviare i log. Le destinazioni correnti includono l'area di lavoro di Log Analytics, Hub eventi e Archiviazione di Azure.

Una singola impostazione di diagnostica può definire non più di una delle destinazioni. Se si vogliono inviare dati a più tipi specifici di destinazione (ad esempio, due diverse aree di lavoro di Log Analytics), creare più impostazioni. Ogni risorsa può avere al massimo 5 impostazioni di diagnostica.

Il video seguente illustra i log della piattaforma di routing con le impostazioni di diagnostica.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> Le [metriche della piattaforma](metrics-supported.md) vengono inviate automaticamente alle metriche di monitoraggio di [Azure](data-platform-metrics.md). È possibile usare le impostazioni di diagnostica per inviare le metriche per determinati servizi di Azure nei log di monitoraggio di Azure per l'analisi con altri dati di monitoraggio usando le [query di log](../log-query/log-query-overview.md) con alcune limitazioni. 
>  
>  
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione. *Ad esempio*: la metrica ' IOReadBytes ' in un blockchain può essere esplorata e mappata in base a un livello per nodo. Tuttavia, quando viene esportato tramite le impostazioni di diagnostica, la metrica esportata rappresenta come tutti i byte letti per tutti i nodi. Inoltre, a causa delle limitazioni interne non tutte le metriche sono esportabili nei log/Log Analytics di monitoraggio di Azure. Per altre informazioni, vedere l' [elenco delle metriche esportabili](metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Per aggirare queste limitazioni per metriche specifiche, è consigliabile estrarle manualmente usando l' [API REST delle metriche](/rest/api/monitor/metrics/list) e importarle nei log di monitoraggio di Azure usando l' [API dell'agente di raccolta dati di monitoraggio di Azure](data-collector-api.md).  


## <a name="destinations"></a>Destinazioni
Le metriche e i log della piattaforma possono essere inviati alle destinazioni nella tabella seguente. 

| Destination | Descrizione |
|:---|:---|
| [area di lavoro Log Analytics](design-logs-deployment.md) | L'invio di log e metriche a un'area di lavoro di Log Analytics consente di analizzarli con altri dati di monitoraggio raccolti da monitoraggio di Azure con potenti query di log e anche per sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni. |
| [Hub eventi](../../event-hubs/index.yml) | L'invio di log e metriche a hub eventi consente di trasmettere i dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di log Analytics.  |
| [Account di archiviazione di Azure](../../storage/blobs/index.yml) | L'archiviazione di log e metriche in un account di archiviazione di Azure è utile per il controllo, l'analisi statica o il backup. Rispetto ai log di monitoraggio di Azure e a un'area di lavoro Log Analytics, archiviazione di Azure è meno costosa e i log possono essere conservati per un periodo illimitato.  |


### <a name="destination-requirements"></a>Requisiti di destinazione

Prima di creare le impostazioni di diagnostica, è necessario creare tutte le destinazioni per l'impostazione di diagnostica. Non è necessario che la destinazione si trovi nella stessa sottoscrizione della risorsa che invia i log, purché l'utente che configura l'impostazione disponga dell'accesso RBAC di Azure appropriato a entrambe le sottoscrizioni. Nella tabella seguente vengono forniti i requisiti univoci per ogni destinazione, incluse eventuali restrizioni a livello di area.

| Destination | Requisiti |
|:---|:---|
| Area di lavoro Log Analytics | Non è necessario che l'area di lavoro si trovi nella stessa area della risorsa monitorata.|
| Hub eventi | Il criterio di accesso condiviso per lo spazio dei nomi definisce le autorizzazioni di cui dispone il meccanismo di streaming. Per lo streaming a hub eventi sono necessarie autorizzazioni di gestione, invio e ascolto. Per aggiornare l'impostazione di diagnostica in modo da includere il flusso, è necessario avere l'autorizzazione ListKey per la regola di autorizzazione di hub eventi.<br><br>Lo spazio dei nomi dell'hub eventi deve trovarsi nella stessa area della risorsa da monitorare se la risorsa è a livello di area. |
| Account di archiviazione di Azure | Non usare un account di archiviazione esistente con altri dati non di monitoraggio archiviati, in modo da poter controllare meglio l'accesso ai dati. Se si archiviano i log attività e i log delle risorse insieme, è possibile scegliere di usare lo stesso account di archiviazione per conservare tutti i dati di monitoraggio in una posizione centrale.<br><br>Per inviare i dati a una risorsa di archiviazione non modificabile, impostare i criteri non modificabili per l'account di archiviazione come descritto in [impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](../../storage/blobs/storage-blob-immutability-policies-manage.md). È necessario seguire tutti i passaggi descritti in questo articolo, inclusa l'abilitazione di scritture BLOB accodati.<br><br>L'account di archiviazione deve trovarsi nella stessa area della risorsa da monitorare se la risorsa è a livello di area. |

> [!NOTE]
> Gli account di Azure Data Lake Storage Gen2 non sono attualmente supportati come destinazione per le impostazioni di diagnostica anche se possono essere elencati come opzioni valide nel portale di Azure.

> [!NOTE]
> Monitoraggio di Azure (impostazioni di diagnostica) non è in grado di accedere alle risorse di hub eventi quando sono abilitate reti virtuali. È necessario abilitare l'impostazione Consenti a servizi Microsoft attendibili di ignorare questo firewall nell'hub eventi, in modo che il servizio monitoraggio di Azure (impostazioni di diagnostica) disponga dell'accesso alle risorse di hub eventi. 


## <a name="create-in-azure-portal"></a>Creare nel portale di Azure

È possibile configurare le impostazioni di diagnostica nella portale di Azure dal menu monitoraggio di Azure o dal menu della risorsa.

1. La configurazione delle impostazioni di diagnostica nella portale di Azure dipende dalla risorsa.

   - Per una singola risorsa, fare clic su **impostazioni di diagnostica** in **monitoraggio** nel menu della risorsa.

        ![Screenshot della sezione monitoraggio di un menu delle risorse in portale di Azure con le impostazioni di diagnostica evidenziate.](media/diagnostic-settings/menu-resource.png)

   - Per una o più risorse, fare clic su **impostazioni di diagnostica** in **Impostazioni** nel menu monitoraggio di Azure e quindi fare clic sulla risorsa.

        ![Screenshot della sezione impostazioni nel menu monitoraggio di Azure con impostazioni di diagnostica evidenziate.](media/diagnostic-settings/menu-monitor.png)

   - Per il log attività, fare clic su **log attività** nel menu **monitoraggio di Azure** e quindi su **impostazioni di diagnostica**. Assicurarsi di disabilitare qualsiasi configurazione legacy per il log attività. Per informazioni dettagliate, vedere [disabilitare le impostazioni esistenti](./activity-log.md#legacy-collection-methods) .

        ![Screenshot del menu monitoraggio di Azure con il log attività selezionato e le impostazioni di diagnostica evidenziate nella barra dei menu Monitor-Activity log.](media/diagnostic-settings/menu-activity-log.png)

2. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su **Aggiungi impostazione di diagnostica**.

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/diagnostic-settings/add-setting.png)

   Se nella risorsa sono presenti impostazioni, viene visualizzato un elenco di impostazioni già configurate. Fare clic su **Aggiungi impostazione di diagnostica** per aggiungere una nuova impostazione o **modificare** un'impostazione per modificarne una esistente. Ogni impostazione può avere non più di uno dei tipi di destinazione.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/diagnostic-settings/edit-setting.png)

3. Assegnare un nome all'impostazione se non ne è già presente uno.

    ![Aggiungi impostazione di diagnostica](media/diagnostic-settings/setting-new-blank.png)

4. **Dettagli categoria (cosa da indirizzare)** : selezionare la casella per ogni categoria di dati che si vuole inviare alle destinazioni specificate in un secondo momento. L'elenco di categorie varia per ogni servizio di Azure.

     - **AllMetrics** instrada le metriche della piattaforma di una risorsa nell'archivio dei log di Azure, ma in forma di log. Queste metriche vengono in genere inviate solo al database di serie temporali delle metriche di monitoraggio di Azure. Inviarli all'archivio dei log di monitoraggio di Azure (ricercabile tramite Log Analytics) consente di integrarli in query che effettuano ricerche negli altri log. Questa opzione potrebbe non essere disponibile per tutti i tipi di risorsa. Quando è supportato, le [metriche supportate da monitoraggio di Azure](metrics-supported.md) elencano le metriche raccolte per i tipi di risorse.

       > [!NOTE]
       > Vedere limitazioni per il routing delle metriche ai log di monitoraggio di Azure più indietro in questo articolo.  


     - **Log** elenca le diverse categorie disponibili a seconda del tipo di risorsa. Controllare le categorie che si desidera indirizzare a una destinazione.

5. **Dettagli destinazione** : selezionare la casella per ogni destinazione. Quando si seleziona ogni casella, vengono visualizzate le opzioni che consentono di aggiungere ulteriori informazioni.

      ![Invia a Log Analytics o hub eventi](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** : immettere la sottoscrizione e l'area di lavoro.  Se non si dispone di un'area di lavoro, è necessario [crearne una prima di procedere](../learn/quick-create-workspace.md).

    1. **Hub eventi** : specificare i criteri seguenti:
       - Sottoscrizione di cui fa parte l'hub eventi
       - Lo spazio dei nomi dell'hub eventi, se non ne è ancora presente uno, sarà necessario [crearne uno](../../event-hubs/event-hubs-create.md)
       - Nome dell'hub eventi (facoltativo) a cui inviare tutti i dati. Se non si specifica un nome, viene creato un hub eventi per ogni categoria di log. Se si inviano più categorie, potrebbe essere necessario specificare un nome per limitare il numero di hub eventi creati. Per informazioni dettagliate, vedere [quote e limiti di hub eventi di Azure](../../event-hubs/event-hubs-quotas.md) .
       - Un criterio dell'hub eventi (facoltativo) definisce le autorizzazioni di cui dispone il meccanismo di streaming. Per altre informazioni, vedere [Event-Hub-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Archiviazione** : scegliere la sottoscrizione, l'account di archiviazione e i criteri di conservazione.

        ![Invia alla risorsa di archiviazione](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > È consigliabile impostare i criteri di conservazione su 0 ed eliminare manualmente i dati dall'archiviazione usando un processo pianificato per evitare possibili confusioni in futuro.
        >
        > Prima di tutto, se si usa l'archiviazione per l'archiviazione, in genere si vuole che i dati vengano superati per più di 365 giorni. In secondo luogo, se si sceglie un criterio di conservazione maggiore di 0, la data di scadenza viene associata ai log al momento dell'archiviazione. Non è possibile modificare la data di questi log una volta archiviati.
        >
        > Se, ad esempio, si impostano i criteri di conservazione per *WorkflowRuntime* su 180 giorni e le 24 ore successive lo si imposta su 365 giorni, i log archiviati durante le prime 24 ore verranno eliminati automaticamente dopo 180 giorni, mentre tutti i registri successivi del tipo verranno eliminati automaticamente dopo 365 giorni. Se si modifica il criterio di conservazione in un secondo momento, le prime 24 ore di log rimaneranno per 365 giorni.

6. Fare clic su **Salva**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log vengono trasmessi alle destinazioni specificate quando vengono generati nuovi dati degli eventi. Potrebbero essere necessari fino a 15 minuti tra il momento in cui viene generato un evento e quando questo viene [visualizzato in un'area di lavoro log Analytics](data-ingestion-time.md).

## <a name="create-using-powershell"></a>Creare usando PowerShell

Usare il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) per creare un'impostazione di diagnostica con [Azure PowerShell](../samples/powershell-samples.md). Per una descrizione dei parametri, vedere la documentazione relativa a questo cmdlet.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure. Usare invece [Crea impostazione diagnostica in monitoraggio di Azure usando un modello di gestione risorse](../samples/resource-manager-diagnostic-settings.md) per creare un modello di gestione risorse e distribuirlo con PowerShell.

Di seguito è riportato un esempio di cmdlet di PowerShell per la creazione di un'impostazione di diagnostica con le tre destinazioni.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Creare usando l'interfaccia della riga di comando

Usare il comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) per creare un'impostazione di diagnostica con l'interfaccia della riga di comando di [Azure](/cli/azure/monitor?view=azure-cli-latest). Per una descrizione dei parametri, vedere la documentazione relativa a questo comando.

> [!IMPORTANT]
> Non è possibile usare questo metodo per il log attività di Azure. Usare invece [Crea impostazione diagnostica in monitoraggio di Azure usando un modello di gestione risorse](../samples/resource-manager-diagnostic-settings.md) per creare un modello di gestione risorse e distribuirlo con l'interfaccia della riga di comando.

Di seguito è riportato un esempio di comando dell'interfaccia della riga di comando per creare un'impostazione di diagnostica con le tre destinazioni

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

## <a name="create-using-resource-manager-template"></a>Crea usando Gestione risorse modello
Per la creazione o l'aggiornamento delle impostazioni di diagnostica con un modello di Gestione risorse, vedere [esempi di gestione risorse modello per le impostazioni di diagnostica in monitoraggio di Azure](../samples/resource-manager-diagnostic-settings.md) .

## <a name="create-using-rest-api"></a>Creare usando l'API REST
Vedere [impostazioni di diagnostica](/rest/api/monitor/diagnosticsettings) per creare o aggiornare le impostazioni di diagnostica usando l' [API REST di monitoraggio di Azure](/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Creare usando criteri di Azure
Poiché è necessario creare un'impostazione di diagnostica per ogni risorsa di Azure, è possibile usare criteri di Azure per creare automaticamente un'impostazione di diagnostica durante la creazione di ogni risorsa. Per informazioni dettagliate, vedere [distribuire monitoraggio di Azure su larga scala usando criteri di Azure](../deploy-scale.md) .


## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più sui log della piattaforma Azure](platform-logs-overview.md)
