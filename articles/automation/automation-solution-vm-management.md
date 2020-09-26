---
title: Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività di Automazione di Azure
description: Questo articolo illustra la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività, che consente di avviare o arrestare le VM in base a una pianificazione e di monitorarle in modo proattivo dai log di Monitoraggio di Azure.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 236b4f47894db8aa8880b7535b6ee0921802a31c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317362"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Panoramica di Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Il Avvio/Arresto di macchine virtuali durante gli orari di minore attività funzionalità avvia o arresta le VM di Azure abilitate. Consente di avviare o arrestare computer in base a pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite i log di Monitoraggio di Azure e invia messaggi di posta elettronica facoltativi usando i [gruppi di azioni](../azure-monitor/platform/action-groups.md). Questa funzionalità può essere abilitata sia in Azure Resource Manager che nelle macchine virtuali classiche per la maggior parte degli scenari. 

Questa funzionalità Usa il cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) per avviare le macchine virtuali. USA [Stop-AzVM](/powershell/module/az.compute/stop-azvm) per arrestare le macchine virtuali.

> [!NOTE]
> Mentre i manuali operativi sono stati aggiornati per l'uso dei nuovi cmdlet di Azure AZ Module, usano l'alias del prefisso AzureRM.

> [!NOTE]
> La funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività è stata aggiornata per supportare le versioni più recenti dei moduli di Azure disponibili. La versione aggiornata di questa funzionalità, disponibile nel Marketplace, non supporta moduli di AzureRM perché è stata eseguita la migrazione da AzureRM ai moduli di Az.

Questa funzionalità fornisce un'opzione di automazione decentralizzata a basso costo per gli utenti che vogliono ottimizzare i costi delle macchine virtuali. È possibile usare questa funzionalità per:

- [Pianificare le macchine virtuali da avviare e arrestare](automation-solution-vm-management-config.md#schedule).
- Pianificare le macchine virtuali per l'avvio e l'arresto in ordine crescente usando i [tag di Azure](automation-solution-vm-management-config.md#tags). Questa attività non è supportata per le macchine virtuali classiche.
- Arrestare automaticamente le macchine virtuali in caso di [basso utilizzo della CPU](automation-solution-vm-management-config.md#cpuutil).

La funzionalità corrente presenta le limitazioni seguenti:

- Gestisce macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di Automazione di Azure.
- È disponibile in Azure e Azure per enti pubblici per tutte le aree che supportano un'area di lavoro Log Analytics, un account di Automazione di Azure e Avvisi. Le aree di Azure per enti pubblici attualmente non supportano funzionalità di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

- I runbook per la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività funzionano con un [account RunAs di Azure](./manage-runas-account.md). L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che potrebbe scadere o essere modificata di frequente.

- L'account di automazione collegato e l'area di lavoro Log Analytics devono trovarsi nello stesso gruppo di risorse.

- È consigliabile usare un account di Automazione separato per lavorare con macchine virtuali abilitate per la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Le versioni dei moduli di Azure vengono aggiornate di frequente ed è possibile che i rispettivi parametri subiscano modifiche. La funzionalità non viene aggiornata in base alla stessa frequenza ed è possibile che non funzioni con le versioni più recenti dei cmdlet usati. È consigliabile testare gli aggiornamenti dei moduli in un account di Automazione di prova prima di importarli in account di Automazione di produzione.

## <a name="permissions"></a>Autorizzazioni

Per abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività sono necessarie determinate autorizzazioni. Le autorizzazioni necessarie se la funzionalità usa un'area di lavoro Log Analytics o un account di Automazione creato in precedenza sono diverse da quelle necessarie se la funzionalità crea un nuovo account e una nuova area di lavoro.

Non è necessario configurare autorizzazioni se si è un Collaboratore per la sottoscrizione e un Amministratore globale nel tenant di Azure Active Directory (AD). Se questi diritti non sono disponibili o se è necessario configurare un ruolo personalizzato, assicurarsi di avere le autorizzazioni illustrate di seguito.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Autorizzazioni per un account di Automazione e un'area di lavoro Log Analytics già esistenti

Per abilitare una macchina virtuale per la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività con un account di Automazione e un'area di lavoro Log Analytics esistenti, sono necessarie le autorizzazioni seguenti nell'ambito Gruppo di risorse. Per altre informazioni sui ruoli, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md).

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
| Microsoft.OperationalInsights/workspaces/* | Gruppo di risorse |
| Microsoft.Insights/diagnosticSettings/write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/Write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/read | Gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Gruppo di risorse |
| Microsoft.Resources/deployments/* | Gruppo di risorse |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Autorizzazioni per un nuovo account di Automazione e una nuova area di lavoro Log Analytics

È possibile abilitare le macchine virtuali per la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività con un nuovo account di Automazione e una nuova area di lavoro Log Analytics. In questo caso sono necessarie le autorizzazioni definite nella sezione precedente, oltre alle autorizzazioni definite in questa sezione. Sono necessari anche i ruoli seguenti:

- Coamministratore nella sottoscrizione. Questo ruolo è necessario per creare la versione classica dell'account RunAs se si prevede di gestire VM classiche. Gli [account RunAs classici](automation-create-standalone-account.md#create-a-classic-run-as-account) non vengono più creati per impostazione predefinita.
- Appartenenza al ruolo Sviluppatore applicazioni di [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Per altre informazioni sulla configurazione degli account RunAs, vedere [Autorizzazioni per la configurazione degli account RunAs](manage-runas-account.md#permissions).
- Collaboratore nella sottoscrizione o le autorizzazioni seguenti.

| Autorizzazione |Scope|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription || Microsoft.Automation/automationAccounts/connections/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/write | Gruppo di risorse |

## <a name="components"></a>Componenti

La funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività include manuali operativi preconfigurati, pianificazioni e integrazione con i log di monitoraggio di Azure. È possibile usare questi elementi per personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali.

### <a name="runbooks"></a>Runbook

La tabella seguente elenca i runbook distribuiti da questa funzionalità nell'account di Automazione. NON apportare modifiche al codice del runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente i runbook con **child** accodato al nome.

Tutti i runbook padre includono il parametro `WhatIf`. Se impostato su True, il parametro supporta i dettagli relativi al comportamento esatto del runbook quando viene eseguito senza il parametro e verifica che vengano specificate come destinazione le macchine virtuali corrette. Quando il parametro `WhatIf` è impostato su False, un runbook esegue solo le azioni definite.

|Runbook | Parametri | Descrizione|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato dal runbook padre. Questo runbook crea gli avvisi in base alle risorse per lo scenario di arresto automatico.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true o false  | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> `VMList` è un elenco delimitato da virgole di macchine virtuali (senza spazi vuoti), ad esempio `vm1,vm2,vm3` .<br> `WhatIf` abilita la convalida della logica del runbook senza eseguirlo.|
|AutoStop_Disable | nessuno | Disabilita gli avvisi di arresto automatico e la pianificazione predefinita.|
|AutoStop_VM_Child | WebHookData | Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare una VM classica.|
|AutoStop_VM_Child_ARM | WebHookData |Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare una VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Azione: Avviare o arrestare<br> VMList  | Esegue l'avvio o l'arresto di azioni in un gruppo di VM classiche da parte di servizi cloud. |
|ScheduledStartStop_Child | VMName <br> Azione: Avviare o arrestare <br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato.|
|ScheduledStartStop_Child_Classic | VMName<br> Azione: Avviare o arrestare<br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato per le VM classiche. |
|ScheduledStartStop_Parent | Azione: Avviare o arrestare <br>VMList <br> WhatIf: true o false | Avvia o arresta tutte le VM della sottoscrizione. Modificare le variabili `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` per l'esecuzione solo in questi gruppi di risorse interessati. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile `External_ExcludeVMNames`.|
|SequencedStartStop_Parent | Azione: Avviare o arrestare <br> WhatIf: true o false<br>VMList| Crea i tag denominati **sequencestart** e **sequencestop** in ogni macchina virtuale per cui si vuole mettere in sequenza l'attività di avvio/arresto. I nomi dei tag distinguono tra maiuscole e minuscole. Il valore del tag deve essere un elenco di numeri interi positivi, ad esempio, `1,2,3` che corrisponde all'ordine in cui si desidera avviare o arrestare. <br>**Nota**: le VM devono trovarsi nei gruppi di risorse definiti nelle variabili `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di Automazione. Modificare solo le variabili con il prefisso `External`. La modifica delle variabili con il prefisso `Internal` provoca effetti indesiderati.

> [!NOTE]
> Le limitazioni relative al nome della VM e al gruppo di risorse sono in gran parte il risultato delle dimensioni delle variabili. Vedere [Asset di tipo variabile in Automazione di Azure](./shared-resources/variables.md).

|Variabile | Descrizione|
|---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori accettabili sono `GreaterThan`, `GreaterThanOrEqual`, `LessThan` e `LessThanOrEqual`.|
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|
|External_AutoStop_Frequency | Frequenza di valutazione per la regola. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore. |
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.|
|External_AutoStop_Severity | Gravità dell'avviso delle metriche, che può essere compresa tra 0 e 4. |
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile `External_AutoStop_MetricName`. I valori di percentuale sono compresi tra 1 e 100.|
|External_AutoStop_TimeAggregationOperator | Operatore dell'aggregazione temporale che viene applicato alle dimensioni della finestra selezionata per valutare la condizione. I valori accettabili sono `Average`, `Minimum`, `Maximum`, `Total` e `Last`.|
|External_AutoStop_TimeWindow | Dimensioni della finestra durante le quali Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|
|External_EnableClassicVMs| Valore che specifica se le VM classiche sono interessate dalla funzionalità. Il valore predefinito è True. Imposta la variabile su False per sottoscrizioni Azure Cloud Solution Provider (CSP). Le VM classiche richiedono un [account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Elenco delimitato da virgole di nomi di VM da escludere, limitato a 140 VM. Se si aggiungono più di 140 macchine virtuali all'elenco, le macchine virtuali specificate per l'esclusione potrebbero essere avviate o arrestate inavvertitamente.|
|External_Start_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse interessati dalle azioni di avvio.|
|External_Stop_ResourceGroupNames | Elenco delimitato da virgole di uno o più gruppi di risorse interessati dalle azioni di arresto.|
|External_WaitTimeForVMRetrySeconds |Tempo di attesa in secondi per le azioni da eseguire sulle VM per il runbook **SequencedStartStop_Parent**. Questa variabile consente al runbook di attendere le operazioni figlio per un numero di secondi specificato prima di passare all'azione successiva. Il tempo di attesa massimo è 10800 ovvero tre ore. Il valore predefinito è 2100 secondi.|
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|
|Internal_AutoSnooze_ARM_WebhookURI | URI del webhook chiamato per lo scenario AutoStop per le macchine virtuali.|
|Internal_AutoSnooze_WebhookUri | URI del webhook chiamato per lo scenario AutoStop per le macchine virtuali classiche.|
|Internal_AzureSubscriptionId | ID sottoscrizione di Azure.|
|Internal_ResourceGroupName | Nome del gruppo di risorse dell'account di Automazione.|

>[!NOTE]
>Per la variabile `External_WaitTimeForVMRetryInSeconds`, il valore predefinito è stato aggiornato da 600 a 2100. 

In tutti gli scenari le variabili `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` sono necessarie per specificare le VM come destinazione, ad eccezione dell'elenco delimitato da virgole di VM per i runbook **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** e **ScheduledStartStop_Parent**. In altre parole, le macchine virtuali devono appartenere ai gruppi di risorse di destinazione perché si verifichino le azioni di avvio e arresto. La logica funziona in modo simile a Criteri di Azure perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e lasciare che le azioni vengano ereditate dalle nuove macchine virtuali create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione. È possibile modificarle o creare le proprie pianificazioni personalizzate. Tutte le pianificazioni sono disabilitate per impostazione predefinita, ad eccezione delle pianificazioni **Scheduled_StartVM** e **Scheduled_StopVM**.

Non abilitare tutte le pianificazioni, perché potrebbe verificarsi una sovrapposizione delle azioni di pianificazione. È meglio determinare quali ottimizzazioni si vogliono eseguire e apportare le modifiche di conseguenza. Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.

|Nome pianificazione | Frequenza | Descrizione|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook **AutoStop_CreateAlert_Parent** ogni 8 ore, che a sua volta arresta i valori basati su VM nelle variabili `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`. In alternativa, è possibile specificare un elenco separato da virgole delle macchine virtuali usando il parametro `VMList`.|
|Scheduled_StopVM | Definito dall'utente, ogni giorno | Esegue il runbook **ScheduledStopStart_Parent** con un parametro `Stop` ogni giorno all'ora specificata. Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dagli asset delle variabili. Abilitare la pianificazione **Scheduled-StartVM** correlata.|
|Scheduled_StartVM | Definito dall'utente, ogni giorno | Esegue il runbook **ScheduledStopStart_Parent** con un valore di parametro `Start` ogni giorno all'ora specificata. Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dagli asset delle variabili. Abilitare la pianificazione **Scheduled-StopVM** correlata.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue il runbook **Sequenced_StopStop_Parent** con un valore di parametro `Stop` ogni venerdì all'ora specificata. Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag **SequenceStop** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere [Runbook](#runbooks). Abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue il runbook **SequencedStopStart_Parent** con un valore di parametro `Start` ogni lunedì all'ora specificata. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sugli asset delle variabili, vedere [Runbook](#runbooks). Abilitare la pianificazione correlata, **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>Usare la funzionalità con le macchine virtuali classiche

Se si usa la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività per le macchine virtuali classiche, Automazione elabora tutte le VM in sequenza per il servizio cloud. Le macchine virtuali vengono comunque elaborate in parallelo tra servizi cloud diversi. 

Per usare la funzionalità con le VM classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere [Creare un account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se sono presenti più di 20 macchine virtuali per ogni servizio cloud, di seguito sono disponibili alcune raccomandazioni:

* Creare più pianificazioni con il runbook padre **ScheduledStartStop_Parent** e specificare 20 VM per pianificazione. 
* Nelle proprietà della pianificazione, usare il `VMList` parametro per specificare i nomi delle VM come un elenco delimitato da virgole (senza spazi vuoti). 

In caso contrario, se il processo di Automazione per questa funzionalità viene eseguito per più di tre ore, verrà scaricato temporaneamente o arrestato in base al limite di [condivisione equa](automation-runbook-execution.md#fair-share).

Le sottoscrizioni di Azure CSP supportano solo il modello di Azure Resource Manager. I servizi diversi da Azure Resource Manager non sono disponibili nel programma. Quando la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività viene eseguita, è possibile che vengano visualizzati errori perché include cmdlet per la gestione di risorse classiche. Per altre informazioni su CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services). Se si usa una sottoscrizione di CSP, è necessario impostare la variabile [External_EnableClassicVMs](#variables) su False dopo la distribuzione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>Abilitare la funzionalità

Per iniziare a usare la funzionalità, seguire la procedura in [Abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-enable.md).

## <a name="view-the-feature"></a>Visualizzare la funzionalità

Usare uno dei meccanismi seguenti per accedere alla funzionalità abilitata:

* Dall'account di Automazione selezionare **Avvio/Arresto di macchine virtuali** in **Risorse correlate**. Nella pagina Avvio/Arresto di macchine virtuali selezionare **Gestisci la soluzione** in **Gestisci soluzioni di Avvio/Arresto di macchine virtuali**.

* Passare all'area di lavoro Log Analytics collegata all'account Automazione. Dopo avere selezionato l'area di lavoro, scegliere **Soluzioni** dal riquadro sinistro. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]) dall'elenco.  

Se si seleziona la funzionalità, verrà visualizzata la pagina Start-Stop-VM[workspace] (Avvio-Arresto-VM[Area di lavoro]) dove è possibile esaminare importanti dettagli, ad esempio le informazioni nel riquadro **StartStopVM** (Avvio e arresto di VM). Come nell'area di lavoro Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della funzionalità che sono stati avviati e completati.

![Pagina Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

È possibile eseguire un'analisi aggiuntiva dei record dei processi facendo clic sul riquadro ad anello. Il dashboard mostra la cronologia processo e le query di ricerca log predefinite. Passare al portale avanzato di Log Analytics per eseguire una ricerca in base alle query di ricerca.

## <a name="update-the-feature"></a>Aggiornare la funzionalità

Se è stata distribuita una versione precedente della funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è necessario prima di tutto eliminarla dall'account prima di distribuire una versione aggiornata. Seguire la procedura per [rimuovere la funzionalità](#remove-the-feature) e quindi seguire la procedura per [abilitarla](automation-solution-vm-management-enable.md).

## <a name="remove-the-feature"></a>Rimuovere la funzionalità

Se non è più necessario usare la funzionalità, è possibile eliminarla dall'account di Automazione. L'eliminazione della funzionalità rimuoverà solo i runbook associati. Non vengono eliminate le pianificazioni o le variabili create quando la funzionalità è stata aggiunta. 

Per eliminare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività:

1. Dall'account di Automazione selezionare **Area di lavoro collegata** in **Risorse correlate**.

2. Selezionare **Vai all'area di lavoro**.

3. Fare clic su **Soluzioni** e **Generale**. 

4. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]). 

5. Nella pagina VMManagementSolution[Workspace] selezionare **Elimina** dal menu.<br><br> ![Eliminare la funzionalità di gestione delle VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Nella finestra Elimina soluzione confermare di voler eliminare la funzionalità.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

8. L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole conservare l'area di lavoro Log Analytics, è necessario eliminarla manualmente dal portale di Azure:

    1. Cercare e selezionare le **Aree di lavoro di Log Analytics**.

    2. Nella pagina Area di lavoro di Log Analytics selezionare l'area di lavoro.

    3. Seleziona **Elimina** dal menu.

    4. Se non si vogliono conservare i [componenti della funzionalità](#components) dell'account di Automazione di Azure, è possibile eliminarli manualmente.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare la funzionalità nelle macchine virtuali nell'ambiente, vedere [Abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-enable.md).
