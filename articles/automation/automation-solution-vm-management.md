---
title: Avviare/arrestare le macchine virtuali durante la soluzione fuori orarioStart/stop VMs during off-hours solution
description: Questa soluzione di gestione delle macchine virtuali avvia e arresta le macchine virtuali di Azure in base a una pianificazione e monitora in modo proattivo dai log di Monitoraggio di Azure.This VM management solution starts and stops your Azure virtual machines on a schedule and proactively monitors from Azure Monitor logs.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 968e609772e08814a9943734d30c16bf6f5972e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604718"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Avviare/arrestare le macchine virtuali durante la soluzione fuori orario in Automazione di AzureStart/stop VMs during off-hours solution in Azure Automation

Avvia/arresta macchine virtuali durante l'avvio o l'arresto delle macchine virtuali di Azure.The **Start/stop VMs during off-hours solution** starts or stops your Azure virtual machines. Avvia o arresta i computer in base a pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite i log di Monitoraggio di Azure e invia messaggi di posta elettronica facoltativi usando i [gruppi di azioni.](../azure-monitor/platform/action-groups.md) La soluzione supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. 

Questa soluzione fornisce un'opzione di automazione decentralizzata a basso costo per gli utenti che vogliono ottimizzare i costi delle macchine virtuali. Con questa soluzione, è possibile:

- [Pianificare l'avvio e l'arresto delle macchine virtuali.](automation-solution-vm-management-config.md#schedule)
- Pianificare l'avvio e l'arresto delle macchine virtuali in ordine crescente [usando i tag](automation-solution-vm-management-config.md#tags) di Azure (non supportati per le macchine virtuali classiche).
- Arresto automatico delle macchine virtuali in base [all'utilizzo ridotto della CPU.](automation-solution-vm-management-config.md#cpuutil)

> [!NOTE]
> La soluzione **Avvia/arresta macchine virtuali durante le ore** non orariè è stata aggiornata per supportare le versioni più recenti dei moduli di Azure disponibili.

Di seguito sono riportate le limitazioni relative alla soluzione corrente:

- Gestisce le macchine virtuali in qualsiasi area, ma può essere usato solo nella stessa sottoscrizione dell'account di Automazione di Azure.It manages VMs in any region, but can only be used in the same subscription as your Azure Automation account.
- È disponibile in Azure e Azure per enti pubblici in qualsiasi area che supporti un'area di lavoro di Log Analytics, un account di Automazione di Azure e avvisi. Le aree di Azure per enti pubblici attualmente non supportano la funzionalità di posta elettronica.

## <a name="solution-prerequisites"></a>Prerequisiti della soluzione

I runbook per questa soluzione funzionano con un [account RunAs di Azure](automation-create-runas-account.md). L'account RunAs è il metodo di autenticazione preferito perché utilizza l'autenticazione del certificato anziché una password che potrebbe scadere o modificare frequentemente.

È consigliabile usare un account di automazione separato per la soluzione **Start/stop VMs durante le ore non orari.** Le versioni dei moduli di Azure vengono aggiornate di frequente e i relativi parametri potrebbero cambiare. La soluzione non viene aggiornata alla stessa cadenza e potrebbe non funzionare con le versioni più recenti dei cmdlet utilizzati. Ti consigliamo di testare gli aggiornamenti dei moduli in un account di automazione di test prima di importarli negli account di Automazione di produzione.

## <a name="solution-permissions"></a>Autorizzazioni soluzione

È necessario disporre di determinate autorizzazioni per distribuire le macchine virtuali di **avvio/arresto durante** la soluzione fuori orario. Le autorizzazioni sono diverse se la soluzione utilizza un account di automazione creato in precedena e un'area di lavoro di Log Analytics dalle autorizzazioni necessarie se la soluzione crea un nuovo account e un nuovo'area di lavoro durante la distribuzione. 

Non è necessario configurare le autorizzazioni se si è un collaboratore della sottoscrizione e un amministratore globale nel tenant di Azure Active Directory.You don't need to configure permissions if you're a Contributor on the subscription and a Global Administrator in your Azure Active Directory tenant. Se non si dispone di questi diritti o è necessario configurare un ruolo personalizzato, assicurarsi di disporre delle autorizzazioni descritte di seguito.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Autorizzazioni per l'account di automazione preesistente e l'area di lavoro di Log Analytics

Per distribuire le macchine virtuali di **avvio/arresto durante** la soluzione fuori orario in un account di automazione esistente e un'area di lavoro di Log Analytics, l'utente che distribuisce la soluzione richiede le autorizzazioni seguenti per l'ambito del gruppo di risorse. Per altre informazioni sui ruoli, vedere Ruoli personalizzati per le risorse di Azure.To learn more about roles, see [Custom roles for Azure resources.](../role-based-access-control/custom-roles.md)

| Autorizzazione | Scope|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/variables/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/schedules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/runbooks/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/connections/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/modules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/modules/read | Gruppo di risorse |
| Microsoft.automation/automationAccounts/jobSchedules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/read | Gruppo di risorse |
| Microsoft.OperationsManagement/solutions/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/. | Gruppo di risorse |
| Microsoft.Insights/diagnosticSettings/write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/Write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/lettura | Gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Gruppo di risorse |
| Microsoft.Resources/deployments/* | Gruppo di risorse |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Autorizzazioni per il nuovo account di automazione e la nuova area di lavoro di Log Analytics

È possibile distribuire le macchine virtuali di avvio/arresto durante la soluzione fuori orario in un nuovo account di automazione e nell'area di lavoro di Log Analytics.You can deploy the **Start/stop VMs during off-hours** solution to a new Automation account and Log Analytics workspace. In questo caso, l'utente che distribuisce la soluzione necessita delle autorizzazioni definite nella sezione precedente e delle autorizzazioni definite in questa sezione. 

L'utente che distribuisce la soluzione ha bisogno dei ruoli seguenti:The user deploying the solution needs the following roles:

- Coamministratore della sottoscrizione. Questo ruolo è necessario per creare l'account RunAs classico se si intende gestire le macchine virtuali classiche. [Gli account RunAs classici](automation-create-standalone-account.md#create-a-classic-run-as-account) non vengono più creati per impostazione predefinita.
- Appartenenza al ruolo Sviluppatore applicazione [Azure Active Directory.Membership](../active-directory/users-groups-roles/directory-assign-admin-roles.md) in the Azure Active Directory Application Developer role. Per ulteriori informazioni sulla configurazione degli account [RunAs, vedere Autorizzazioni per configurare gli account RunAs](manage-runas-account.md#permissions).
- Collaboratore per la sottoscrizione o le autorizzazioni seguenti.

| Autorizzazione |Scope|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/write | Gruppo di risorse |

## <a name="solution-components"></a>Componenti della soluzione

Le macchine virtuali di **avvio/arresto durante le ore non lavorative** includono runbook preconfigurati, pianificazioni e integrazione con i log di Monitoraggio di Azure.The Start/stop VMs during off-hours solution includes preconfigured runbooks, schedules, and integration with Azure Monitor logs. È possibile usare questi elementi per personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali.

### <a name="runbooks"></a>Runbook

Nella tabella seguente sono elencati i runbook che la soluzione distribuisce nell'account di automazione. NON apportare modifiche al codice del runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente alcun runbook con **figlio** aggiunto al nome.

Tutti i runbook `WhatIf` padre includono il parametro. Se impostato su True, il parametro supporta i dettagli del comportamento esatto del runbook quando viene eseguito senza il parametro e verifica che le macchine virtuali corrette siano destinate. Un runbook esegue le azioni `WhatIf` definite solo quando il parametro è impostato su False.A runbook performs its defined actions only when the parameter is set to False.

|Runbook | Parametri | Descrizione|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato dal runbook padre. Questo runbook crea avvisi per ogni risorsa per lo scenario di arresto automatico.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True o False  | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> `VMList`è un elenco separato da virgole di macchine virtuali. Ad esempio: `vm1, vm2, vm3`.<br> `WhatIf`consente la convalida della logica del runbook senza eseguire.|
|AutoStop_Disable | nessuno | Disabilita gli avvisi di arresto automatico e la pianificazione predefinita.|
|AutoStop_VM_Child | WebHookData | Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare una macchina virtuale classica.|
|AutoStop_VM_Child_ARM | WebHookData |Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare una macchina virtuale.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Azione: avvio o arresto<br> VMList  | Esegue l'avvio o l'arresto dell'azione nel gruppo di macchine virtuali classico da parte di Servizi cloud. |
|ScheduledStartStop_Child | VMName <br> Azione: avvio o arresto <br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato.|
|ScheduledStartStop_Child_Classic | VMName<br> Azione: avvio o arresto<br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o di arresto per l'arresto pianificato per le macchine virtuali classiche. |
|ScheduledStartStop_Parent | Azione: avvio o arresto <br>VMList <br> WhatIf: True o False | Avvia o arresta tutte le macchine virtuali nella sottoscrizione. Modificare le `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variabili ed eseguire solo su questi gruppi di risorse di destinazione. È anche possibile escludere macchine `External_ExcludeVMNames` virtuali specifiche aggiornando la variabile.|
|SequencedStartStop_Parent | Azione: avvio o arresto <br> WhatIf: True o False<br>VMList| Crea tag denominati **sequencestart** e **sequencestop** in ogni macchina virtuale per cui si vuole sequenziare l'attività di avvio/arresto. I nomi dei tag distinguono tra maiuscole e minuscole. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine in cui si vuole eseguire l'avvio o l'arresto. <br>**Nota:** le macchine virtuali devono `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`essere `External_ExcludeVMNames` all'interno di gruppi di risorse definiti in , e variabili. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di Automazione. Modifica solo le `External`variabili precedute da . La modifica di `Internal` variabili precedute da prefisso causa effetti indesiderati.

> [!NOTE]
> Le limitazioni relative al nome della macchina virtuale e al gruppo di risorse sono in gran parte il risultato della dimensione della variabile.

|Variabile | Descrizione|
|---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori `GreaterThan` `GreaterThanOrEqual`accettabili `LessThan`sono `LessThanOrEqual`, , , e .|
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|
|External_AutoStop_Frequency | Frequenza di valutazione per la regola. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore. |
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.|
|External_AutoStop_Severity | Gravità dell'avviso metrico, che può variare da 0 a 4. |
|External_AutoStop_Threshold | Soglia per la regola di avviso `External_AutoStop_MetricName`di Azure specificata nella variabile . I valori percentuali sono compresi tra 1 e 100.|
|External_AutoStop_TimeAggregationOperator | Operatore di aggregazione temporale applicato alla dimensione della finestra selezionata per valutare la condizione. I valori `Average` `Minimum`accettabili `Maximum` `Total`sono `Last`, , , , e .|
|External_AutoStop_TimeWindow | Dimensioni della finestra durante la quale Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|
|External_EnableClassicVMs| Valore che specifica se le macchine virtuali classiche sono destinate alla soluzione. Il valore predefinito è True. Impostare questa variabile su False per le sottoscrizioni di Azure Cloud Solution Provider (CSP). Le macchine virtuali classiche richiedono un [account RunAs classico.](automation-create-standalone-account.md#create-a-classic-run-as-account)|
|External_ExcludeVMNames | Elenco delimitato da virgole di nomi di macchine virtuali da escludere, limitato a 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali all'elenco, le macchine virtuali che sono impostate per l'esclusione potrebbero essere avviate o arrestate inavvertitamente.|
|External_Start_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse di destinazione per le azioni di avvio.|
|External_Stop_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse di destinazione per le azioni di arresto.|
|External_WaitTimeForVMRetrySeconds |Tempo di attesa in secondi per l'esecuzione delle azioni nelle macchine virtuali per il runbook **SequencedStartStop_Parent.** Questa variabile consente al runbook di attendere le operazioni figlio per un numero specificato di secondi prima di procedere con l'azione successiva. Il tempo di attesa massimo è 10800 o tre ore. Il valore predefinito è 2100 secondi.|
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|
|Internal_AutoSnooze_ARM_WebhookURI | L'URI webhook chiamato per lo scenario AutoStop per le macchine virtuali.|
|Internal_AutoSnooze_WebhookUri | L'URI webhook chiamato per lo scenario AutoStop per le macchine virtuali classiche.|
|Internal_AzureSubscriptionId | ID sottoscrizione di Azure.|
|Internal_ResourceGroupName | Nome del gruppo di risorse Account di automazione.|

>[!NOTE]
>Per la `External_WaitTimeForVMRetryInSeconds`variabile , il valore predefinito è stato aggiornato da 600 a 2100. 

In tutti gli `External_Start_ResourceGroupNames`scenari, le variabili `External_Stop_ResourceGroupNames`, e `External_ExcludeVMNames` sono necessarie per la destinazione delle macchine virtuali, ad eccezione degli elenchi di macchine virtuali separate da virgole per i AutoStop_CreateAlert_Parent, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** runbook. **AutoStop_CreateAlert_Parent** In altre parte, le macchine virtuali devono appartenere a gruppi di risorse di destinazione affinché si verifichino azioni di avvio e arresto. La logica funziona in modo simile ai criteri di Azure perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e lasciare che le azioni vengano ereditate dalle nuove macchine virtuali create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.È possibile modificarle o creare le proprie pianificazioni personalizzate.Per impostazione predefinita, tutte le pianificazioni sono disabilitate, ad eccezione delle **pianificazioni Scheduled_StartVM** e **Scheduled_StopVM.**

Non abilitare tutte le pianificazioni, perché questa operazione potrebbe creare azioni di pianificazione sovrapposte. È consigliabile determinare le ottimizzazioni che si desidera eseguire e modificarle di conseguenza. Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.

|Nome pianificazione | Frequenza | Descrizione|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook **AutoStop_CreateAlert_Parent** ogni 8 ore, che `External_Start_ResourceGroupNames`a `External_Stop_ResourceGroupNames`sua `External_ExcludeVMNames` volta arresta i valori basati su macchine virtuali in , e variabili. In alternativa, è possibile specificare un elenco separato `VMList` da virgole di macchine virtuali usando il parametro .|
|Scheduled_StopVM | Definito dall'utente, ogni giorno | Esegue il runbook **ScheduledStopStart_Parent** `Stop` con un parametro di ogni giorno all'ora specificata.Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dagli asset variabili.Abilitare la pianificazione correlata **Scheduled-StartVM**.|
|Scheduled_StartVM | Definito dall'utente, ogni giorno | Esegue il runbook **ScheduledStopStart_Parent** con `Start` un valore di parametro di ogni giorno all'ora specificata. Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dagli asset variabili.Abilitare la pianificazione correlata **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue il runbook Sequenced_Parent con `Stop` un valore di parametro di ogni venerdì all'ora specificata.Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag **SequenceStop** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere la sezione Runbook.Abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue il runbook **SequencedStopStart_Parent** con `Start` un valore di parametro di ogni lunedì all'ora specificata. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate. Per ulteriori informazioni sui valori dei tag e sulle risorse variabili, consultate [Runbooks.](#runbooks) Abilitare la pianificazione correlata, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Uso della soluzione con le macchine virtuali classicheUse of the solution with classic VMs

Se si usa la soluzione **Start/stop VMs durante le** macchine virtuali non orariper le macchine virtuali classiche, l'automazione elabora tutte le macchine virtuali in sequenza per ogni servizio cloud. Le macchine virtuali vengono comunque elaborate in parallelo tra diversi servizi cloud. 

Per usare la soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, consultate [Creare un account RunAs classico.](automation-create-standalone-account.md#create-a-classic-run-as-account)

Se si dispone di più di 20 macchine virtuali per servizio cloud, ecco alcuni consigli:If you have more than 20 VMs per cloud service, here are some recommendations:

* Creare più pianificazioni con il runbook padre **ScheduledStartStop_Parent** e specificare 20 macchine virtuali per ogni pianificazione. 
* Nelle proprietà della pianificazione `VMList` usare il parametro per specificare i nomi delle macchine virtuali come elenco separato da virgole. 

In caso contrario, se il processo di automazione per questa soluzione viene eseguito più di tre ore, viene temporaneamente scaricato o interrotto in base al limite di [condivisione equa.](automation-runbook-execution.md#fair-share)

Le sottoscrizioni di Azure CSP supportano solo il modello di Azure Resource Manager.Azure CSP subscriptions support only the Azure Resource Manager model. I servizi non di Azure Resource Manager non sono disponibili nel programma. Quando le macchine virtuali di **avvio/arresto durante** l'esecuzione della soluzione fuori orario, è possibile che vengano visualizzati errori poiché sono disponibili cmdlet per gestire le risorse classiche. Per altre informazioni su CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se si usa una sottoscrizione CSP, è necessario impostare la variabile [External_EnableClassicVMs](#variables) su False dopo la distribuzione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Abilitare la soluzione

Per iniziare a usare la soluzione, seguire i passaggi descritti in [Abilitare la soluzione Start/stop VMs](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Visualizzare la soluzione

Utilizzare uno dei meccanismi seguenti per accedere alla soluzione dopo averla abilitata:

* Nell'account di automazione selezionare **Avvia/Arresta macchina virtuale** in **Risorse correlate**. Nella pagina Avvia/Interrompi macchina virtuale selezionare **Gestisci soluzione** sul lato destro della pagina, in **Gestisci soluzioni start/stop VM**.

* Passare all'area di lavoro di Log Analytics collegata all'account di automazione. Dopo aver selezionato l'area di lavoro, scegliere **Soluzioni** nel riquadro sinistro. Nella pagina Soluzioni selezionare la soluzione **Start-Stop-VM [area di lavoro]** dall'elenco.  

Selezionando la soluzione, viene visualizzata la pagina della soluzione **Start-Stop-VM[workspace]** (Avvio-Arresto-VM[Area di lavoro]), Qui è possibile esaminare i dettagli importanti, ad esempio le informazioni nel riquadro **StartStopVM.Here** you can review important details, such as the information in the StartStopVM tile. Come nell'area di lavoro Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

È possibile eseguire un'ulteriore analisi dei record di processo facendo clic sul riquadro ciambella. Il dashboard della soluzione mostra la cronologia dei processi e le query di ricerca log predefinite. Passare al portale avanzato di analisi dei log per eseguire la ricerca in base alle query di ricerca.

## <a name="update-the-solution"></a>Aggiornare la soluzione

Se è stata distribuita una versione precedente di questa soluzione, eliminarla dall'account prima di distribuire una versione aggiornata. Seguire i passaggi per [rimuovere la soluzione](#remove-the-solution) e quindi seguire i passaggi per distribuire la [soluzione](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Rimuovere la soluzione

Se non è più necessario utilizzare la soluzione, è possibile eliminarla dall'account di automazione. Eliminando la soluzione, vengono rimossi solo i runbook. Non vengono eliminate le pianificazioni o le variabili create quando la soluzione è stata aggiunta. Rimuovi queste risorse manualmente se non le usi con altri runbook.

Per eliminare la soluzione:

1. Dall'account Automazione selezionare **Area di lavoro collegata** in **Risorse correlate**.

2. Selezionare **Vai all'area di lavoro**.

3. Fare clic su **Soluzioni** in **Generale**. 

4. Nella pagina Soluzioni selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]). 

5. Nella pagina **VMManagementSolution[Workspace]** selezionare **Delete** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Nella finestra **Elimina soluzione** confermare che si desidera eliminare la soluzione.

7. Mentre le informazioni vengono verificate e la soluzione viene eliminata, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, scelto dal menu. Si torna alla pagina Soluzioni dopo l'avvio del processo di rimozione della soluzione.

L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole mantenere l'area di lavoro log Analytics, è necessario eliminarla manualmente dal portale di Azure:If you don't want to keep the Log Analytics workspace, you must manually delete it from the Azure portal:

1. Cercare e selezionare **Aree di lavoro**di Log Analytics .

2. Nella pagina Area di lavoro di Log Analytics selezionare l'area di lavoro.

3. Scegliere **Elimina** dal menu nella pagina delle impostazioni dell'area di lavoro.

4. Se non si vuole mantenere i [componenti](#solution-components)della soluzione dell'account di Automazione di Azure, è possibile eliminarli manualmente.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare](automation-solution-vm-management-enable.md) la soluzione Start/stop VMs durante le ore non orari per le macchine virtuali di Azure.Enable the **Start/stop VMs during off-hours** solution for your Azure VMs.
