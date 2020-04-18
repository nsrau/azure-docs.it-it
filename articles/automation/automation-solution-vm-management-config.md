---
title: Automazione di Azure Configurare/arrestare le macchine virtuali durante la soluzione fuori orarioAzure Automation Configure Start/Stop VMs during off hours solution
description: In questo articolo viene descritto come configurare la soluzione Start/Stop VM durante le ore non orari per supportare diversi casi d'uso o scenari.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604769"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Come configurare la soluzione Start/Stop VMs durante le ore non lavorative

Con la soluzione **Start/Stop VMs durante le ore non lavorative,** è possibile:With the Start/Stop VMs during off hours solution, you can:

- [Pianificare l'avvio e l'arresto delle macchine virtuali.](#schedule)
- Pianificare l'avvio e l'arresto delle macchine virtuali in ordine crescente [usando i tag](#tags) di Azure (non supportati per le macchine virtuali classiche).
- Arrestare automaticamente le macchine virtuali in base a [un utilizzo ridotto della CPU.](#cpuutil)

In questo articolo viene descritto come configurare correttamente la soluzione per supportare questi scenari. È inoltre possibile apprendere come eseguire altre impostazioni di configurazione comuni per la soluzione, ad esempio:You can also learn how to perform other common configuration settings for the solution, such as:

* [Configurare le notifiche tramite posta elettronica](#configure-email-notifications)

* [Aggiungere una macchina virtuale](#add-a-vm)

* [Escludere una macchina virtuale](#exclude-a-vm)

* [Modificare le pianificazioni di avvio e arresto](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: avviare/arrestare una macchina virtuale in base a una pianificazione

Questo scenario è la configurazione predefinita al momento della prima distribuzione della soluzione. Ad esempio, è possibile configurarla per poter arrestare tutte le macchine virtuali in una sottoscrizione quando si lascia il luogo di lavoro la sera e avviarle al mattino quando si ritorna in ufficio. Quando si configurano le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM** durante la distribuzione, queste avviano e arrestano le macchine virtuali di destinazione. È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali. Per informazioni su come configurare una pianificazione personalizzata, vedere [Modificare le pianificazioni di avvio e arresto](#modify-the-startup-and-shutdown-schedules).

> [!NOTE]
> Il fuso orario è il fuso orario corrente quando si configura il parametro dell'orario di pianificazione. Viene tuttavia archiviato nel formato UTC in Automazione di Azure. Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione.

È possibile controllare quali macchine virtuali rientrano nell'ambito configurando le variabili seguenti: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames**.

È possibile consentire di specificare come destinazione dell'azione una sottoscrizione e un gruppo di risorse oppure un elenco specifico di macchine virtuali, ma non entrambi.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Configurare `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` le variabili e per specificare le macchine virtuali di destinazione.

2. Abilitare e aggiornare le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Eseguire il runbook **ScheduledStartStop_Parent** con il campo parametro **ACTION** impostato per **l'avvio** e il campo del parametro **WHATIF** impostato su True per visualizzare in anteprima le modifiche.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il runbook **ScheduledStartStop_Parent** con **ACTION** impostato per **avviare**, aggiungere un elenco separato da virgole di macchine virtuali nel campo del parametro **VMList** e quindi impostare il campo del parametro **WHATIF** su True. Visualizzare in anteprima le modifiche.

2. Configurare `External_ExcludeVMNames` la variabile con un elenco separato da virgole di macchine virtuali (VM1, VM2, VM3).

3. Questo scenario non `External_Start_ResourceGroupNames` rispetta `External_Stop_ResourceGroupnames` le variabili e . Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

    > [!NOTE]
    > Il valore per **Target ResourceGroup Names** viene `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`archiviato come valore per both and . Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse. Per l'azione `External_Start_ResourceGroupNames`di `External_Stop_ResourceGroupNames` avvio, utilizzare e utilizzare per l'azione di arresto. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: avviare/arrestare le macchine virtuali in sequenza usando i tag

In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine. A tale scopo, seguire questa procedura:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Aggiungere `sequencestart` un `sequencestop` tag e un tag con un valore `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` intero positivo alle macchine virtuali destinate alle variabili e alle macchine virtuali. Le azioni di avvio e arresto vengono eseguite in ordine crescente. Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).

2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.

3. Eseguire il runbook **SequencedStartStop_Parent** con **ACTION** impostato per **l'avvio** e **WHATIF** su True per visualizzare in anteprima le modifiche.

4. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, eseguire manualmente il runbook con `Sequenced-StartVM` `Sequenced-StopVM` il parametro impostato su **False**oppure lasciare che l'automazione venga pianificata ed eseguita automaticamente in base alla pianificazione prescritta.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Aggiungere `sequencestart` a `sequencestop` e un tag con un valore intero positivo `VMList` alle macchine virtuali che si intende aggiungere al parametro.

2. Eseguire il **runbook SequencedStartStop_Parent** con **ACTION** impostato per **avviare**, aggiungere un elenco separato da virgole di macchine virtuali nel campo del parametro **VMList** e quindi impostare **WHATIF** su True. Visualizzare in anteprima le modifiche.

3. Configurare `External_ExcludeVMNames` la variabile con un elenco separato da virgole di macchine virtuali (VM1, VM2, VM3).

4. Questo scenario non `External_Start_ResourceGroupNames` rispetta `External_Stop_ResourceGroupnames` le variabili e . Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

5. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, eseguire manualmente il libro di **monitoraggio e diagnostica/monitoraggio-azione-gruppirunbook** con il parametro impostato su **False**. In alternativa, lasciare `Sequenced-StartVM` che `Sequenced-StopVM` l'automazione pianificare ed eseguire automaticamente seguendo la pianificazione prescritta.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatizzare l'avvio/arresto in base all'utilizzo della CPU

Questa soluzione consente di gestire il costo dell'esecuzione di Azure Resource Manager e delle macchine virtuali classiche nella sottoscrizione valutando le macchine virtuali non usate durante i periodi non di punta, ad esempio dopo l'orario di ufficio, e arrestandole automaticamente se l'utilizzo del processore è inferiore a una percentuale specificata.

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%. Questo scenario è controllato dalle seguenti variabili e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

È possibile abilitare e impostare come destinazione l'azione su una sottoscrizione e un gruppo di risorse oppure scegliere come destinazione un elenco specifico di macchine virtuali.

Quando si esegue il runbook **AutoStop_CreateAlert_Parent,** viene verificato che la sottoscrizione di destinazione, i gruppi di risorse e le macchine virtuali esistano. Se le macchine virtuali esistono, il runbook chiama quindi il **runbook AutoStop_CreateAlert_Child** per ogni macchina virtuale verificata dal runbook padre. Questo runbook figlio esegue le operazioni seguenti:This child runbook performs the following:

* Crea una regola di avviso metrica per ogni macchina virtuale verificata.

* Attiva il runbook **AutoStop_VM_Child** per una determinata macchina virtuale se la CPU scende al di sotto della soglia configurata per l'intervallo di tempo specificato. Questo runbook tenta quindi di arrestare la macchina virtuale.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Per impostare come destinazione l'azione di arresto automatico per tutte le macchine virtuali in una sottoscrizioneTo target the auto stop action against all VMs in a subscription

1. Assicurarsi `External_Stop_ResourceGroupNames` che la variabile sia vuota o che sia impostata su (carattere jolly).

2. [Passaggio facoltativo] Se si desidera escludere alcune macchine virtuali dall'arresto automatico, è possibile aggiungere un `External_ExcludeVMNames` elenco separato da virgole di nomi di macchine virtuali alla variabile.

3. Abilitare `Schedule_AutoStop_CreateAlert_Parent` la pianificazione da eseguire per creare le regole di avviso della metrica di arresto della macchina virtuale necessarie per tutte le macchine virtuali nella sottoscrizione. L'esecuzione di questo tipo di pianificazione consente di creare nuove regole di avviso di metrica quando nuove macchine virtuali vengono aggiunte alla sottoscrizione.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Per impostare come destinazione l'azione di arresto automatico su tutte le macchine virtuali in un gruppo di risorse o in più gruppi di risorseTo target the auto stop action against all VMs in a resource group or multiple resource groups

1. Aggiungere un elenco delimitato da virgole `External_Stop_ResourceGroupNames` di nomi di gruppi di risorse alla variabile.

2. Se si desidera escludere alcune delle macchine virtuali dall'arresto automatico, è possibile aggiungere `External_ExcludeVMNames` un elenco separato da virgole di nomi di macchine virtuali alla variabile.

3. Abilitare la pianificazione **del Schedule_AutoStop_CreateAlert_Parent** per l'esecuzione per creare le regole di avviso della metrica di arresto della macchina virtuale necessarie per tutte le macchine virtuali nei gruppi di risorse. L'esecuzione di questa operazione in base a una pianificazione consente di creare nuove regole di avviso di metrica man mano che vengono aggiunte nuove macchine virtuali ai gruppi di risorse.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>To target the autostop action to a list of VMs

1. Creare una nuova [pianificazione](shared-resources/schedules.md#creating-a-schedule) e collegarla al runbook **AutoStop_CreateAlert_Parent,** aggiungendo un `VMList` elenco separato da virgole di nomi di macchine virtuali al parametro.

2. Facoltativamente, se si desidera escludere alcune macchine virtuali dall'arresto automatico, è possibile aggiungere `External_ExcludeVMNames` un elenco separato da virgole di nomi di macchine virtuali alla variabile.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche di posta elettronica dopo la distribuzione della soluzione, modificare il gruppo di azioni creato durante la distribuzione.  

> [!NOTE]
> Le sottoscrizioni nel cloud di Azure per enti pubblici non supportano la funzionalità di posta elettronica di questa soluzione.

1. Nel portale di Azure passare a **Monitoraggio**e quindi **Gruppi di azioni**. Selezionare il gruppo di azioni denominato **StartStop_VM_Notication**.

    ![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-monitor.png)

2. Nella pagina **StartStop_VM_Notification**, fare clic su **Modifica dettagli** in **Dettagli**. Questo apre la pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). Aggiornare l'indirizzo di posta elettronica e fare clic su **OK** per salvare le modifiche.

    ![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/change-email.png)

    In alternativa è possibile aggiungere azioni aggiuntive al gruppo di azione, per altre informazioni sui gruppi di azioni, vedere [gruppi di azioni](../azure-monitor/platform/action-groups.md)

Di seguito è riportato un messaggio di posta elettronica di esempio che viene inviato quando la soluzione arresta macchine virtuali.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Aggiungere o escludere macchine virtuali

È possibile aggiungere macchine virtuali a cui applicare la soluzione oppure escludere determinate macchine dalla soluzione.

### <a name="add-a-vm"></a>Aggiungere una macchina virtuale

Esistono due opzioni che è possibile usare per assicurarsi che una macchina virtuale sia inclusa nella soluzione Start/Stop durante l'esecuzione.

* Ognuno dei [runbook](automation-solution-vm-management.md#runbooks) padre della `VMList` soluzione dispone di un parametro. È possibile passare un elenco separato da virgole di nomi di macchine virtuali a questo parametro quando si pianifica il runbook padre appropriato per la situazione e queste macchine virtuali verranno incluse durante l'esecuzione della soluzione.

* Per selezionare più `External_Start_ResourceGroupNames` macchine `External_Stop_ResourceGroupNames` virtuali, impostare e con i nomi dei gruppi di risorse che contengono le macchine virtuali che si vuole avviare o arrestare. È inoltre possibile impostare le `*` variabili su un valore di per fare in modo che la soluzione eseda su tutti i gruppi di risorse nella sottoscrizione.

### <a name="exclude-a-vm"></a>Escludere una macchina virtuale

Per escludere una macchina virtuale dalla soluzione, `External_ExcludeVMNames` è possibile aggiungerla alla variabile. Questa variabile è un elenco separato da virgole di macchine virtuali specifiche da escludere dalla soluzione Start/Stop. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco separato da virgole, le macchine virtuali che sono impostate per l'esclusione possono essere avviate o arrestate inavvertitamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificare le pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md). Per l'avvio e l'arresto di macchine virtuali devono essere definite due pianificazioni separate.

È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali a una data ora. In questo scenario è sufficiente creare una pianificazione **di arresto** e nessuna pianificazione **di avvio** corrispondente. A questo scopo, è necessario:

1. Assicurarsi di aver aggiunto i gruppi di risorse `External_Stop_ResourceGroupNames` per le macchine virtuali da arrestare nella variabile.

2. Creare una pianificazione personalizzata per l'ora di arresto delle macchine virtuali.

3. Passare al runbook **ScheduledStartStop_Parent** e fare clic su **Pianificazione**. In questo modo è possibile selezionare la pianificazione creata nel passaggio precedente.

4. Selezionare **Parametri ed eseguire le impostazioni** e impostare il campo **ACTION** su **Stop**.

5. Selezionare **OK** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come risolvere i problemi relativi all'avvio/arresto delle macchine virtuali durante le ore non lavorative, vedere [Risoluzione dei problemi relativi all'avvio/arresto](troubleshoot/start-stop-vm.md)delle macchine virtuali.

* [Esaminare](automation-solution-vm-management-logs.md) i record di automazione scritti in Log di monitoraggio di Azure e le query di ricerca log di esempio per analizzare lo stato dei processi di runbook di automazione da Start/Stop VMs.
