---
title: Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: È possibile gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure usando svariate attività amministrative per utenti, account, aree di lavoro e account di Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: c6fa4df1fb2fc7559f706d81621ea198f5ca7cdc
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881420"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gestire i dati di log e le aree di lavoro in monitoraggio di Azure

Monitoraggio di Azure archivia i dati di [log](data-platform-logs.md) in un'area di lavoro log Analytics, che è essenzialmente un contenitore che include informazioni sulla configurazione e sui dati. Per gestire l'accesso ai dati di log, è necessario eseguire diverse attività amministrative correlate all'area di lavoro.

Questo articolo illustra come gestire l'accesso ai log e per amministrare le aree di lavoro che li contengono, tra cui:

* Come concedere l'accesso agli utenti che devono accedere ai dati di log da risorse specifiche usando il controllo degli accessi in base al ruolo (RBAC) di Azure.

* Come concedere l'accesso all'area di lavoro utilizzando le autorizzazioni dell'area di lavoro.

* Come concedere l'accesso agli utenti che devono accedere ai dati di log in una tabella specifica nell'area di lavoro usando il controllo degli accessi in base al ruolo di Azure

## <a name="define-access-control-mode"></a>Definire la modalità di controllo di accesso

È possibile visualizzare la modalità di controllo di accesso configurata in un'area di lavoro dal portale di Azure o con Azure PowerShell.  È possibile modificare questa impostazione usando uno dei metodi supportati seguenti:

* Portale di Azure

* Azure PowerShell

* Modello di Azure Resource Manager

### <a name="configure-from-the-azure-portal"></a>Configurare dal portale di Azure

È possibile visualizzare la modalità di controllo di accesso dell'area di lavoro corrente nella pagina **Panoramica** dell'area di lavoro nel menu **log Analytics area di lavoro** . 

![Visualizzazione della modalità di controllo di accesso dell'area di lavoro](media/manage-access/view-access-control-mode.png)

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
1. Nella portale di Azure selezionare Log Analytics aree di lavoro > l'area di lavoro.  

È possibile modificare questa impostazione dalla pagina **Proprietà** dell'area di lavoro. Se non si dispone delle autorizzazioni necessarie per configurare l'area di lavoro, la modifica dell'impostazione verrà disabilitata.

![Modificare la modalità di accesso all'area di lavoro](media/manage-access/change-access-control-mode.png)

### <a name="configure-using-powershell"></a>Configurare con PowerShell

Usare il comando seguente per esaminare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

L'output sarà simile al seguente:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Il valore `False` indica che l'area di lavoro è configurata con la modalità di accesso al contesto dell'area di lavoro.  Il valore `True` indica che l'area di lavoro è configurata con la modalità di accesso del contesto di risorsa. 

>[!NOTE]
>Se un'area di lavoro viene restituita senza un valore booleano ed è vuota, corrisponde anche ai `False` risultati di un valore.
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
```

### <a name="configure-using-a-resource-manager-template"></a>Configurare usando un modello di Gestione risorse

Per configurare la modalità di accesso in un modello di Azure Resource Manager, impostare il flag della funzionalità **enableLogAccessUsingOnlyResourcePermissions** nell'area di lavoro su uno dei valori seguenti.

* **false**: Impostare l'area di lavoro su autorizzazioni del contesto dell'area di lavoro. Si tratta dell'impostazione predefinita se il flag non è impostato.
* **true**: Impostare l'area di lavoro su autorizzazioni del contesto di risorsa.

## <a name="manage-accounts-and-users"></a>Gestire utenti e account

Le autorizzazioni applicate all'area di lavoro per un determinato utente sono definite dalla [modalità di accesso](design-logs-deployment.md#access-mode) e dalla [modalità di controllo di accesso](design-logs-deployment.md#access-control-mode) dell'area di lavoro. Con il **contesto dell'area di lavoro**è possibile visualizzare tutti i log nell'area di lavoro per cui si dispone dell'autorizzazione, poiché le query in questa modalità hanno come ambito tutti i dati di tutte le tabelle nell'area di lavoro. Con il **contesto delle risorse**è possibile visualizzare i dati dei log nell'area di lavoro per una risorsa, un gruppo di risorse o una sottoscrizione particolare quando si esegue una ricerca direttamente dalla risorsa nella portale di Azure a cui si ha accesso. Le query in questa modalità hanno come ambito solo i dati associati a tale risorsa.

### <a name="workspace-permissions"></a>Autorizzazioni per l'area di lavoro

A ogni area di lavoro possono essere associati più account, ognuno dei quali può avere accesso a diverse aree di lavoro. L'accesso viene gestito usando [l'accesso in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md).

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

Per concedere l'accesso all'area di lavoro Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

Azure offre due ruoli utente predefiniti per le aree di lavoro di Log Analytics:

* Lettore di Log Analytics
* Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:

* Visualizzare e cercare tutti i dati di monitoraggio
* Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

Il ruolo di lettore di Log Analytics include le azioni di Azure seguenti:

| Type    | Autorizzazione | DESCRIZIONE |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse di Azure e la configurazione delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere le impostazioni dell'area di lavoro ed eseguire query sui dati. Vedere le opzioni più granulari precedenti. |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Deprecato, non è necessario assegnarli agli utenti. |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Deprecato, non è necessario assegnarli agli utenti. |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti. Ciò impedisce all'utente di aggiungere nuove risorse all'area di lavoro |

I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:

* Leggere tutti i dati di monitoraggio come Lettore di Log Analytics può
* Creare e configurare account di automazione
* Aggiunta e rimozione di soluzioni di gestione

    > [!NOTE]
    > Per eseguire correttamente le ultime due azioni, questa autorizzazione deve essere concessa a livello di gruppo di risorse o di abbonamento.  

* Leggere le chiavi degli account di archiviazione
* Configurare la raccolta di log da Archiviazione di Azure  
* Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  * Aggiunta dell'estensione macchina virtuale alle VM
  * Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE]
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

Il ruolo di collaboratore di Log Analytics include le azioni di Azure seguenti:

| Autorizzazione | DESCRIZIONE |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse. <br> Per le aree di lavoro, consente autorizzazioni complete senza restrizioni per leggere l'impostazione dell'area di lavoro ed eseguire query sui dati. Vedere le opzioni più granulari precedenti. |
| `Microsoft.Automation/automationAccounts/*` | Consente di creare e configurare account di automazione di Azure, inclusa l'aggiunta e modifica di runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Consente di aggiungere, aggiornare e rimuovere estensioni macchina virtuale, inclusa l'estensione Microsoft Monitoring Agent e l'estensione agente OMS per Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Consente di visualizzare la chiave dell'account di archiviazione, è necessaria per configurare Log Analytics per la lettura dei log dagli account di archiviazione di Azure |
| `Microsoft.Insights/alertRules/*` | Consente di aggiungere, aggiornare e rimuovere regole di avviso |
| `Microsoft.Insights/diagnosticSettings/*` | Consente di aggiungere, aggiornare e rimuovere impostazioni di diagnostica dalle risorse di Azure |
| `Microsoft.OperationalInsights/*` | Consente di aggiungere, aggiornare e rimuovere configurazioni per le aree di lavoro di Log Analytics |
| `Microsoft.OperationsManagement/*` | Consente di aggiungere e rimuovere soluzioni di gestione |
| `Microsoft.Resources/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |

Per aggiungere e rimuovere utenti da un ruolo utente, è necessario avere le autorizzazioni `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Usare questi ruoli per concedere agli utenti l'accesso ad ambiti diversi:

* Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
* Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
* Risorsa: accesso alla sola area di lavoro specificata

È necessario eseguire assegnazioni a livello di risorsa (area di lavoro) per garantire un controllo di accesso accurato.  Usare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

### <a name="resource-permissions"></a>Autorizzazioni per le risorse

Quando gli utenti eseguono query sui log da un'area di lavoro usando l'accesso al contesto delle risorse, avranno le autorizzazioni seguenti per la risorsa:

| Autorizzazioni | DESCRIZIONE |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Esempi:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Possibilità di visualizzare tutti i dati di log per la risorsa.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Possibilità di configurare l'impostazione di diagnostica per consentire l'impostazione dei log per questa risorsa. |

`/read`l'autorizzazione viene in genere concessa da un ruolo _\*_ che include  _\*/Read o_ autorizzazioni quali i ruoli predefiniti [Reader](../../role-based-access-control/built-in-roles.md#reader) e [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) . Si noti che i ruoli personalizzati che includono azioni specifiche o ruoli predefiniti dedicati potrebbero non includere questa autorizzazione.

Per creare un controllo di accesso diverso per diverse tabelle, vedere [definizione del controllo degli accessi per tabella](#table-level-rbac) .

## <a name="table-level-rbac"></a>RBAC a livello di tabella

Il controllo degli accessi in base al **livello di tabella** consente di definire un controllo più granulare dei dati in un'area di lavoro log Analytics, oltre alle altre autorizzazioni. Questo controllo consente di definire tipi di dati specifici accessibili solo a un set specifico di utenti.

Implementare il controllo di accesso alle tabelle con i [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md) per concedere o negare l'accesso a [tabelle](../log-query/logs-structure.md) specifiche nell'area di lavoro. Questi ruoli vengono applicati alle aree di lavoro con [modalità di controllo](design-logs-deployment.md#access-control-mode) degli accessi del contesto di area di lavoro o delle risorse indipendentemente dalla [modalità di accesso](design-logs-deployment.md#access-mode)dell'utente.

Creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) con le azioni seguenti per definire l'accesso al controllo di accesso alla tabella.

* Per concedere l'accesso a una tabella, includerlo nella sezione **azioni** della definizione di ruolo.
* Per negare l'accesso a una tabella, includerlo nella sezione notacts della definizione di ruolo.
* Usare * per specificare tutte le tabelle.

Per creare un ruolo con accesso alle tabelle _heartbeat_ e _AzureActivity_ , ad esempio, creare un ruolo personalizzato utilizzando le azioni seguenti:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Per creare un ruolo con accesso solo a _SecurityBaseline_ e nessuna altra tabella, creare un ruolo personalizzato utilizzando le azioni seguenti:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Log personalizzati

 I log personalizzati vengono creati dalle origini dati, ad esempio i log personalizzati e l'API dell'agente di raccolta dati HTTP. Il modo più semplice per identificare il tipo di log consiste nel controllare le tabelle elencate [in log personalizzati nello schema del log](../log-query/get-started-portal.md#understand-the-schema).

 Attualmente non è possibile concedere o negare l'accesso ai singoli log personalizzati, ma è possibile concedere o negare l'accesso a tutti i log personalizzati. Per creare un ruolo con accesso a tutti i log personalizzati, creare un ruolo personalizzato utilizzando le azioni seguenti:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considerazioni

* Se a un utente viene concessa l'autorizzazione di lettura globale con i ruoli Reader o collaboratore standard che includono l'  _\*azione/Read_ , eseguirà l'override del controllo di accesso per ogni tabella e consentirà l'accesso a tutti i dati di log.
* Se a un utente viene concesso l'accesso per tabella ma non altre autorizzazioni, potranno accedere ai dati di log dall'API ma non dal portale di Azure. Per fornire l'accesso dalla portale di Azure, utilizzare Log Analytics Reader come ruolo di base.
* Gli amministratori della sottoscrizione avranno accesso a tutti i tipi di dati indipendentemente dalle altre impostazioni di autorizzazione.
* I proprietari dell'area di lavoro vengono trattati come qualsiasi altro utente per il controllo di accesso per tabella.
* Per ridurre il numero di assegnazioni, è necessario assegnare i ruoli ai gruppi di sicurezza anziché ai singoli utenti. Ciò consentirà inoltre di utilizzare gli strumenti di gestione dei gruppi esistenti per configurare e verificare l'accesso.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Log Analytics agent overview](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per raccogliere dati dai computer nel data center o in un altro ambiente cloud.

* Vedere [raccogliere dati sulle macchine virtuali di Azure](../../azure-monitor/learn/quick-collect-azurevm.md) per configurare la raccolta dati dalle macchine virtuali di Azure.