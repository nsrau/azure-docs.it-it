---
title: Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività
description: Questa soluzione di gestione delle macchine virtuali avvia e arresta le macchine virtuali di Azure in base a una pianificazione e monitora in modo proattivo dai log di Monitoraggio di Azure.This VM management solution starts and stops your Azure virtual machines on a schedule and proactively monitors from Azure Monitor logs.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548365"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure

Avvia/arresta macchine virtuali durante l'avvio delle soluzioni fuori orario e arresta le macchine virtuali di Azure in pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite i log di Monitoraggio di Azure e invia messaggi di posta elettronica facoltativi tramite gruppi di [azioni.](../azure-monitor/platform/action-groups.md) Supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. 

Per usare questa soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere [Account RunAs classici](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> La soluzione Avvia/Arresta macchine virtuali durante le ore non in orario è stata aggiornata per supportare le versioni più recenti dei moduli di Azure disponibili.

Questa soluzione fornisce un'opzione di automazione decentralizzata a basso costo per gli utenti che vogliono ottimizzare i costi delle macchine virtuali. Con questa soluzione, è possibile:

- [Pianificare l'avvio e l'arresto delle macchine virtuali.](automation-solution-vm-management-config.md#schedule)
- Pianificare l'avvio e l'arresto delle macchine virtuali in ordine crescente [usando i tag](automation-solution-vm-management-config.md#tags) di Azure (non supportati per le macchine virtuali classiche).
- Arresto automatico delle macchine virtuali in base [all'utilizzo ridotto della CPU.](automation-solution-vm-management-config.md#cpuutil)

Di seguito sono riportate le limitazioni relative alla soluzione corrente:

- Questa soluzione gestisce macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di Automazione di Azure.
- Questa soluzione è disponibile in Azure e Azure per enti pubblici in qualsiasi area che supporti un'area di lavoro di Log Analytics, un account di Automazione di Azure e Avvisi.This solution is available in Azure and Azure Government to any region that supports a Log Analytics workspace, an Azure Automation account, and Alerts. Le aree di Azure per enti pubblici attualmente non supportano la funzionalità di posta elettronica.

> [!NOTE]
> Se si usa la soluzione per le macchine virtuali classiche, tutte le macchine virtuali verranno elaborate in sequenza per ogni servizio cloud. Le macchine virtuali vengono comunque elaborate in parallelo tra servizi cloud diversi. Se si dispone di più di 20 macchine virtuali per servizio cloud, è consigliabile creare più pianificazioni con il runbook padre **ScheduledStartStop_Parent** e specificare 20 macchine virtuali per ogni pianificazione. Nelle proprietà della pianificazione specificare come elenco separato da virgole i nomi delle macchine virtuali nel parametro **VMList.In** the schedule properties, specify as a comma-separated list, VM names in the VMList parameter. In caso contrario, se il processo di automazione per questa soluzione viene eseguito più di tre ore, viene temporaneamente scaricato o interrotto in base al limite di [condivisione equa.](automation-runbook-execution.md#fair-share)
>
> Le sottoscrizioni Cloud Solution Provider di Azure (Azure CSP) supportano solo il modello Azure Resource Manager, i servizi non Azure Resource Manager non sono disponibili nel programma. Quando è in esecuzione la soluzione Avvio/Arresto, è possibile che vengano visualizzati degli errori perché le risorse classiche sono gestite dai cmdlet. Per altre informazioni su CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Se si usa una sottoscrizione di CSP, è necessario modificare la variabile [**External_EnableClassicVMs**](#variables) su **False** dopo la distribuzione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

I runbook per questa soluzione funzionano con un [account RunAs di Azure](automation-create-runas-account.md). L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che potrebbe scadere o essere modificata di frequente.

È consigliabile usare un account di automazione separato per la soluzione Start/Stop VM. Ciò è dovuto al fatto che le versioni dei moduli di Azure vengono aggiornate di frequente e i relativi parametri possono cambiare. La soluzione Start/Stop VM non viene aggiornata alla stessa cadenza, pertanto potrebbe non funzionare con le versioni più recenti dei cmdlet utilizzati. Ti consigliamo inoltre di testare gli aggiornamenti dei moduli in un account di automazione di test prima di importarli negli account di automazione di produzione.

### <a name="permissions"></a>Autorizzazioni

Esistono alcune autorizzazioni di cui un utente deve disporre per distribuire la soluzione Start/Stop VMs durante le ore non lavorative. Queste autorizzazioni sono diverse se si usa un'area di lavoro Account di automazione e Log Analytics creata in precedena o se ne crea di nuove durante la distribuzione. Se si è un collaboratore della sottoscrizione e un amministratore globale nel tenant di Azure Active Directory, non è necessario configurare le autorizzazioni seguenti. Se non si dispone di tali diritti o è necessario configurare un ruolo personalizzato, vedere le autorizzazioni necessarie di seguito.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Area di lavoro Account di automazione e Log Analytics preesistente

Per distribuire la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative in un'area di lavoro Account di automazione e Log Analytics esistente, l'utente che distribuisce la soluzione richiede le autorizzazioni seguenti per il **gruppo di risorse.** Per altre informazioni sui ruoli, vedere Ruoli personalizzati per le risorse di Azure.To learn more about roles, see [Custom roles for Azure resources.](../role-based-access-control/custom-roles.md)

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nuovo account di automazione e una nuova area di lavoro di Log Analytics

Per distribuire la soluzione Avvia/Arresta macchine virtuali durante le ore non lavorative in una nuova area di lavoro Account di automazione e Log Analytics, l'utente che distribuisce la soluzione necessita delle autorizzazioni definite nella sezione precedente e delle autorizzazioni seguenti:To deploy the Start/Stop VMs during off hours solution to a new Automation Account and Log Analytics workspace, the user deploying the solution needs the permissions defined in the preceding section as well as the following permissions:

- Coamministratore della sottoscrizione: è necessario creare l'account RunAs classico solo se si intende gestire le macchine virtuali classiche. [Gli account RunAs classici](automation-create-standalone-account.md#classic-run-as-accounts) non vengono più creati per impostazione predefinita.
- Membro del ruolo **Sviluppatore applicazione** [Azure Active Directory.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Per ulteriori informazioni sulla configurazione degli account [RunAs, vedere Autorizzazioni per configurare gli account RunAs](manage-runas-account.md#permissions).
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

Questa soluzione include runbook preconfigurati, pianificazioni e integrazione con i log di Monitoraggio di Azure in modo da poter personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali.

### <a name="runbooks"></a>Runbook

La tabella seguente elenca i runbook distribuiti nell'account di Automazione da questa soluzione. Non apportare modifiche al codice del runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente alcun runbook con *figlio* aggiunto al nome.

Tutti i runbook padre includono il parametro _WhatIf_. Se impostato su **True**, _WhatIf_ supporta i dettagli relativi al comportamento esatto del runbook quando viene eseguito senza il parametro _WhatIf_ e verifica che vengano specificate come destinazione le macchine virtuali corrette. Quando il parametro _WhatIf_ è impostato su **False**, un runbook esegue solo le azioni definite.

|Runbook | Parametri | Descrizione|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato dal runbook padre. Questo runbook crea gli avvisi in base alle risorse per lo scenario AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True o False  | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_.<br> *WhatIf* convalida la logica del runbook senza eseguirlo.|
|AutoStop_Disable | none | Disabilita gli avvisi di AutoStop e la pianificazione predefinita.|
|AutoStop_VM_Child | WebHookData | Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare la macchina virtuale classica.|
|AutoStop_VM_Child_ARM | WebHookData |Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare la VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Azione: avvio o arresto<br> VMList  | Questo runbook usato per eseguire l'avvio o l'arresto dell'azione nel gruppo VM classico di Servizi cloud.<br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Azione: avvio o arresto <br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato.|
|ScheduledStartStop_Child_Classic | VMName<br> Azione: avvio o arresto<br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o di arresto per l'arresto pianificato per le macchine virtuali classiche. |
|ScheduledStartStop_Parent | Azione: avvio o arresto <br>VMList <br> WhatIf: True o False | Questa impostazione ha effetto su tutte le macchine virtuali della sottoscrizione. Modificare **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupNames** in modo che vengano eseguiti solo in questi gruppi di risorse di destinazione. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile **External_ExcludeVMNames**.<br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_.<br> _WhatIf_ convalida la logica del runbook senza eseguirlo.|
|SequencedStartStop_Parent | Azione: avvio o arresto <br> WhatIf: True o False<br>VMList| Creare tag denominati **sequencestart** e **sequencestop** in ogni macchina virtuale per cui si vuole sequenziare l'attività di avvio/arresto. I nomi dei tag distinguono tra maiuscole e minuscole. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine in cui si vuole eseguire l'avvio o l'arresto. <br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_. <br> _WhatIf_ convalida la logica del runbook senza eseguirlo. <br> **Nota**: le macchine virtuali devono trovarsi all'interno di gruppi di risorse definiti come External_Start_ResourceGroupNames External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di Automazione. Modificare solo le variabili con il prefisso **External**. La modifica delle variabili con il prefisso **Internal** provoca effetti indesiderati.

|Variabile | Descrizione|
|---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori possibili sono: **GreaterThan**, **GreaterThanOrEqual**, **LessThan** e **LessThanOrEqual**.|
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|
|External_AutoStop_Frequency | Frequenza di valutazione per la regola. Questo parametro accetta l'input nel formato timespan. I valori possibili sono da 5 a 6 ore. |
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.|
|External_AutoStop_Severity | Gravità dell'avviso metrico, che può variare da 0 a 4. |
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile _External_AutoStop_MetricName_. I valori di percentuale possono variare da 1 a 100.|
|External_AutoStop_TimeAggregationOperator | Operatore dell'aggregazione temporale che viene applicato alle dimensioni della finestra selezionata per valutare la condizione. I valori possibili sono: **Average**, **Minimum**, **Maximum**, **Total** e **Last**.|
|External_AutoStop_TimeWindow | Dimensioni della finestra durante le quali Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|
|External_EnableClassicVMs| Specifica se le macchine virtuali classiche sono considerate come destinazione della soluzione. Il valore predefinito è True. Il valore deve essere impostato su False per le sottoscrizioni CSP. Le macchine virtuali classiche richiedono un account [RunAs classico.](automation-create-standalone-account.md#classic-run-as-accounts)|
|External_ExcludeVMNames | Immettere i nomi delle macchine virtuali da escludere, separandoli usando una virgola senza spazi. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco separato da virgole, le macchine virtuali che sono impostate per l'esclusione possono essere avviate o arrestate inavvertitamente.|
|External_Start_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di avvio.|
|External_Stop_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di arresto.|
|External_WaitTimeForVMRetrySeconds |Tempo di attesa in secondi per le azioni da eseguire sulle macchine virtuali per il runbook di avvio/arresto in sequenza.<br> Il valore predefinito è 2100 secondi e supporta la configurazione su un valore massimo di 10800 o tre ore.|
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|
|Internal_AutoSnooze_ARM_WebhookURI | Specifica l'URI Webhook chiamato per lo scenario AutoStop per le macchine virtuali classiche.|
|Internal_AutoSnooze_WebhookUri | Specifica l'URI del Webhook chiamato per lo scenario AutoStop.|
|Internal_AzureSubscriptionId | Specifica l'ID sottoscrizione di Azure.|
|Internal_ResourceGroupName | Specifica il nome del gruppo di risorse dell'account di Automazione.|

>[!NOTE]
>Per la variabile **External_WaitTimeForVMRetryInSeconds**, il valore predefinito è stato aggiornato da 600 a 2100. Questa variabile consente al runbook dello scenario di **avvio/arresto in sequenza** di attendere le operazioni figlio per il numero di secondi specificato prima di procedere con l'azione successiva.
>

In tutti gli scenari le variabili **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames** sono necessarie per definire la destinazione delle macchine virtuali, con l'eccezione di fornire un elenco separato da virgole delle macchine virtuali per i runbook **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** e **ScheduledStartStop_Parent**. In altre parole, le macchine virtuali devono risiedere nei gruppi di risorse di destinazione perché si verifichino le azioni di avvio e arresto. La logica funziona in modo simile ai criteri di Azure perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e lasciare che le azioni vengano ereditate dalle nuove macchine virtuali create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.È possibile modificarle o creare le proprie pianificazioni personalizzate.Tutte le pianificazioni sono disabilitate per impostazione predefinita, ad eccezione di **Scheduled_StartVM** e **Scheduled_StopVM**.

Non è consigliabile abilitare tutte le pianificazioni, perché potrebbe verificarsi una sovrapposizione delle azioni di pianificazione. È meglio determinare quali ottimizzazioni si vogliono eseguire e apportare le modifiche di conseguenza. Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.

|Nome pianificazione | Frequenza | Descrizione|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook AutoStop_CreateAlert_Parent ogni 8 ore, che a sua volta arresta i valori basati sulla macchina virtuale in External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure. In alternativa, è possibile specificare un elenco separato da virgole delle macchine virtuali usando il parametro VMList.|
|Scheduled_StopVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro _Stop_ ogni giorno all'ora specificata.Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili di asset.Abilitare la pianificazione correlata, **Scheduled-StartVM**.|
|Scheduled_StartVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro _Start_ ogni giorno all'ora specificata. Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili appropriate.Abilitare la pianificazione correlata, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue il runbook Sequenced_Parent con un parametro _Stop_ ogni venerdì all'ora specificata.Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag **SequenceStop** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere la sezione Runbook.Abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue il runbook Sequenced_Parent con un parametro _Start_ ogni lunedì a una determinata ora. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere la sezione Runbook. Abilitare la pianificazione correlata, **Sequenced-StopVM**.|

## <a name="enable-the-solution"></a>Abilitare la soluzione

Per iniziare a usare la soluzione, eseguire i passaggi descritti nella [soluzione Abilita avvio/arresto macchine virtuali.](automation-solution-vm-management-enable.md)

## <a name="viewing-the-solution"></a>Visualizzazione della soluzione

È possibile accedere alla soluzione dopo averla abilitata in uno dei modi seguenti:

* Nell'account di automazione selezionare **Avvia/Arresta macchina virtuale** in **Risorse correlate**. Nella pagina **Avvia/Interrompi macchina virtuale** selezionare **Gestisci la soluzione** sul lato destro della pagina, nella sezione Gestisci **soluzioni di avvio/arresto macchina virtuale.**

* Passare all'area di lavoro di Log Analytics collegata all'account di automazione e, dopo aver selezionato l'area di lavoro, selezionare **Soluzioni** nel riquadro sinistro. Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM [area di lavoro]** dall'elenco.  

Selezionando la soluzione, viene visualizzata la pagina della soluzione **Start-Stop-VM[workspace]** (Avvio-Arresto-VM[Area di lavoro]), dove è possibile esaminare importanti dettagli, ad esempio il riquadro **StartStopVM** (Avvio e arresto di VM). Come nell'area di lavoro Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Da qui è possibile eseguire un'analisi aggiuntiva dei record dei processi facendo clic sul riquadro ad anello. Il dashboard della soluzione mostra la cronologia processo e le query di ricerca log predefinite. Passare al portale avanzato di analisi dei log per eseguire la ricerca in base alle query di ricerca.

## <a name="update-the-solution"></a>Aggiornare la soluzione

Se è stata distribuita una versione precedente di questa soluzione, è necessario prima di tutto eliminarla dall'account prima di distribuire una versione aggiornata. Seguire i passaggi per [rimuovere la soluzione](#remove-the-solution) e quindi seguire i passaggi per distribuire la [soluzione](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Rimuovere la soluzione

Se non è più necessario usare la soluzione, è possibile eliminarla dall'account di Automazione. Eliminando la soluzione, vengono rimossi solo i runbook. Non vengono eliminate le pianificazioni o le variabili create quando la soluzione è stata aggiunta. Tali asset devono essere eliminati manualmente se non vengono usati con altri runbook.

Per eliminare la soluzione, attenersi alla procedura seguente:

1. Nell'account di Automazione, in **Risorse correlate,** selezionare **Area di lavoro collegata**.

2. Selezionare **Vai all'area di lavoro**.

3. In **Generale**selezionare **Soluzioni**. 

4. Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]). Nella pagina **VMManagementSolution[area di lavoro]** scegliere **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. Nella finestra **Elimina soluzione** confermare di voler eliminare la soluzione.

6. Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu. Quando il processo di rimozione della soluzione inizia, l'utente viene rimandato alla pagina **Soluzioni**.

L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole conservare l'area di lavoro Log Analytics, è necessario eliminarla manualmente. Tale operazione può essere eseguita dal portale di Azure:

1. Nel portale di Azure cercare e selezionare **Aree**di lavoro di Log Analytics .

2. Nella pagina **Aree** di lavoro di Log Analytics selezionare l'area di lavoro.

3. Scegliere **Elimina** dal menu nella pagina delle impostazioni dell'area di lavoro.

Se non si vogliono mantenere i componenti dell'account di Automazione di Azure, è possibile eliminarli manualmente. Per l'elenco dei runbook, delle variabili e delle pianificazioni create dalla soluzione, vedere [Componenti della soluzione](#solution-components).

## <a name="next-steps"></a>Passaggi successivi

[Abilitare](automation-solution-vm-management-enable.md) la soluzione Start/Stop durante le ore non orari per le macchine virtuali di Azure.Enable the Start/Stop during off-hours solution for your Azure VMs.
