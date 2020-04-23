---
title: Soluzione di avvio/arresto di macchine virtuali durante gli orari di indisponibilità
description: Questa soluzione di gestione delle VM avvia e arresta le macchine virtuali di Azure in base a una pianificazione e monitora in modo proattivo i log di monitoraggio di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 0331678b50d2448013556ab0694d0ca87045c3a3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096917"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Avviare/arrestare VM durante gli orari di indisponibilità in automazione di Azure

La soluzione **Avvia/arresta macchine virtuali durante gli orari di** indisponibilità avvia o interrompe le macchine virtuali di Azure. Avvia o arresta le macchine virtuali nelle pianificazioni definite dall'utente, fornisce informazioni dettagliate sui log di monitoraggio di Azure e invia messaggi di posta elettronica facoltativi tramite i [gruppi di azioni](../azure-monitor/platform/action-groups.md). Per la maggior parte degli scenari, la soluzione supporta macchine virtuali sia Azure Resource Manager che classiche. 

Questa soluzione fornisce un'opzione di automazione decentralizzata a basso costo per gli utenti che vogliono ottimizzare i costi delle macchine virtuali. Con questa soluzione, è possibile:

- [Pianificare l'avvio e l'arresto delle macchine virtuali](automation-solution-vm-management-config.md#schedule).
- Pianificare le macchine virtuali per l'avvio e l'arresto in ordine crescente [usando i tag di Azure](automation-solution-vm-management-config.md#tags) (non supportati per le macchine virtuali classiche).
- Arrestare le macchine virtuali in base a un [utilizzo ridotto della CPU](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> La soluzione **avvio/arresto di macchine virtuali durante gli orari** di indisponibilità è stata aggiornata per supportare le versioni più recenti dei moduli di Azure disponibili.

Di seguito sono riportate le limitazioni della soluzione corrente:

- Gestisce le macchine virtuali in qualsiasi area, ma può essere usato solo nella stessa sottoscrizione dell'account di automazione di Azure.
- È disponibile in Azure e in Azure per enti pubblici in qualsiasi area che supporta un'area di lavoro Log Analytics, un account di automazione di Azure e gli avvisi. Le aree di Azure per enti pubblici attualmente non supportano la funzionalità di posta elettronica.

## <a name="solution-prerequisites"></a>Prerequisiti della soluzione

I runbook per questa soluzione funzionano con un [account RunAs di Azure](automation-create-runas-account.md). L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password che potrebbe scadere o modificare di frequente.

Si consiglia di usare un account di automazione separato per la soluzione **avvio/arresto di macchine virtuali durante gli orari di** indisponibilità. Le versioni dei moduli di Azure vengono aggiornate di frequente ed è possibile che i relativi parametri cambino. La soluzione non viene aggiornata alla stessa cadenza e potrebbe non funzionare con le versioni più recenti dei cmdlet usati. È consigliabile testare gli aggiornamenti dei moduli in un account di automazione di test prima di importarli negli account di automazione di produzione.

## <a name="solution-permissions"></a>Autorizzazioni della soluzione

È necessario disporre di determinate autorizzazioni per la distribuzione della soluzione **Avvia/arresta macchine virtuali durante gli orari** di indisponibilità. Le autorizzazioni sono diverse se la soluzione USA un account di automazione creato in precedenza e Log Analytics area di lavoro dalle autorizzazioni necessarie se la soluzione crea un nuovo account e un'area di lavoro durante la distribuzione. 

Non è necessario configurare le autorizzazioni se si è un collaboratore per la sottoscrizione e un amministratore globale nel tenant del Azure Active Directory. Se non si dispone di questi diritti o se è necessario configurare un ruolo personalizzato, assicurarsi di disporre delle autorizzazioni descritte di seguito.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Autorizzazioni per l'account di automazione preesistente e l'area di lavoro Log Analytics

Per distribuire la soluzione **avvio/arresto di macchine virtuali durante gli orari** di indisponibilità in un account di automazione esistente e in log Analytics area di lavoro, l'utente che distribuisce la soluzione richiede le autorizzazioni seguenti per l'ambito del gruppo di risorse. Per altre informazioni sui ruoli, vedere [ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md).

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
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/read | Gruppo di risorse |
| Microsoft.OperationsManagement/solutions/write | Gruppo di risorse |
| Microsoft. OperationalInsights/Workspaces/* | Gruppo di risorse |
| Microsoft. Insights/diagnosticSettings/scrittura | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/Write | Gruppo di risorse |
| Microsoft. Insights/ActionGroups/lettura | Gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Gruppo di risorse |
| Microsoft.Resources/deployments/* | Gruppo di risorse |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Autorizzazioni per il nuovo account di automazione e la nuova area di lavoro Log Analytics

È possibile distribuire la soluzione **avvio/arresto di macchine virtuali durante gli orari** di indisponibilità in un nuovo account di automazione e in log Analytics area di lavoro. In questo caso, l'utente che distribuisce la soluzione deve avere le autorizzazioni definite nella sezione precedente, nonché le autorizzazioni definite in questa sezione. 

Per l'utente che distribuisce la soluzione sono necessari i ruoli seguenti:

- Coamministratore per la sottoscrizione. Questo ruolo è necessario per creare l'account RunAs classico se si intende gestire le VM classiche. Per impostazione predefinita, gli [account RunAs classici](automation-create-standalone-account.md#create-a-classic-run-as-account) non vengono più creati.
- Appartenenza al ruolo di sviluppatore dell'applicazione [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Per ulteriori informazioni sulla configurazione di account RunAs, vedere [autorizzazioni per la configurazione di account RunAs](manage-runas-account.md#permissions).
- Collaboratore per la sottoscrizione o per le autorizzazioni seguenti.

| Autorizzazione |Scope|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/write | Gruppo di risorse |

## <a name="solution-components"></a>Componenti della soluzione

La **soluzione avvio/arresto di macchine virtuali durante gli orari** di indisponibilità include manuali operativi preconfigurati, pianificazioni e integrazione con i log di monitoraggio di Azure. È possibile usare questi elementi per personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali.

### <a name="runbooks"></a>Runbook

La tabella seguente elenca i manuali operativi distribuiti dalla soluzione nell'account di automazione. Non apportare modifiche al codice Runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente alcun runbook con **child** aggiunto al nome.

Tutti manuali operativi padre includono il `WhatIf` parametro. Quando è impostato su true, il parametro supporta il dettaglio del comportamento esatto che Runbook accetta quando viene eseguito senza il parametro e verifica che le VM corrette siano destinate. Un Runbook esegue le azioni definite solo quando il `WhatIf` parametro è impostato su false.

|Runbook | Parametri | Descrizione|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato dal runbook padre. Questo runbook crea avvisi in base alle singole risorse per lo scenario di arresto automatico.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True o False  | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> `VMList`è un elenco delimitato da virgole di macchine virtuali. Ad esempio: `vm1, vm2, vm3`.<br> `WhatIf`Abilita la convalida della logica Runbook senza eseguire.|
|AutoStop_Disable | nessuno | Disabilita gli avvisi di arresto automatico e la pianificazione predefinita.|
|AutoStop_VM_Child | WebHookData | Chiamato dal runbook padre. Le regole di avviso chiamano questo Runbook per arrestare una macchina virtuale classica.|
|AutoStop_VM_Child_ARM | WebHookData |Chiamato dal runbook padre. Le regole di avviso chiamano questo Runbook per arrestare una macchina virtuale.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Azione: avvio o arresto<br> VMList  | Esegue l'avvio o l'arresto dell'azione nel gruppo di VM classico per servizi cloud. |
|ScheduledStartStop_Child | VMName <br> Azione: avvio o arresto <br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato.|
|ScheduledStartStop_Child_Classic | VMName<br> Azione: avvio o arresto<br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato per le macchine virtuali classiche. |
|ScheduledStartStop_Parent | Azione: avvio o arresto <br>VMList <br> WhatIf: True o False | Avvia o arresta tutte le macchine virtuali nella sottoscrizione. Modificare le variabili `External_Start_ResourceGroupNames` ed `External_Stop_ResourceGroupNames` eseguire solo in questi gruppi di risorse di destinazione. È anche possibile escludere macchine virtuali specifiche aggiornando la `External_ExcludeVMNames` variabile.|
|SequencedStartStop_Parent | Azione: avvio o arresto <br> WhatIf: True o False<br>VMList| Crea tag denominati **sequencestart** e **sequencestop** in ogni macchina virtuale per cui si vuole sequenziare l'attività di avvio/arresto. I nomi dei tag distinguono tra maiuscole e minuscole. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine in cui si vuole eseguire l'avvio o l'arresto. <br>**Nota**: le macchine virtuali devono trovarsi all'interno `External_Start_ResourceGroupNames`di `External_Stop_ResourceGroupNames`gruppi di `External_ExcludeVMNames` risorse definiti nelle variabili, e. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di Automazione. Modificare solo le variabili con `External`prefisso. La modifica delle variabili con `Internal` prefisso causa effetti indesiderati.

> [!NOTE]
> Le limitazioni per il nome della macchina virtuale e il gruppo di risorse sono in gran parte un risultato di dimensioni variabili. Vedere [Asset variabili in automazione di Azure](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variabile | Descrizione|
|---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori accettabili `GreaterThanOrEqual`sono `LessThan` `GreaterThan`,, `LessThanOrEqual`e.|
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|
|External_AutoStop_Frequency | Frequenza di valutazione per la regola. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore. |
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.|
|External_AutoStop_Severity | Gravità dell'avviso della metrica, che può variare da 0 a 4. |
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile `External_AutoStop_MetricName`. I valori percentuali sono compresi tra 1 e 100.|
|External_AutoStop_TimeAggregationOperator | Operatore di aggregazione temporale applicato alle dimensioni della finestra selezionata per valutare la condizione. I `Average`valori accettabili `Minimum`sono `Maximum`, `Total`,, `Last`e.|
|External_AutoStop_TimeWindow | Dimensioni della finestra durante le quali Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|
|External_EnableClassicVMs| Valore che specifica se le VM classiche sono destinate alla soluzione. Il valore predefinito è True. Impostare questa variabile su false per le sottoscrizioni Azure Cloud Solution Provider (CSP). Le macchine virtuali classiche richiedono un [account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Elenco delimitato da virgole di nomi di VM da escludere, limitato a 140 VM. Se si aggiungono più di 140 VM all'elenco, le macchine virtuali che sono impostate per essere escluse potrebbero essere inavvertitamente avviate o arrestate.|
|External_Start_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse destinati alle azioni di avvio.|
|External_Stop_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse destinati alle azioni di arresto.|
|External_WaitTimeForVMRetrySeconds |Il tempo di attesa in secondi per l'esecuzione delle azioni sulle macchine virtuali per l' **SequencedStartStop_Parent** Runbook. Questa variabile consente al Runbook di attendere le operazioni figlio per un numero specificato di secondi prima di procedere con l'azione successiva. Il tempo di attesa massimo è 10800 o tre ore. Il valore predefinito è 2100 secondi.|
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|
|Internal_AutoSnooze_ARM_WebhookURI | URI del webhook chiamato per lo scenario autostop per le macchine virtuali.|
|Internal_AutoSnooze_WebhookUri | L'URI del webhook chiamato per lo scenario autostop per le macchine virtuali classiche.|
|Internal_AzureSubscriptionId | ID sottoscrizione di Azure.|
|Internal_ResourceGroupName | Nome del gruppo di risorse dell'account di automazione.|

>[!NOTE]
>Per la variabile `External_WaitTimeForVMRetryInSeconds`, il valore predefinito è stato aggiornato da 600 a 2100. 

In tutti gli scenari, le `External_Start_ResourceGroupNames`variabili `External_Stop_ResourceGroupNames`, e `External_ExcludeVMNames` sono necessarie per la destinazione delle macchine virtuali, ad eccezione degli elenchi di VM delimitati da virgole per il **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** manuali operativi. In altre parole, è necessario che le macchine virtuali appartengano ai gruppi di risorse di destinazione per le azioni di avvio e arresto. La logica funziona in modo simile ai criteri di Azure perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e lasciare che le azioni vengano ereditate dalle nuove macchine virtuali create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.È possibile modificarle o creare le proprie pianificazioni personalizzate.Per impostazione predefinita, tutte le pianificazioni sono disabilitate ad eccezione delle pianificazioni **Scheduled_StartVM** e **Scheduled_StopVM** .

Non abilitare tutte le pianificazioni, perché questa operazione potrebbe creare azioni di pianificazione sovrapposte. È preferibile determinare quali ottimizzazioni si desidera eseguire e modificare di conseguenza. Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.

|Nome pianificazione | Frequenza | Descrizione|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue l' **AutoStop_CreateAlert_Parent** Runbook ogni 8 ore, che a sua volta arresta i valori basati su VM `External_Start_ResourceGroupNames`nelle `External_Stop_ResourceGroupNames`variabili, `External_ExcludeVMNames` e. In alternativa, è possibile specificare un elenco delimitato da virgole delle macchine virtuali `VMList` usando il parametro.|
|Scheduled_StopVM | Definito dall'utente, giornaliero | Esegue l' **ScheduledStopStart_Parent** runbook con un parametro di `Stop` ogni giorno all'ora specificata.Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle risorse variabili.Abilitare la pianificazione correlata scheduled **-StartVM**.|
|Scheduled_StartVM | Definito dall'utente, giornaliero | Esegue l' **ScheduledStopStart_Parent** runbook con un valore di parametro `Start` di ogni giorno all'ora specificata. Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle risorse variabili.Abilitare la pianificazione correlata scheduled **-StopVM**.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue l' **Sequenced_StopStop_Parent** runbook con un valore di parametro `Stop` di ogni venerdì all'ora specificata.Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag **SequenceStop** definito dalle variabili appropriate. Per ulteriori informazioni sui valori di tag e sulle variabili di asset, vedere [manuali operativi](#runbooks).Abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue l' **SequencedStopStart_Parent** runbook con un valore di parametro `Start` di ogni lunedì all'ora specificata. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate. Per ulteriori informazioni sui valori dei tag e sugli Asset variabili, vedere [manuali operativi](#runbooks). Abilitare la pianificazione correlata, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Uso della soluzione con le VM classiche

Se si usa la soluzione **avvio/arresto di macchine virtuali durante gli orari** di indisponibilità per le macchine virtuali classiche, l'automazione elabora in sequenza tutte le VM per ogni servizio cloud. Le macchine virtuali vengono ancora elaborate in parallelo tra diversi servizi cloud. 

Per usare la soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere [creare un account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se sono presenti più di 20 macchine virtuali per ogni servizio cloud, di seguito sono riportati alcuni suggerimenti:

* Creare più pianificazioni con la **ScheduledStartStop_Parent** padre Runbook e specificando 20 VM per ogni pianificazione. 
* Nelle proprietà della pianificazione, usare il `VMList` parametro per specificare i nomi delle macchine virtuali come elenco delimitato da virgole. 

In caso contrario, se il processo di automazione per questa soluzione viene eseguito più di tre ore, viene temporaneamente scaricato o arrestato in base al limite di [condivisione equa](automation-runbook-execution.md#fair-share) .

Le sottoscrizioni di Azure CSP supportano solo il modello di Azure Resource Manager. I servizi non Azure Resource Manager non sono disponibili nel programma. Quando viene eseguita la **soluzione avvio/arresto di macchine virtuali durante gli orari** di indisponibilità, è possibile che si verifichino errori poiché sono presenti cmdlet per la gestione delle risorse classiche. Per altre informazioni su CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se si usa una sottoscrizione CSP, è necessario impostare la variabile [External_EnableClassicVMs](#variables) su false dopo la distribuzione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Abilitare la soluzione

Per iniziare a usare la soluzione, seguire la procedura descritta in [abilitare la soluzione di avvio/arresto di macchine virtuali](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Visualizzare la soluzione

Usare uno dei meccanismi seguenti per accedere alla soluzione dopo averla abilitata:

* Dall'account di automazione selezionare **Avvia/Arresta macchina virtuale** in **risorse correlate**. Nella pagina Avvia/Arresta macchina virtuale selezionare **Gestisci la soluzione** dal lato destro della pagina in **Gestisci soluzioni VM di avvio/arresto**.

* Passare all'area di lavoro Log Analytics collegata all'account di automazione. Dopo aver selezionato l'area di lavoro, scegliere **soluzioni** dal riquadro sinistro. Nella pagina soluzioni selezionare la soluzione **Start-Stop-VM [workspace]** nell'elenco.  

Selezionando la soluzione, viene visualizzata la pagina della soluzione **Start-Stop-VM[workspace]** (Avvio-Arresto-VM[Area di lavoro]), Qui è possibile esaminare i dettagli importanti, come le informazioni nel riquadro **StartStopVM** . Come nell'area di lavoro Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

È possibile eseguire un'ulteriore analisi dei record del processo facendo clic sul riquadro ad anello. Il dashboard della soluzione Mostra la cronologia processo e le query di ricerca log predefinite. Passare al portale avanzato di log Analytics per eseguire la ricerca in base alle query di ricerca.

## <a name="update-the-solution"></a>Aggiornare la soluzione

Se è stata distribuita una versione precedente di questa soluzione, eliminarla dall'account prima di distribuire una versione aggiornata. Seguire i passaggi per [rimuovere la soluzione](#remove-the-solution) e quindi seguire i passaggi per la [distribuzione della soluzione](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Rimuovere la soluzione

Se non è più necessario usare la soluzione, è possibile eliminarla dall'account di automazione. Eliminando la soluzione, vengono rimossi solo i runbook. Non vengono eliminate le pianificazioni o le variabili create quando la soluzione è stata aggiunta. Rimuovere questi asset manualmente se non vengono usati con altri manuali operativi.

Per eliminare la soluzione:

1. Dall'account di automazione selezionare **area di lavoro collegata** in **risorse correlate**.

2. Selezionare **Vai all'area di lavoro**.

3. Fare clic su **soluzioni** in **generale**. 

4. Nella pagina Soluzioni selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]). 

5. Nella pagina **VMManagementSolution [workspace]** selezionare **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Nella finestra **Elimina soluzione** confermare che si desidera eliminare la soluzione.

7. Mentre le informazioni vengono verificate e la soluzione viene eliminata, è possibile tenere traccia dello stato di avanzamento in **notifiche**, scelto dal menu. Dopo il processo di rimozione della soluzione, viene visualizzata nuovamente la pagina soluzioni.

L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole tenere l'area di lavoro Log Analytics, è necessario eliminarla manualmente dalla portale di Azure:

1. Cercare e selezionare **aree di lavoro log Analytics**.

2. Nella pagina area di lavoro Log Analytics selezionare l'area di lavoro.

3. Scegliere **Elimina** dal menu nella pagina delle impostazioni dell'area di lavoro.

4. Se non si vogliono tenere i [componenti della soluzione](#solution-components)dell'account di automazione di Azure, è possibile eliminarli manualmente.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare](automation-solution-vm-management-enable.md) la soluzione **avvio/arresto di macchine virtuali durante gli orari** di indisponibilità per le macchine virtuali di Azure.
