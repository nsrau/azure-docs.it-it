---
title: Esportare il log attività di Azure
description: Esportare il log attività di Azure nell'archiviazione per l'archiviazione o Hub eventi di Azure per l'esportazione all'esterno di Azure.Export Azure Activity log to storage for archiving or Azure Event Hubs for exporting outside of Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096287"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Esportare il log attività di Azure nell'archiviazione o nelle hub eventi di AzureExport Azure Activity log to storage or Azure Event Hubs

> [!IMPORTANT]
> Il metodo per l'invio del log attività di Azure ad Archiviazione di Azure e agli hub eventi di Azure è stato modificato in [impostazioni di diagnostica.](diagnostic-settings.md) In questo articolo viene descritto il metodo legacy che è in fase di essere deprecato. Per un confronto, vedere Aggiornamento alla [raccolta e all'esportazione](diagnostic-settings-legacy.md) del log attività di Azure.See Update to Azure Activity log collection and export for a comparison.


Il [log attività di Azure](platform-logs-overview.md) fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione che si sono verificati nella sottoscrizione di Azure.The Azure Activity Log provides insight into subscription-level events that have occurred in your Azure subscription. Oltre a visualizzare il log attività nel portale di Azure o a copiarlo in un'area di lavoro di Log Analytics in cui può essere analizzato con altri dati raccolti da Monitoraggio di Azure, è possibile creare un profilo di log per archiviare il log attività in un account di archiviazione di Azure o trasmetterlo in un Hub eventi.

## <a name="archive-activity-log"></a>Registro attività archiviazione
L'archiviazione del registro attività in un account di archiviazione è utile se si desidera conservare i dati del log per più di 90 giorni (con controllo completo sui criteri di conservazione) per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni o meno, non è necessario configurare l'archiviazione in un account di archiviazione, poiché gli eventi del log attività vengono mantenuti nella piattaforma Azure per 90 giorni.

## <a name="stream-activity-log-to-event-hub"></a>Registro attività flusso all'hub eventi
[Hub eventi di Azure](/azure/event-hubs/) è una piattaforma di streaming dati e un servizio di inserimento eventi in grado di ricevere ed elaborare milioni di eventi al secondo. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Due modi in cui è possibile usare la funzionalità di streaming per il log attività:
* **Trasmettere a sistemi di telemetria e registrazione di terze parti** : in futuro, la funzionalità di trasmissione di Hub eventi di Azure diventerà il meccanismo di invio del log attività a soluzioni di analisi dei log e SIEM di terze parti.
* **Creare una piattaforma di telemetria e registrazione personalizzata** : se si ha già una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi consentono di inserire il log attività con la massima flessibilità.

## <a name="prerequisites"></a>Prerequisiti

### <a name="storage-account"></a>Account di archiviazione
Se si archivia il log attività, è necessario [creare un account di archiviazione](../../storage/common/storage-account-create.md) se non ne è già stato. È consigliabile non usare un account di archiviazione esistente contenente altri dati non di monitoraggio archiviati in modo da poter controllare meglio l'accesso ai dati di monitoraggio. Se si archiviano anche i log e le metriche in un account di archiviazione, è tuttavia possibile scegliere di usare lo stesso account di archiviazione per mantenere tutti i dati di monitoraggio in una posizione centrale.

L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della sottoscrizione che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni. 

> [!TIP]
> Vedere [Configurare i firewall e le reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) di Archiviazione di Azure per fornire l'accesso a un account di archiviazione dietro una rete virtuale protetta.

### <a name="event-hubs"></a>Hub eventi
Se si invia il log attività a un hub eventi, è necessario [creare un hub eventi](../../event-hubs/event-hubs-create.md) se non ne è già presente uno. Se in precedenza sono stati trasmessi eventi del Log attività a questo spazio dei nomi Hub eventi, l'hub eventi verrà riutilizzato.

Il criterio di accesso condiviso definisce le autorizzazioni per il meccanismo di trasmissione. Lo streaming in Hub eventi richiede le autorizzazioni di gestione, invio e ascolto. È possibile creare o modificare i criteri di accesso condiviso per lo spazio dei nomi di Hub eventi nel portale di Azure, nella scheda Configura relativa a tale spazio dei nomi.

Per aggiornare il profilo del log attività per includere lo streaming, è necessario disporre dell'autorizzazione ListKey per la regola di autorizzazione Hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni e queste ultime si trovino entrambe nello stesso tenant AAD.

Trasmettere il log attività a un hub eventi [creando un profilo di log.](#create-a-log-profile)

## <a name="create-a-log-profile"></a>Creare un profilo di logCreate a log profile
Definire la modalità di esportazione del log attività di Azure usando un profilo di **log.** Ogni sottoscrizione di Azure può avere un solo profilo di log. Queste impostazioni possono essere configurate tramite l'opzione **Esporta** nel pannello Log attività nel portale. oppure a livello di codice tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o l'interfaccia della riga di comando.

Il profilo di log definisce quanto segue.

**Dove deve essere inviato il log attività.** Attualmente, le opzioni disponibili sono Account di archiviazione o Hub eventi.

**Quali categorie di eventi devono essere inviate.** Il significato della *categoria* negli eventi Log Profiles e Activity Log è diverso. Nel Profilo log, *Categoria* rappresenta il tipo di operazione (Scrittura, Eliminazione, Azione). In un evento del registro attività, la *categoria*"proprietà z rappresenta l'origine o il tipo di evento (ad esempio, amministrazione, ServiceHealth e alert).

**Quali regioni (località) devono essere esportate.** È necessario includere tutte le posizioni poiché molti eventi nel Log attività sono eventi globali.

**Per quanto tempo il log attività deve essere mantenuto nell'account di archiviazione.** Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 365.

Se i criteri di conservazione sono impostati, ma l'archiviazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.


> [!IMPORTANT]
> È possibile che venga visualizzato un errore durante la creazione di un profilo di log se il provider di risorse Microsoft.Insights non è registrato. Per registrare questo provider, vedere [Tipi e provider di risorse di Azure.See Azure resource providers](../../azure-resource-manager/management/resource-providers-and-types.md) and types to register this provider.


### <a name="create-log-profile-using-the-azure-portal"></a>Creare il profilo di log usando il portale di AzureCreate log profile using the Azure portal

Creare o modificare un profilo di log con l'opzione Esporta nell'hub eventi nel portale di Azure.Create or edit a log profile with the **Export to Event Hub** option in the Azure portal.

1. Dal menu **Monitoraggio di Azure** nel portale di Azure selezionare Log **attività**.
3. Fare clic su **Impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fai clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)

3. Nel pannello visualizzato specificare quanto segue:
   * Regioni con gli eventi da esportare. È necessario selezionare tutte le aree per assicurarsi di non perdere gli eventi chiave poiché il log attività è un log globale (non regionale) e pertanto alla maggior parte degli eventi non è associata un'area.
   * Se si vuole scrivere nell'account di archiviazione:If you want to write to storage account:
       * Account di archiviazione in cui si desidera salvare gli eventi.
       * Il numero di giorni in cui si desidera mantenere questi eventi nell'archivio. (se si impostano 0 giorni, i log vengono conservati all'infinito)
   * Se si desidera scrivere nell'hub eventi:
       * Spazio dei nomi del bus di servizio in cui si desidera creare un hub eventi per lo streaming di questi eventi.

     ![Pannello Esporta log di controllo](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.


### <a name="configure-log-profile-using-powershell"></a>Configurare il profilo di log tramite PowerShellConfigure log profile using PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se esiste già un profilo di log, è innanzitutto necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `Get-AzLogProfile` per determinare se esiste già un profilo di log.  Se esiste un profilo di log, prendere nota della proprietà *name.*

1. Usare `Remove-AzLogProfile` per rimuovere il profilo di log usando il valore della proprietà *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Usare `Add-AzLogProfile` per creare un nuovo profilo di log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | Nome |Sì |Nome del profilo di log. |
    | StorageAccountId |No |ID risorsa dell'account di archiviazione in cui deve essere salvato il log attività. |
    | serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una `{service bus resource ID}/authorizationrules/{key name}`stringa con il formato: . |
    | Location |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
    | RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere mantenuti nell'account di archiviazione, tra 1 e 365. Se il valore è zero, i log vengono conservati all'infinito. |
    | Category |No |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono _Write_, _Delete_e _Action_. |

### <a name="example-script"></a>Script di esempio
Di seguito è riportato uno script di PowerShell di esempio per creare un profilo di log che scrive il log attività sia in un account di archiviazione che in un hub eventi.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurare il profilo di log usando l'interfaccia della riga di comando di AzureConfigure log profile using

Se esiste già un profilo di log, è innanzitutto necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `az monitor log-profiles list` per determinare se esiste già un profilo di log.
2. Usare `az monitor log-profiles delete --name "<log profile name>` per rimuovere il profilo di log usando il valore della proprietà *name*.
3. Usare `az monitor log-profiles create` per creare un nuovo profilo di log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | name |Sì |Nome del profilo di log. |
    | storage-account-id |Sì |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
    | locations |Sì |Elenco delimitato da spazi di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree per la sottoscrizione tramite `az account list-locations --query [].name`. |
    | days |Sì |Numero di giorni per i quali gli eventi devono essere conservati, tra 1 e 365. Se il valore è zero, i log vengono archiviati per un periodo illimitato.  Se zero, il parametro enabled deve essere impostato su false. |
    |Enabled | Sì |True o False.  Consente di abilitare o disabilitare i criteri di conservazione.  Se True, il parametro days deve essere un valore maggiore di 0.
    | Categorie |Sì |Elenco delimitato da spazi di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |



## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sul Log attività](../../azure-resource-manager/management/view-activity-logs.md)
* [Raccogliere il log attività nei log di Monitoraggio di AzureCollect Activity Log into Azure Monitor Logs](activity-log-collect.md)
