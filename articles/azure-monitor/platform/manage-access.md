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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 27db27d79a05f24461e63242c0395cfd81315432
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276412"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gestire i dati di log e le aree di lavoro in Monitoraggio di Azure
Monitoraggio di Azure archivia i dati di log in un'area di lavoro Log Analytics, che in pratica è un contenitore che include dati e informazioni di configurazione. Per gestire l'accesso ai dati di log, vengono eseguite diverse attività amministrative relative alle aree di lavoro. Nell'organizzazione è possibile usare più aree di lavoro per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

Questo articolo illustra come gestire l'accesso ai log e per amministrare le aree di lavoro che li contengono. 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
Per creare un'area di lavoro di Log Analitica, è necessario:

1. Disporre di una sottoscrizione di Azure
2. Scegliere un nome per l'area di lavoro
3. Associare l'area di lavoro con uno degli abbonamenti e dei gruppi di risorse.
4. Scegliere una località geografica

Vedere gli articoli seguenti per informazioni dettagliate sulla creazione di un'area di lavoro:

- [Creare un'area di lavoro di Log Analitica nel portale di Azure](../learn/quick-create-workspace.md)
- [Creare un'area di lavoro di Log Analitica con Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Creare un'area di lavoro di Log Analitica con Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinare il numero di aree di lavoro necessarie
Un'area di lavoro Log Analytics è una risorsa di Azure e rappresenta un contenitore in cui i dati vengono raccolti, analizzati e presentati in Monitoraggio di Azure. È possibile disporre di più aree di lavoro per ogni sottoscrizione di Azure e avere accesso a più di un'area di lavoro, con la possibilità di eseguire facilmente query su di esse. Questa sezione descrive quando può essere utile creare più aree di lavoro.

Un'area di lavoro Log Analytics offre:

* Una posizione geografica per archiviare i dati.
* Isolamento dei dati per definire i diritti di accesso utente diverso nella modalità incentrata su area di lavoro. Non è rilevante quando si lavora in modalità incentrata su risorse.
* Ambito per la configurazione delle impostazioni, ad esempio [piano tariffario](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [conservazione](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e [dati limitando](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Gli addebiti relativi alla conservazione e l'inserimento di dati vengono eseguiti sulla risorsa dell'area di lavoro.

Dal punto di vista del consumo, è consigliabile creare meno aree di lavoro possibile. In questo modo le operazioni di amministrazione e di esecuzione di query sono più semplici e più rapide. Tuttavia, in base alle caratteristiche precedenti, si possono creare più aree di lavoro se:

* Si opera in un'azienda globale ed è necessario che i dati di log siano archiviati in aree specifiche per motivi di sovranità o conformità dei dati.
* Si usa Azure e si intendono evitare costi di trasferimento dei dati in uscita tramite un'area di lavoro nella stessa area delle risorse di Azure da essa gestite.
* Si opera come provider di servizi gestiti e per ogni cliente gestito è necessario mantenere i dati di Log Analytics isolati da altri dati del cliente.
* Si gestiscono più clienti e si desidera che ogni cliente o reparto o gruppo aziendale visualizzi i propri dati, ma non i dati da altri utenti, e non vi è alcuna necessità di un cliente tra consolidato / reparto o gruppo aziendale visualizzazione. ".

Quando si usano agenti Windows per la raccolta dei dati, è possibile [configurare ogni agente in modo che faccia riferimento a una o più aree di lavoro](../../azure-monitor/platform/agent-windows.md).

Se si usa System Center Operations Manager, ogni gruppo di gestione di Operations Manager può essere connesso con una sola area di lavoro. È possibile installare Microsoft Monitoring Agent nei computer gestiti da Operations Manager e fare sì che l’agente faccia riferimento sia a Operations Manager che a un'altra area di lavoro Log Analytics.

Una volta definito l'architettura dell'area di lavoro, è necessario applicare questi criteri sulle risorse di Azure con [criteri di Azure](../../governance/policy/overview.md). Questo può fornire una definizione predefinita che verrà applicata automaticamente a tutte le risorse di Azure. Ad esempio, è possibile impostare un criterio per assicurarsi che tutte le risorse di Azure in una determinata area inviati tutti i log di diagnostica a un'area di lavoro specifico.

## <a name="view-workspace-details"></a>Visualizzare i dettagli dell'area di lavoro
Durante l'analisi dei dati nell'area di lavoro di Log Analitica dal **monitoraggio di Azure** menu nel portale di Azure, creare e gestire aree di lavoro di **le aree di lavoro di Log Analitica** menu.
 

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.  

    ![Portale di Azure](media/manage-access/azure-portal-01.png)  

3. Selezionare l'area di lavoro dall'elenco.

4. La pagina dell'area di lavoro vengono visualizzati i dettagli su area di lavoro, introduzione, configurazione e i collegamenti per altre informazioni.  

    ![Dettagli dell'area di lavoro](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Ambito e le autorizzazioni dell'area di lavoro
I dati che un utente può accedere a dipendono da diversi fattori che sono elencati nella tabella seguente. Ognuno è descritto nelle sezioni seguenti.

| Fattore | DESCRIZIONE |
|:---|:---|
| [Modalità di accesso](#access-modes) | Metodo utilizzato per l'utente accede l'area di lavoro.  Definisce l'ambito dei dati disponibili e la modalità di controllo di accesso che viene applicata. |
| [Modalità di controllo di accesso](#access-control-mode) | L'impostazione nell'area di lavoro che definisce se le autorizzazioni vengono applicate a livello di area di lavoro o una risorsa. |
| [Autorizzazioni](#manage-accounts-and-users) | Autorizzazioni applicate al file singoli o gruppi di utenti per l'area di lavoro o la risorsa. Definisce i dati che l'utente avrà accesso a. |
| [RBAC a livello di tabella](#table-level-rbac) | Autorizzazioni granulari facoltative che si applica a tutti gli utenti indipendentemente dai loro modalità di accesso o la modalità di controllo di accesso. Definisce i tipi di dati accessibili all'utente. |



## <a name="access-modes"></a>Modalità di accesso
Il _accessmode_ fa riferimento al modo in cui un utente accede a un'area di lavoro di Log Analitica e definisce l'ambito dei dati può accedere. 

**Workspace-centric**: In questa modalità, un utente può visualizzare tutti i log nell'area di lavoro che dispongono delle autorizzazioni in. Le query in questa modalità sono limitate a tutti i dati in tutte le tabelle nell'area di lavoro. Si tratta della modalità di accesso utilizzata quando i log sono accessibili con l'area di lavoro come ambito, ad esempio quando si seleziona **registri** dalle **monitoraggio di Azure** menu nel portale di Azure.

**Risorsa incentrato**: Accesso all'area di lavoro per una determinata risorsa, ad esempio quando si seleziona **registri** dal menu di risorse nel portale di Azure, è possibile visualizzare i log per la risorsa solo in tutte le tabelle che è possibile utilizzare. Le query in questa modalità sono limitate solo i dati associati a tale risorsa. Questa modalità consente inoltre il controllo di accesso granulari in base al ruolo (RBAC). 

> [!NOTE]
> I log sono disponibili per le query sugli elementi risorsa solo se si trovassero sia associati correttamente la risorsa in questione. Attualmente, le risorse seguenti presentano limitazioni: 
> - Computer all'esterno di Azure
> - Service Fabric
> - Application Insights
> - Contenitori
>
> È possibile verificare se i log associate correttamente alle relative risorse eseguendo una query e analizzare i record di interesse. Se l'ID di risorsa corretta è la [_ResourceId](log-standard-properties.md#_resourceid) proprietà, i dati è disponibile per le query sugli elementi risorsa.

### <a name="comparing-access-modes"></a>Confronto tra le modalità di accesso

Nella tabella seguente vengono riepilogate le modalità di accesso:

| | Area di lavoro incentrato | Incentrato su risorse |
|:---|:---|:---|
| Per chi è destinato ogni modello? | Amministrazione centrale. Amministratori che devono configurare utenti e la raccolta dei dati che devono accedere a un'ampia gamma di risorse. Attualmente necessari anche per gli utenti autorizzati di accedere ai log per le risorse all'esterno di Azure. | Team dell'applicazione. Amministratori delle risorse di Azure da monitorare. |
| Che cos'è richiesto un utente per visualizzare i log? | Autorizzazioni per l'area di lavoro. Visualizzare **le autorizzazioni dell'area di lavoro** nelle [gestire utenti e account](#manage-accounts-and-users). | Accesso in lettura alla risorsa. Visualizzare **autorizzazioni per le risorse** nelle [gestire utenti e account](#manage-accounts-and-users). Le autorizzazioni possono essere ereditate (ad esempio il gruppo di risorse che lo contiene) o assegnato direttamente alla risorsa. L'autorizzazione per i log per la risorsa verrà assegnato automaticamente. |
| Che cos'è l'ambito delle autorizzazioni? | Area di lavoro. Eseguire una query tutti i log nell'area di lavoro dalle tabelle di cui dispongono delle autorizzazioni per gli utenti con accesso all'area di lavoro. Vedere [controllo di accesso di tabella](#table-level-rbac) | Risorse di Azure. Utente possa eseguire query sui log per le risorse che ha accesso da qualsiasi area di lavoro ma non è possibile eseguire query sui log per le altre risorse. |
| Come è possibile accedere ai log utente? | Avviare **registri** dalla **monitoraggio di Azure** dal menu o **aree di lavoro di Log Analitica**. | Avviare **registri** dal menu di scelta per la risorsa di Azure. |


## <a name="access-control-mode"></a>Modalità di controllo di accesso
Il _modalità di controllo di accesso_ è un'impostazione su ogni aree di lavoro che definisce come vengono determinate le autorizzazioni per tale area di lavoro.

**Richiede le autorizzazioni dell'area di lavoro**:  Questa modalità di controllo non consente accessi granulare. Per un utente possa accedere l'area di lavoro, essi devono essere concesse autorizzazioni per l'area di lavoro o a tabelle specifiche. 

Se un utente accede l'area di lavoro in modalità area di lavoro incentrato, avranno accesso a tutti i dati di tabelle che è stato concesso l'accesso a. Se un utente accede a area di lavoro in modalità incentrata su risorse, avranno accesso ai dati solo per tale risorsa in tutte le tabelle che è stato concesso l'accesso a.

Si tratta dell'impostazione predefinita per tutte le aree di lavoro create prima di marzo 2019.

**Usare le autorizzazioni dell'area di lavoro o risorse**: Questa modalità di controllo consente accessi granulare. Gli utenti hanno accesso solo i dati associati alle risorse possono visualizzare tramite le autorizzazioni di Azure, le risorse per cui dispongono `read` l'autorizzazione. 

Quando un utente accede a area di lavoro in modalità incentrata su area di lavoro, verranno applicate le autorizzazioni dell'area di lavoro. Quando un utente accede a area di lavoro in modalità incentrata su risorse, verranno verificate solo le autorizzazioni delle risorse e verranno ignorate le autorizzazioni dell'area di lavoro. Abilitare RBAC per un utente di rimuoverle dalle autorizzazioni dell'area di lavoro e consentendo loro le autorizzazioni delle risorse essere riconosciuta.

Si tratta dell'impostazione predefinita per tutte le aree di lavoro creati dopo il mese di marzo 2019.

> [!NOTE]
> Se un utente ha solo autorizzazioni per le risorse nell'area di lavoro, solo saranno in grado di accedere usando l'area di lavoro [modalità incentrata su risorse](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definire la modalità controllo di accesso nel portale di Azure
È possibile visualizzare la modalità di controllo di accesso dell'area di lavoro corrente nel **Overview** pagina per l'area di lavoro il **dell'area di lavoro di Log Analitica** menu.

![Modalità di controllo di visualizzazione dell'area di lavoro accesso](media/manage-access/view-access-control-mode.png)

È possibile modificare questa impostazione nella **proprietà** pagina dell'area di lavoro. Modificare l'impostazione verrà disabilitata se non si dispone delle autorizzazioni per configurare l'area di lavoro.

![Modifica della modalità di accesso dell'area di lavoro](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definire la modalità controllo di accesso in PowerShell

Per esaminare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione, usare il comando seguente:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Usare lo script seguente per impostare la modalità di controllo di accesso per un'area di lavoro specifico:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Usare lo script seguente per impostare la modalità di controllo di accesso per tutte le aree di lavoro nella sottoscrizione

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definire la modalità di accesso nel modello di Resource Manager
Per configurare la modalità di accesso in un modello di Azure Resource Manager, impostare il **enableLogAccessUsingOnlyResourcePermissions** flag nell'area di lavoro a uno dei seguenti valori di funzionalità.

- **false**: Impostare l'area di lavoro per le autorizzazioni sugli elementi dell'area di lavoro. Questo è l'impostazione predefinita se non è impostato il flag.
- **true**: Impostare l'area di lavoro per le autorizzazioni sugli elementi risorsa.


## <a name="manage-accounts-and-users"></a>Gestire utenti e account
Le autorizzazioni per l'area di lavoro che vengono applicate a un determinato utente sono definite da loro modalità di accesso e il [modalità di controllo di accesso](#access-control-mode) dell'area di lavoro. **Le autorizzazioni dell'area di lavoro** vengono applicate quando un utente accede a qualsiasi area di lavoro usando **dell'area di lavoro incentrato** nelle [modalità area di lavoro incentrato](#access-modes). **Autorizzazioni per le risorse** vengono applicati quando un utente accede a un'area di lavoro con **utilizza le autorizzazioni di risorsa o dell'area di lavoro** [modalità di controllo di accesso](#access-control-mode) usando [modalità incentrata su risorse ](#access-modes).

### <a name="workspace-permissions"></a>Autorizzazioni dell'area di lavoro
A ogni area di lavoro possono essere associati più account, ognuno dei quali può avere accesso a diverse aree di lavoro. L'accesso viene gestito tramite [accesso basato sui ruoli Azure](../../role-based-access-control/role-assignments-portal.md). 


Le attività seguenti richiedono anche le autorizzazioni di Azure:

| Azione                                                          | Autorizzazioni di Azure necessarie | Note |
|-----------------------------------------------------------------|--------------------------|-------|
| Aggiunta e rimozione di soluzioni di monitoraggio                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Queste autorizzazioni devono essere concesse a livello di gruppo di risorse o di sottoscrizione. |
| Modifica del piano tariffario                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visualizzazione dei dati nei riquadri delle soluzioni *Backup* e *Site Recovery* | Amministratore/Coamministratore | Risorse di accesso distribuite usando il modello di distribuzione classica |
| Creare un'area di lavoro nel portale di Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gestire l'accesso all'area di lavoro di Analitica Log usando le autorizzazioni di Azure 
Per concedere l'accesso all'area di lavoro Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

Azure offre due ruoli utente predefiniti per le aree di lavoro di Log Analytics:
- Lettore di Log Analytics
- Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:
- Visualizzare e cercare tutti i dati di monitoraggio 
- Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

Il ruolo di lettore di Log Analytics include le azioni di Azure seguenti:

| Type    | Autorizzazione | DESCRIZIONE |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse di Azure e la configurazione delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Consente di eseguire query di ricerca log versione 2 |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Consente di eseguire query di ricerca log versione 1 |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti. Ciò impedisce all'utente di aggiungere nuove risorse all'area di lavoro |


I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:
- Leggere tutti i dati di monitoraggio come Lettore di Log Analytics può  
- Creare e configurare account di automazione  
- Aggiunta e rimozione di soluzioni di gestione    
    > [!NOTE] 
    > Per eseguire correttamente le ultime due azioni, questa autorizzazione deve essere concessa a livello di gruppo di risorse o di abbonamento.  

- Leggere le chiavi degli account di archiviazione   
- Configurare la raccolta di log da Archiviazione di Azure  
- Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  - Aggiunta dell'estensione macchina virtuale alle VM
  - Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE] 
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

Il ruolo di collaboratore di Log Analytics include le azioni di Azure seguenti:

| Autorizzazione | DESCRIZIONE |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
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
- Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
- Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
- Risorsa: accesso alla sola area di lavoro specificata

È consigliabile eseguire assegnazioni a livello di risorse (area di lavoro) per garantire il controllo di accesso accurate.  Usare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

### <a name="resource-permissions"></a>Autorizzazioni delle risorse 
Quando si connette da aree di lavoro tramite l'accesso incentrato su risorse con query degli utenti, hanno le autorizzazioni seguenti per la risorsa:

| Autorizzazione | DESCRIZIONE |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Esempi:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Possibilità di visualizzare tutti i dati di log per la risorsa.  |


Questa autorizzazione viene in genere concesso da un ruolo che includa  _\*/lettura oppure_ _\*_ autorizzazioni, ad esempio l'elemento predefinito [lettore](../../role-based-access-control/built-in-roles.md#reader) e[ Collaboratore](../../role-based-access-control/built-in-roles.md#contributor) ruoli. Si noti che i ruoli personalizzati che includono azioni specifiche o i ruoli predefiniti dedicati potrebbero non includere l'autorizzazione.

Visualizzare [definizione di controllo di accesso per ogni tabella](#table-level-rbac) seguito se si desidera creare il controllo di accesso diversi per diverse tabelle.


## <a name="table-level-rbac"></a>RBAC a livello di tabella
**Tabella a livello di RBAC** consente di fornire un controllo più granulare ai dati in un'area di lavoro di Log Analitica oltre alle altre autorizzazioni. Questo controllo consente di definire tipi di dati specifici che sono accessibili solo a un set specifico di utenti.

Si implementa il controllo degli accessi nella tabella con [Azure ruoli personalizzati](../../role-based-access-control/custom-roles.md) di concedere o negare l'accesso a specifici [tabelle](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) nell'area di lavoro. Questi ruoli vengono applicati alle aree di lavoro con area di lavoro incentrato o risorsa incentrato [modalità di controllo di accesso ai](#access-control-mode) indipendentemente dal fatto l'utente [accessmode](#access-modes).

Creare un [ruolo personalizzato](../../role-based-access-control/custom-roles.md) con le azioni seguenti per definire l'accesso al controllo degli accessi di tabella.

- Per concedere l'accesso a una tabella, includerlo nella **azioni** sezione della definizione del ruolo.
- Per negare l'accesso a una tabella, includerlo nella **NotActions** sezione della definizione del ruolo.
- Usare * per specificare tutte le tabelle.

Ad esempio, per creare un ruolo con accesso per il _Heartbeat_ e _AzureActivity_ tabelle, creare un ruolo personalizzato usando le azioni seguenti:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Per creare un ruolo con accesso solo _SecurityBaseline_ e altre tabelle, creare un ruolo personalizzato tramite le azioni seguenti:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Log personalizzati
 I log personalizzati vengono creati dalle origini dati, ad esempio i log personalizzati e API di raccolta dati HTTP. Il modo più semplice per identificare il tipo di log sia controllando le tabelle elencate in [dello schema del log di log personalizzati](../log-query/get-started-portal.md#understand-the-schema).

 È attualmente non è possibile concedere o negare l'accesso ai singoli log personalizzati, ma è possibile concedere o negare l'accesso a tutti i log personalizzati. Per creare un ruolo con accesso a tutti i log personalizzati, creare un ruolo personalizzato tramite le azioni seguenti:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considerazioni

- Se un utente viene concessa globale leggere autorizzazioni ai ruoli collaboratore o lettore standard che includono il  _\*/lettura_ azione, esso sostituirà il controllo di accesso per ogni tabella e assegnare loro l'accesso a tutti i dati di log.
- Se un utente viene concesso l'accesso per ogni tabella ma non altre autorizzazioni, quanto lo sarebbero in grado di accedere ai dati di log dall'API, ma non dal portale di Azure. Per fornire l'accesso al portale di Azure, usare agente di lettura Log Analitica come il ruolo di base.
- Gli amministratori per la sottoscrizione avranno accesso a tutti i tipi di dati indipendentemente dalle eventuali altre impostazioni di autorizzazione.
- I proprietari dell'area di lavoro vengono considerati come qualsiasi altro utente per il controllo di accesso per ogni tabella.
- È consigliabile assegnare ruoli ai gruppi di sicurezza anziché a singoli utenti per ridurre il numero di assegnazioni. Ciò risulta utile anche usare strumenti di gestione di gruppo esistenti per configurare e verificare l'accesso.




## <a name="next-steps"></a>Passaggi successivi
* Vedere [Log Analytics agent overview](../../azure-monitor/platform/log-analytics-agent.md) (Panoramica dell'agente di Log Analytics) per raccogliere dati dai computer nel data center o in un altro ambiente cloud.
* Vedere [Raccogliere dati sulle macchine virtuali di Azure](../../azure-monitor/learn/quick-collect-azurevm.md) per configurare la raccolta di dati dalle macchine virtuali di Azure.  

