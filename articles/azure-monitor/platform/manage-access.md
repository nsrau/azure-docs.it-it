---
title: Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: È possibile gestire l'accesso ai dati archiviati in un'area di lavoro di Log Analytics in monitoraggio di Azure usando le autorizzazioni a livello di risorsa, area di lavoro o tabella. Questo articolo illustra come completare l'operazione.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 79e5b1ddde0ff5f0d09dc1c20e3b20ec4de3d925
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536677"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure

Monitoraggio di Azure archivia i dati di [log](data-platform-logs.md) in un'area di lavoro log Analytics. Un'area di lavoro è un contenitore che include informazioni sui dati e sulla configurazione. Per gestire l'accesso ai dati di log, è necessario eseguire diverse attività amministrative correlate all'area di lavoro.

Questo articolo illustra come gestire l'accesso ai log e come amministrare le aree di lavoro che li contengono, inclusa la procedura per concedere l'accesso a: 

* Area di lavoro utilizzando le autorizzazioni dell'area di lavoro.
* Utenti che devono accedere ai dati di log da risorse specifiche usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), noto anche come [contesto di risorsa](design-logs-deployment.md#access-mode)
* Utenti che devono accedere ai dati di log in una tabella specifica nell'area di lavoro usando il controllo degli accessi in base al ruolo

Per comprendere i concetti relativi ai log relativi alle strategie di accesso e RBAC di Azure, vedere [progettazione della distribuzione dei log di monitoraggio di Azure](design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>Configurare la modalità di controllo di accesso

È possibile visualizzare la [modalità di controllo di accesso](design-logs-deployment.md) configurata in un'area di lavoro dal portale di Azure o con Azure PowerShell.  È possibile modificare questa impostazione usando uno dei metodi supportati seguenti:

* Portale di Azure

* Azure PowerShell

* Modello di Azure Resource Manager

### <a name="from-the-azure-portal"></a>Dal portale di Azure

È possibile visualizzare la modalità di controllo di accesso dell'area di lavoro corrente nella pagina **Panoramica** dell'area di lavoro nel menu **log Analytics area di lavoro** .

![Visualizzazione della modalità di controllo di accesso dell'area di lavoro](media/manage-access/view-access-control-mode.png)

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
1. Nella portale di Azure selezionare Log Analytics aree di lavoro > l'area di lavoro.

È possibile modificare questa impostazione dalla pagina **Proprietà** dell'area di lavoro. Se non si dispone delle autorizzazioni necessarie per configurare l'area di lavoro, la modifica dell'impostazione verrà disabilitata.

![Modificare la modalità di accesso all'area di lavoro](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Utilizzo di PowerShell

Usare il comando seguente per esaminare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

L'output sarà simile al seguente:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

`False`Il valore indica che l'area di lavoro è configurata con la modalità di accesso al contesto dell'area di lavoro.  `True`Il valore indica che l'area di lavoro è configurata con la modalità di accesso del contesto di risorsa.

> [!NOTE]
> Se un'area di lavoro viene restituita senza un valore booleano ed è vuota, corrisponde anche ai risultati di un `False` valore.
>

Usare lo script seguente per impostare la modalità di controllo di accesso per un'area di lavoro specifica sull'autorizzazione del contesto di risorsa:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Usare lo script seguente per impostare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione per l'autorizzazione del contesto della risorsa:

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

Per configurare la modalità di accesso in un modello di Azure Resource Manager, impostare il flag della funzionalità **enableLogAccessUsingOnlyResourcePermissions** nell'area di lavoro su uno dei valori seguenti.

* **false**: impostare l'area di lavoro sulle autorizzazioni per il contesto dell'area di lavoro. Si tratta dell'impostazione predefinita se il flag non è impostato.
* **true**: impostare l'area di lavoro sulle autorizzazioni del contesto di risorsa.

## <a name="manage-access-using-workspace-permissions"></a>Gestire l'accesso con le autorizzazioni dell'area di lavoro

A ogni area di lavoro possono essere associati più account, ognuno dei quali può avere accesso a diverse aree di lavoro. L'accesso viene gestito tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/role-assignments-portal.md).

Le attività seguenti richiedono anche le autorizzazioni di Azure:

|Azione |Autorizzazioni di Azure necessarie |Note |
|-------|-------------------------|------|
| Aggiunta e rimozione di soluzioni di monitoraggio | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Queste autorizzazioni devono essere concesse a livello di gruppo di risorse o di sottoscrizione. |
| Modifica del piano tariffario | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visualizzazione dei dati nei riquadri delle soluzioni *Backup* e *Site Recovery* | Amministratore/Coamministratore | Risorse di accesso distribuite usando il modello di distribuzione classica |
| Creare un'area di lavoro nel portale di Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Visualizzare le proprietà di base dell'area di lavoro e immettere il pannello dell'area di lavoro nel portale | `Microsoft.OperationalInsights/workspaces/read` ||
| Eseguire query sui log con qualsiasi interfaccia | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Accedere a tutti i tipi di log usando le query | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Accedere a una tabella di log specifica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leggere le chiavi dell'area di lavoro per consentire l'invio dei log a questa area di lavoro | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gestire l'accesso con le autorizzazioni di Azure

Per concedere l'accesso all'area di lavoro Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md). Per esempi di ruoli personalizzati, vedere [ruoli personalizzati di esempio](#custom-role-examples)

Azure offre due ruoli utente predefiniti per le aree di lavoro di Log Analytics:

* Lettore di Log Analytics
* Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:

* Visualizzare e cercare tutti i dati di monitoraggio
* Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

Il ruolo di lettore di Log Analytics include le azioni di Azure seguenti:

| Type    | Autorizzazione | Descrizione |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse di Azure e la configurazione delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere le impostazioni dell'area di lavoro ed eseguire query sui dati. Vedere le opzioni più granulari precedenti. |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Deprecato, non è necessario assegnarli agli utenti. |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Deprecato, non è necessario assegnarli agli utenti. |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti. Ciò impedisce all'utente di aggiungere nuove risorse all'area di lavoro |

I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:

* Include tutti i privilegi del *ruolo lettore log Analytics*, consentendo all'utente di leggere tutti i dati di monitoraggio
* Creare e configurare gli account di automazione
* Consente di aggiungere e rimuovere soluzioni di gestione

    > [!NOTE]
    > Per eseguire correttamente le ultime due azioni, questa autorizzazione deve essere concessa a livello di gruppo di risorse o di abbonamento.

* Leggere le chiavi dell'account di archiviazione
* Configurare la raccolta di log da archiviazione di Azure
* Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  * Aggiunta dell'estensione macchina virtuale alle VM
  * Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE]
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

Il ruolo di collaboratore di Log Analytics include le azioni di Azure seguenti:

| Autorizzazione | Descrizione |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere l'impostazione dell'area di lavoro ed eseguire query sui dati. Vedere le opzioni più granulari precedenti. |
| `Microsoft.Automation/automationAccounts/*` | Consente di creare e configurare account di automazione di Azure, inclusa l'aggiunta e modifica di runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Consente di aggiungere, aggiornare e rimuovere estensioni macchina virtuale, inclusa l'estensione Microsoft Monitoring Agent e l'estensione agente OMS per Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Consente di visualizzare la chiave dell'account di archiviazione, è necessaria per configurare Log Analytics per la lettura dei log dagli account di archiviazione di Azure |
| `Microsoft.Insights/alertRules/*` | Consente di aggiungere, aggiornare e rimuovere regole di avviso |
| `Microsoft.Insights/diagnosticSettings/*` | Consente di aggiungere, aggiornare e rimuovere impostazioni di diagnostica dalle risorse di Azure |
| `Microsoft.OperationalInsights/*` | Aggiungere, aggiornare e rimuovere la configurazione per le aree di lavoro Log Analytics. Per modificare le impostazioni avanzate dell'area di lavoro, è necessario `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Consente di aggiungere e rimuovere soluzioni di gestione |
| `Microsoft.Resources/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |

Per aggiungere e rimuovere utenti da un ruolo utente, è necessario avere le autorizzazioni `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Usare questi ruoli per concedere agli utenti l'accesso ad ambiti diversi:

* Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
* Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
* Risorsa: accesso alla sola area di lavoro specificata

È consigliabile eseguire assegnazioni a livello di risorsa (area di lavoro) per garantire un controllo di accesso accurato. Usare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

### <a name="resource-permissions"></a>Autorizzazioni per le risorse

Quando gli utenti eseguono query sui log da un'area di lavoro usando l'accesso al contesto delle risorse, avranno le autorizzazioni seguenti per la risorsa:

| Autorizzazione | Descrizione |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Esempi:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Possibilità di visualizzare tutti i dati di log per la risorsa.  |
| `Microsoft.Insights/diagnosticSettings/write` | Possibilità di configurare l'impostazione di diagnostica per consentire l'impostazione dei log per questa risorsa. |

`/read`l'autorizzazione viene in genere concessa da un ruolo che include _\* /Read o_ _\*_ autorizzazioni quali i ruoli predefiniti [Reader](../../role-based-access-control/built-in-roles.md#reader) e [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) . I ruoli personalizzati che includono azioni specifiche o ruoli predefiniti dedicati potrebbero non includere questa autorizzazione.

Per creare un controllo di accesso diverso per diverse tabelle, vedere [definizione del controllo degli accessi per tabella](#table-level-azure-rbac) .

## <a name="custom-role-examples"></a>Esempi di ruoli personalizzati

1. Per concedere a un utente l'accesso ai dati di log dalle risorse, eseguire le operazioni seguenti:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per **utilizzare le autorizzazioni delle risorse**

    * Concedere agli utenti `*/read` o `Microsoft.Insights/logs/*/read` alle autorizzazioni le proprie risorse. Se è già stato assegnato il ruolo di [lettore log Analytics](../../role-based-access-control/built-in-roles.md#reader) nell'area di lavoro, è sufficiente.

2. Per concedere a un utente l'accesso ai dati di log dalle risorse e configurare le risorse per inviare i log all'area di lavoro, eseguire le operazioni seguenti:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per **utilizzare le autorizzazioni delle risorse**

    * Concedere agli utenti le seguenti autorizzazioni per l'area di lavoro: `Microsoft.OperationalInsights/workspaces/read` e `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Con queste autorizzazioni, gli utenti non possono eseguire query a livello di area di lavoro. Possono solo enumerare l'area di lavoro e usarla come destinazione per le impostazioni di diagnostica o per la configurazione dell'agente.

    * Concedere agli utenti le seguenti autorizzazioni per le risorse: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write` . Se è già stato assegnato il ruolo di [collaboratore log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , è sufficiente assegnare il ruolo lettore o concedere le `*/read` autorizzazioni per questa risorsa.

3. Per concedere a un utente l'accesso ai dati di log dalle risorse senza la possibilità di leggere gli eventi di sicurezza e inviare dati, eseguire le operazioni seguenti:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per **utilizzare le autorizzazioni delle risorse**

    * Concedere agli utenti le seguenti autorizzazioni per le risorse: `Microsoft.Insights/logs/*/read` .

    * Aggiungere la seguente non azione per impedire agli utenti di leggere il tipo di SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read` . La non azione deve trovarsi nello stesso ruolo personalizzato dell'azione che fornisce l'autorizzazione di lettura ( `Microsoft.Insights/logs/*/read` ). Se l'utente inerente l'azione di lettura da un altro ruolo assegnato a questa risorsa o alla sottoscrizione o al gruppo di risorse, sarà in grado di leggere tutti i tipi di log. Questo vale anche se ereditano `*/read` , che esistono, ad esempio, con il ruolo lettore o collaboratore.

4. Per concedere a un utente l'accesso ai dati di log dalle risorse e leggere tutti i Azure AD accesso e leggere Gestione aggiornamenti i dati di log della soluzione dall'area di lavoro, seguire questa procedura:

    * Configurare la modalità di controllo di accesso dell'area di lavoro per **utilizzare le autorizzazioni delle risorse**

    * Concedere agli utenti le seguenti autorizzazioni per l'area di lavoro: 

        * `Microsoft.OperationalInsights/workspaces/read` : obbligatorio in modo che l'utente possa enumerare l'area di lavoro e aprire il pannello dell'area di lavoro nella portale di Azure
        * `Microsoft.OperationalInsights/workspaces/query/read` : obbligatorio per ogni utente che può eseguire query
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` -per poter leggere i log di accesso Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` -per poter leggere i log della soluzione Gestione aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` -per poter leggere i log della soluzione Gestione aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` -per poter leggere i log di gestione aggiornamenti
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` : necessario per poter usare Gestione aggiornamenti soluzione
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` : necessario per poter usare Gestione aggiornamenti soluzione

    * Concedere agli utenti le seguenti autorizzazioni per le risorse: `*/read` , assegnate al ruolo Reader o `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>Controllo RBAC di Azure a livello di tabella

Il controllo degli accessi in base al **livello di tabella** consente di definire un controllo più granulare dei dati in un'area di lavoro log Analytics, oltre alle altre autorizzazioni. Questo controllo consente di definire tipi di dati specifici accessibili solo a un set specifico di utenti.

Implementare il controllo di accesso alle tabelle con i [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md) per concedere l'accesso a [tabelle](./data-platform-logs.md) specifiche nell'area di lavoro. Questi ruoli vengono applicati alle aree di lavoro con [modalità di controllo degli accessi](design-logs-deployment.md#access-control-mode) del contesto di area di lavoro o delle risorse indipendentemente dalla [modalità di accesso](design-logs-deployment.md#access-mode)dell'utente.

Creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) con le azioni seguenti per definire l'accesso al controllo di accesso alla tabella.

* Per concedere l'accesso a una tabella, includerlo nella sezione **azioni** della definizione di ruolo. Per sottrarre l'accesso dalle **azioni** consentite, includerlo nella sezione **notacts** .
* Usare Microsoft. OperationalInsights/Workspaces/query/* per specificare tutte le tabelle.

Per creare un ruolo con accesso alle tabelle _heartbeat_ e _AzureActivity_ , ad esempio, creare un ruolo personalizzato utilizzando le azioni seguenti:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Per creare un ruolo con accesso solo alla tabella _SecurityBaseline_ , creare un ruolo personalizzato utilizzando le azioni seguenti:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
Gli esempi precedenti definiscono un elenco di tabelle consentite. Questo esempio mostra la definizione di elenco bloccati quando un utente può accedere a tutte le tabelle, ma la tabella _SecurityAlert_ :

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Log personalizzati

 I log personalizzati vengono creati dalle origini dati, ad esempio i log personalizzati e l'API dell'agente di raccolta dati HTTP. Il modo più semplice per identificare il tipo di log consiste nel controllare le tabelle elencate [in log personalizzati nello schema del log](../log-query/get-started-portal.md#understand-the-schema).

 Non è possibile concedere l'accesso ai singoli log personalizzati, ma è possibile concedere l'accesso a tutti i log personalizzati. Per creare un ruolo con accesso a tutti i log personalizzati, creare un ruolo personalizzato utilizzando le azioni seguenti:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Un approccio alternativo per gestire l'accesso ai log personalizzati consiste nell'assegnarli a una risorsa di Azure e gestire l'accesso usando il paradigma del contesto di risorsa. Per usare questo metodo, è necessario includere l'ID risorsa specificandone l'ID nell'intestazione [x-ms-AzureResourceId](data-collector-api.md#request-headers) quando i dati vengono inseriti per log Analytics tramite l' [API dell'agente di raccolta dati http](data-collector-api.md). L'ID risorsa deve essere valido e applicare regole di accesso. Dopo l'inserimento, i log sono accessibili a quelli con accesso in lettura alla risorsa, come illustrato qui.

Talvolta i log personalizzati provengono da origini non direttamente associate a una risorsa specifica. In questo caso, creare un gruppo di risorse solo per gestire l'accesso a questi log. Il gruppo di risorse non comporta alcun costo, ma fornisce un ID di risorsa valido per controllare l'accesso ai log personalizzati. Ad esempio, se un firewall specifico Invia log personalizzati, creare un gruppo di risorse denominato "MyFireWallLogs" e assicurarsi che le richieste API contengano l'ID risorsa "MyFireWallLogs". I record del log del firewall sono quindi accessibili solo agli utenti a cui è stato concesso l'accesso a MyFireWallLogs o a quelli con accesso completo all'area di lavoro.          

### <a name="considerations"></a>Considerazioni

* Se a un utente viene concessa l'autorizzazione di lettura globale con i ruoli Reader o collaboratore standard che includono l'azione _\* /Read_ , eseguirà l'override del controllo di accesso per ogni tabella e consentirà l'accesso a tutti i dati di log.
* Se a un utente viene concesso l'accesso per tabella ma non altre autorizzazioni, potranno accedere ai dati di log dall'API ma non dal portale di Azure. Per fornire l'accesso dalla portale di Azure, utilizzare Log Analytics Reader come ruolo di base.
* Gli amministratori e i proprietari della sottoscrizione avranno accesso a tutti i tipi di dati indipendentemente dalle altre impostazioni di autorizzazione.
* I proprietari dell'area di lavoro vengono trattati come qualsiasi altro utente per il controllo di accesso per tabella.
* È consigliabile assegnare i ruoli ai gruppi di sicurezza anziché ai singoli utenti per ridurre il numero di assegnazioni. Ciò consentirà inoltre di utilizzare gli strumenti di gestione dei gruppi esistenti per configurare e verificare l'accesso.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Log Analytics agent overview](./log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per raccogliere dati dai computer nel data center o in un altro ambiente cloud.

* Vedere [raccogliere dati sulle macchine virtuali di Azure](../learn/quick-collect-azurevm.md) per configurare la raccolta dati dalle macchine virtuali di Azure.