---
title: Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: È possibile gestire l'accesso ai dati archiviati in un'area di lavoro di Log Analytics in Monitoraggio di Azure usando autorizzazioni a livello di risorsa, area di lavoro o tabella. In questo articolo viene descritto in dettaglio come completare.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: b8d7f995997b828c2323b3e6934b97354c2f8c8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255244"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gestire l'accesso per registrare dati e aree di lavoro in Monitoraggio di AzureManage access to log data and workspaces in Azure Monitor

Monitoraggio di Azure archivia i dati [di log](data-platform-logs.md) in un'area di lavoro di Log Analytics.Azure Monitor stores log data in a Log Analytics workspace. Un'area di lavoro è un contenitore che include dati e informazioni di configurazione. Per gestire l'accesso ai dati di log, eseguire varie attività amministrative relative all'area di lavoro.

In questo articolo viene illustrato come gestire l'accesso ai log e amministrare le aree di lavoro che li contengono, incluso come concedere l'accesso a: 

* L'area di lavoro che utilizza le autorizzazioni dell'area di lavoro.
* Utenti che devono accedere al log dei dati da risorse specifiche usando il controllo degli accessi in base al ruolo di Azure.Users who need access to log data from specific resources using Azure role-based access control (RBAC).
* Utenti che devono accedere al log dei dati in una tabella specifica nell'area di lavoro usando il controllo degli accessi in base al ruolo di Azure.Users who need access to log data in a specific table in the workspace using Azure RBAC.

## <a name="configure-access-control-mode"></a>Configurare la modalità di controllo di accesso

È possibile visualizzare la [modalità](design-logs-deployment.md) di controllo di accesso configurata in un'area di lavoro dal portale di Azure o con Azure PowerShell.You can view the access control mode configured on a workspace from the Azure portal or with Azure PowerShell.  È possibile modificare questa impostazione utilizzando uno dei seguenti metodi supportati:

* Portale di Azure

* Azure PowerShell

* Modello di Azure Resource Manager

### <a name="from-the-azure-portal"></a>Dal portale di Azure

È possibile visualizzare la modalità di controllo di accesso dell'area di lavoro corrente nella pagina **Panoramica** per l'area di lavoro nel menu **dell'area di lavoro di Log Analytics.**

![Visualizzare la modalità di controllo dell'accesso all'area di lavoroView workspace control](media/manage-access/view-access-control-mode.png)

1. Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .
1. Nel portale di Azure selezionare Aree di lavoro di Log Analytics >'area di lavoro.

È possibile modificare questa impostazione dalla pagina **Proprietà** dell'area di lavoro. La modifica dell'impostazione sarà disabilitata se non si dispone delle autorizzazioni per configurare l'area di lavoro.

![Modificare la modalità di accesso all'area di lavoro](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Utilizzo di PowerShell

Utilizzare il comando seguente per esaminare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

L'output sarà simile al seguente:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Il valore `False` indica che l'area di lavoro è configurata con la modalità di accesso del contesto dell'area di lavoro.  Il valore `True` indica che l'area di lavoro è configurata con la modalità di accesso del contesto delle risorse.

> [!NOTE]
> Se un'area di lavoro viene restituita senza un valore `False` booleano ed è vuota, corrisponde anche ai risultati di un valore.
>

Utilizzare lo script seguente per impostare la modalità di controllo di accesso per un'area di lavoro specifica sull'autorizzazione del contesto delle risorse:Use the following script to set the access control mode for a specific workspace to the resource-context permission:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Utilizzare lo script seguente per impostare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione per l'autorizzazione del contesto delle risorse:Use the following script to set the access control mode for all workspaces in the subscription to the resource-context permission:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Uso di un modello di Resource Manager

Per configurare la modalità di accesso in un modello di Azure Resource Manager, impostare il flag di funzionalità **enableLogAccessUsingOnlyResourcePermissions** nell'area di lavoro su uno dei valori seguenti.

* **false**: imposta le autorizzazioni del contesto dell'area di lavoro sul contesto dell'area di lavoro. Questa è l'impostazione predefinita se il flag non è impostato.
* **true**: imposta l'area di lavoro sulle autorizzazioni del contesto delle risorse.

## <a name="manage-access-using-workspace-permissions"></a>Gestire l'accesso utilizzando le autorizzazioni dell'area di lavoro

A ogni area di lavoro possono essere associati più account, ognuno dei quali può avere accesso a diverse aree di lavoro. L'accesso viene gestito tramite [l'accesso basato sui ruoli di Azure.](../../role-based-access-control/role-assignments-portal.md)

Le attività seguenti richiedono anche le autorizzazioni di Azure:

|Azione |Autorizzazioni di Azure necessarie |Note |
|-------|-------------------------|------|
| Aggiunta e rimozione di soluzioni di monitoraggio | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Queste autorizzazioni devono essere concesse a livello di gruppo di risorse o di sottoscrizione. |
| Modifica del piano tariffario | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visualizzazione dei dati nei riquadri delle soluzioni *Backup* e *Site Recovery* | Amministratore/Coamministratore | Risorse di accesso distribuite usando il modello di distribuzione classica |
| Creare un'area di lavoro nel portale di Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Visualizzare le proprietà di base dell'area di lavoro e immettere il pannello dell'area di lavoro nel portaleView workspace basic properties and enter the workspace blade in the portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Eseguire query sui log tramite qualsiasi interfacciaQuery logs using any interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Accedere a tutti i tipi di log tramite query | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Accedere a una tabella di log specificaAccess a specific log table | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leggere le chiavi dell'area di lavoro per consentire l'invio di log a questa area di lavoroRead the workspace keys to allow sending logs to this workspace | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gestire l'accesso usando le autorizzazioni di AzureManage access using Azure permissions

Per concedere l'accesso all'area di lavoro Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md). Per i ruoli personalizzati di esempio, vedere [Ruoli personalizzati di esempioFor](#custom-role-examples) example custom roles, see Example custom roles

Azure offre due ruoli utente predefiniti per le aree di lavoro di Log Analytics:

* Lettore di Log Analytics
* Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:

* Visualizzare e cercare tutti i dati di monitoraggio
* Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

Il ruolo di lettore di Log Analytics include le azioni di Azure seguenti:

| Type    | Autorizzazione | Descrizione |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse di Azure e la configurazione delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere le impostazioni dell'area di lavoro ed eseguire query sui dati. Vedi le opzioni più granulari sopra. |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Deprecato, non c'è bisogno di assegnarli agli utenti. |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Deprecato, non c'è bisogno di assegnarli agli utenti. |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti. Ciò impedisce all'utente di aggiungere nuove risorse all'area di lavoro |

I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:

* Include tutti i privilegi del *ruolo Lettore di Log Analytics,* consentendo all'utente di leggere tutti i dati di monitoraggio
* Creare e configurare account di automazione
* Consente di aggiungere e rimuovere soluzioni di gestione

    > [!NOTE]
    > Per eseguire correttamente le ultime due azioni, questa autorizzazione deve essere concessa a livello di gruppo di risorse o di abbonamento.

* Leggere le chiavi dell'account di archiviazioneRead storage account keys
* Configurare la raccolta di log da Archiviazione di AzureConfigure the collection of logs from Azure Storage
* Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  * Aggiunta dell'estensione macchina virtuale alle VM
  * Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE]
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

Il ruolo di collaboratore di Log Analytics include le azioni di Azure seguenti:

| Autorizzazione | Descrizione |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere l'impostazione dell'area di lavoro ed eseguire query sui dati. Vedi le opzioni più granulari sopra. |
| `Microsoft.Automation/automationAccounts/*` | Consente di creare e configurare account di automazione di Azure, inclusa l'aggiunta e modifica di runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Consente di aggiungere, aggiornare e rimuovere estensioni macchina virtuale, inclusa l'estensione Microsoft Monitoring Agent e l'estensione agente OMS per Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Consente di visualizzare la chiave dell'account di archiviazione, è necessaria per configurare Log Analytics per la lettura dei log dagli account di archiviazione di Azure |
| `Microsoft.Insights/alertRules/*` | Consente di aggiungere, aggiornare e rimuovere regole di avviso |
| `Microsoft.Insights/diagnosticSettings/*` | Consente di aggiungere, aggiornare e rimuovere impostazioni di diagnostica dalle risorse di Azure |
| `Microsoft.OperationalInsights/*` | Aggiungere, aggiornare e rimuovere la configurazione per le aree di lavoro di Log Analytics.Add, update, and remove configuration for Log Analytics workspaces. Per modificare le impostazioni `Microsoft.OperationalInsights/workspaces/write`avanzate dell'area di lavoro, l'utente ha bisogno di . |
| `Microsoft.OperationsManagement/*` | Consente di aggiungere e rimuovere soluzioni di gestione |
| `Microsoft.Resources/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |

Per aggiungere e rimuovere utenti da un ruolo utente, è necessario avere le autorizzazioni `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Usare questi ruoli per concedere agli utenti l'accesso ad ambiti diversi:

* Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
* Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
* Risorsa: accesso alla sola area di lavoro specificata

È consigliabile eseguire assegnazioni a livello di risorsa (area di lavoro) per garantire un controllo accurato degli accessi. Usare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

### <a name="resource-permissions"></a>Autorizzazioni per le risorse

Quando gli utenti eseguono query sui log da un'area di lavoro utilizzando l'accesso al contesto delle risorse, dicui avranno le autorizzazioni seguenti per la risorsa:When users query logs from a workspace using resource-context access, they'll have the following permissions on the resource:

| Autorizzazione | Descrizione |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Esempi:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Possibilità di visualizzare tutti i dati di log per la risorsa.  |
| `Microsoft.Insights/diagnosticSettings/write` | Possibilità di configurare l'impostazione di diagnostica per consentire la configurazione dei log per questa risorsa. |

`/read`l'autorizzazione viene in genere concessa da un ruolo che include _\*_ _ \*/read o_ autorizzazioni quali i ruoli [Lettore](../../role-based-access-control/built-in-roles.md#reader) e [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) incorporati. I ruoli personalizzati che includono azioni specifiche o ruoli predefiniti dedicati potrebbero non includere questa autorizzazione.

Vedere [Definizione del controllo](#table-level-rbac) di accesso per tabella di seguito se si desidera creare un controllo di accesso diverso per tabelle diverse.

## <a name="custom-role-examples"></a>Esempi di ruoli personalizzati

1. Per concedere a un utente l'accesso ai dati di log dalle proprie risorse, eseguire le operazioni seguenti:To grant a user access to log data from their resources, perform the following:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per l'utilizzo delle autorizzazioni per **l'area di lavoro o le risorse**

    * Concedere `*/read` agli `Microsoft.Insights/logs/*/read` utenti o alle autorizzazioni le risorse. Se è già assegnato il ruolo [Lettore DiSLog Analytics](../../role-based-access-control/built-in-roles.md#reader) nell'area di lavoro, è sufficiente.

2. Per concedere a un utente l'accesso ai dati di log dalle risorse e configurare le risorse per l'invio di log all'area di lavoro, eseguire le operazioni seguenti:To grant a user access to log data from their resources and configure their resources to send logs to the workspace, perform the following:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per l'utilizzo delle autorizzazioni per **l'area di lavoro o le risorse**

    * Concedere agli utenti le autorizzazioni `Microsoft.OperationalInsights/workspaces/read` `Microsoft.OperationalInsights/workspaces/sharedKeys/action`seguenti per l'area di lavoro: e . Con queste autorizzazioni, gli utenti non possono eseguire query a livello di area di lavoro. Possono solo enumerare l'area di lavoro e usarla come destinazione per le impostazioni di diagnostica o la configurazione dell'agente.

    * Concedere agli utenti le autorizzazioni `Microsoft.Insights/logs/*/read` `Microsoft.Insights/diagnosticSettings/write`seguenti per le risorse: e . Se sono già assegnati al ruolo [Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) di `*/read` Log Analytics, al ruolo Lettore o si sono concesse autorizzazioni per questa risorsa, è sufficiente.

3. Per concedere a un utente l'accesso ai dati di log dalle proprie risorse senza essere in grado di leggere gli eventi di sicurezza e inviare dati, eseguire le operazioni seguenti:To grant a user access to log data from their resources without being able to read security events and send data, perform the following:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per l'utilizzo delle autorizzazioni per **l'area di lavoro o le risorse**

    * Concedere agli utenti le autorizzazioni `Microsoft.Insights/logs/*/read`seguenti per le risorse: .

    * Aggiungere la seguente nonazione per impedire agli `Microsoft.Insights/logs/SecurityEvent/read`utenti di leggere il tipo SecurityEvent: . Il NonAction deve essere nello stesso ruolo personalizzato dell'azione che fornisce l'autorizzazione di lettura (`Microsoft.Insights/logs/*/read`). Se l'utente inerente all'azione di lettura da un altro ruolo assegnato a questa risorsa o alla sottoscrizione o al gruppo di risorse, sarebbe in grado di leggere tutti i tipi di log. Ciò vale anche `*/read`se ereditano , che esistono, ad esempio, con il ruolo Lettore o Collaboratore.

4. Per concedere a un utente l'accesso ai dati di log dalle risorse e leggere tutti gli accessi di Azure AD e leggere i dati del log della soluzione di gestione degli aggiornamenti dall'area di lavoro, eseguire le operazioni seguenti:To grant a user access to log data from their resources and read all Azure AD sign-in and read Update Management solution log data from the workspace, perform the following:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per l'utilizzo delle autorizzazioni per **l'area di lavoro o le risorse**

    * Concedere agli utenti le autorizzazioni seguenti per l'area di lavoro: 

        * `Microsoft.OperationalInsights/workspaces/read`: obbligatorio in modo che l'uso possa enumerare l'area di lavoro e aprire il pannello dell'area di lavoro nel portale di Azure
        * `Microsoft.OperationalInsights/workspaces/query/read`– necessario per ogni utente che può eseguire query
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– per poter leggere i log di accesso di Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– per essere in grado di leggere i log della soluzione di gestione degli aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– per essere in grado di leggere i log della soluzione di gestione degli aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– per essere in grado di leggere i registri di gestione degli aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– necessario per poter utilizzare la soluzione di gestione degli aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– necessario per poter utilizzare la soluzione di gestione degli aggiornamenti

    * Concedere agli utenti le autorizzazioni `*/read`seguenti per le risorse: , assegnato al ruolo Lettore o `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>Controllo degli accessi in base al ruolo a livello di

**Il controllo degli accessi** in base al ruolo a livello di tabella consente di definire un controllo più granulare dei dati in un'area di lavoro di Log Analytics oltre alle altre autorizzazioni. Questo controllo consente di definire tipi di dati specifici accessibili solo a un set specifico di utenti.

Il controllo di accesso alle tabelle viene implementato con [i ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md) per concedere l'accesso a [tabelle](../log-query/logs-structure.md) specifiche nell'area di lavoro. Questi ruoli vengono applicati alle aree di lavoro con [modalità](design-logs-deployment.md#access-control-mode) di controllo dell'accesso del contesto dell'area di lavoro o del contesto delle risorse, indipendentemente dalla [modalità](design-logs-deployment.md#access-mode)di accesso dell'utente.

Creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) con le azioni seguenti per definire l'accesso al controllo di accesso alle tabelle.

* Per concedere l'accesso a una tabella, includerla nella sezione **Azioni** della definizione del ruolo. Per sottrarre l'accesso dalle **azioni**consentite, includerlo nella sezione **NotActions.**
* Per specificare tutte le tabelle, utilizzare Microsoft.OperationalInsights/workspaces/query/' .

Ad esempio, per creare un ruolo con accesso alle tabelle Heartbeat e _AzureActivity,_ creare un ruolo personalizzato usando le azioni seguenti:For example, to create a role with access to the _Heartbeat_ and AzureActivity tables, create a custom role using the following actions:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Per creare un ruolo con accesso solo alla tabella _SecurityBaseline,_ creare un ruolo personalizzato utilizzando le azioni seguenti:To create a role with access to only the SecurityBaseline table, create a custom role using the following actions:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Log personalizzati

 I log personalizzati vengono creati da origini dati, ad esempio log personalizzati e API agente di raccolta dati HTTP. Il modo più semplice per identificare il tipo di log consiste nel controllare le tabelle elencate in [Registri personalizzati nello schema del log.](../log-query/get-started-portal.md#understand-the-schema)

 Non è possibile concedere l'accesso a singoli log personalizzati, ma è possibile concedere l'accesso a tutti i log personalizzati. Per creare un ruolo con accesso a tutti i log personalizzati, creare un ruolo personalizzato utilizzando le azioni seguenti:To create a role with access to all custom logs, create a custom role using the following actions:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Un approccio alternativo per gestire l'accesso ai log personalizzati consiste nell'assegnarli a una risorsa di Azure e gestire l'accesso usando il paradigma del contesto delle risorse. Per usare questo metodo, è necessario includere l'ID risorsa specificandolo nell'intestazione [x-ms-AzureResourceId](data-collector-api.md#request-headers) quando i dati vengono ingeriti in Log Analytics tramite [l'API dell'agente](data-collector-api.md)di raccolta dati HTTP. L'ID risorsa deve essere valido e applicare regole di accesso. Dopo l'inserimento, i log sono accessibili a coloro che hanno accesso in lettura alla risorsa, come illustrato di seguito.

A volte i log personalizzati provengono da origini che non sono direttamente associate a una risorsa specifica. In questo caso, creare un gruppo di risorse solo per gestire l'accesso a questi log. Il gruppo di risorse non comporta alcun costo, ma fornisce un ID risorsa valido per controllare l'accesso ai log personalizzati. Ad esempio, se un firewall specifico invia log personalizzati, creare un gruppo di risorse denominato "MyFireWallLogs" e assicurarsi che le richieste API contengano l'ID risorsa "MyFireWallLogs". I record del registro del firewall sono quindi accessibili solo agli utenti a cui è stato concesso l'accesso a MyFireWallLogs o a quelli con accesso completo all'area di lavoro.          

### <a name="considerations"></a>Considerazioni

* Se a un utente viene concessa l'autorizzazione di lettura globale con i ruoli Lettore o Collaboratore standard che includono l'azione _ \*/lettura,_ sostituirà il controllo di accesso per tabella e concederà loro l'accesso a tutti i dati di log.
* Se a un utente viene concesso l'accesso per tabella ma non altre autorizzazioni, sarà in grado di accedere ai dati di log dall'API ma non dal portale di Azure.If a user is granted per-table access but no other permissions, they would be able to access log data from the API but not from the Azure portal. Per fornire l'accesso dal portale di Azure, usare Lettore di Log Analytics come ruolo di base.
* Gli amministratori della sottoscrizione avranno accesso a tutti i tipi di dati indipendentemente dalle altre impostazioni di autorizzazione.
* I proprietari dell'area di lavoro vengono trattati come qualsiasi altro utente per il controllo degli accessi per tabella.
* È consigliabile assegnare ruoli ai gruppi di sicurezza anziché ai singoli utenti per ridurre il numero di assegnazioni. Ciò consente inoltre di utilizzare gli strumenti di gestione dei gruppi esistenti per configurare e verificare l'accesso.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Log Analytics agent overview](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per raccogliere dati dai computer nel data center o in un altro ambiente cloud.

* Vedere Raccogliere dati sulle macchine virtuali di Azure per configurare la raccolta dei dati dalle macchine virtuali di Azure.See [Collect data about Azure virtual machines](../../azure-monitor/learn/quick-collect-azurevm.md) to configure data collection from Azure VMs.
