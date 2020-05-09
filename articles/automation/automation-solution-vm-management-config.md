---
title: Configurazione Avvio/Arresto di macchine virtuali durante gli orari di minore attività soluzione di automazione di Azure
description: Questo articolo descrive come configurare la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività per supportare diversi casi d'uso o scenari.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4cceb0d5ada82de73bc74c0ed408f8eb988ea8ec
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864267"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Come configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività soluzione

Con la soluzione **avvio/arresto di macchine virtuali durante gli orari di minore attività** è possibile:

- [Pianificare l'avvio e l'arresto delle macchine virtuali](#schedule).
- Pianificare le macchine virtuali per l'avvio e l'arresto in ordine crescente [usando i tag di Azure](#tags) (non supportati per le macchine virtuali classiche).
- Arresta automaticamente le macchine virtuali in base all' [utilizzo ridotto della CPU](#cpuutil).

Questo articolo descrive come configurare correttamente la soluzione per supportare questi scenari. È anche possibile apprendere come eseguire altre impostazioni di configurazione comuni per la soluzione, ad esempio:

* [Configurare le notifiche di posta elettronica](#configure-email-notifications)

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

1. Configurare le `External_Stop_ResourceGroupNames` variabili `External_ExcludeVMNames` e per specificare le macchine virtuali di destinazione.

2. Abilitare e aggiornare le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Eseguire il **ScheduledStartStop_Parent** runbook con il campo parametro **azione** impostato su **Start** e il campo parametro **WHATIF** impostato su true per visualizzare in anteprima le modifiche.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il **ScheduledStartStop_Parent** runbook con **azione** impostata su **Start**, aggiungere un elenco delimitato da virgole delle macchine virtuali nel campo del parametro **VMList** , quindi impostare il campo del parametro **WHATIF** su true. Visualizzare in anteprima le modifiche.

2. Configurare la `External_ExcludeVMNames` variabile con un elenco delimitato da virgole di macchine virtuali (VM1, VM2, VM3).

3. Questo scenario non rispetta le `External_Start_ResourceGroupNames` variabili e `External_Stop_ResourceGroupnames` . Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

    > [!NOTE]
    > Il valore per i **nomi di ResourceGroup di destinazione** viene archiviato come valore `External_Start_ResourceGroupNames` per `External_Stop_ResourceGroupNames`e. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse. Per l'azione di avvio `External_Start_ResourceGroupNames`, utilizzare e `External_Stop_ResourceGroupNames` utilizzare per l'azione arresta. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: avviare/arrestare le macchine virtuali in sequenza usando i tag

In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine. A tale scopo, seguire questa procedura:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Aggiungere un `sequencestart` oggetto e `sequencestop` un tag con un valore integer positivo alle macchine virtuali destinate `External_Start_ResourceGroupNames` a `External_Stop_ResourceGroupNames` variabili e. Le azioni di avvio e arresto vengono eseguite in ordine crescente. Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).

2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.

3. Eseguire l' **SequencedStartStop_Parent** runbook con **azione** impostata su **Start** e **WHATIF** su true per visualizzare in anteprima le modifiche.

4. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, eseguire manualmente runbook con il parametro impostato su **false**o lasciare che `Sequenced-StartVM` `Sequenced-StopVM` la pianificazione dell'automazione venga eseguita automaticamente in base alla pianificazione prestabilita.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Aggiungere un `sequencestart` oggetto e `sequencestop` un tag con un valore integer positivo alle macchine virtuali che si prevede di `VMList` aggiungere al parametro.

2. Eseguire il **SequencedStartStop_Parent** runbook con **azione** impostata su **Start**, aggiungere un elenco delimitato da virgole delle macchine virtuali nel campo del parametro **VMList** e quindi impostare **WHATIF** su true. Visualizzare in anteprima le modifiche.

3. Configurare la `External_ExcludeVMNames` variabile con un elenco delimitato da virgole di macchine virtuali (VM1, VM2, VM3).

4. Questo scenario non rispetta le `External_Start_ResourceGroupNames` variabili e `External_Stop_ResourceGroupnames` . Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

5. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, eseguire manualmente il **monitoraggio e la diagnostica/monitoraggio-azione-groupsrunbook** con il parametro impostato su **false**. In alternativa, lasciare che `Sequenced-StartVM` `Sequenced-StopVM` la pianificazione dell'automazione venga eseguita automaticamente in base alla pianificazione prestabilita.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatizzare l'avvio/arresto in base all'utilizzo della CPU

Questa soluzione consente di gestire i costi di esecuzione di Azure Resource Manager e macchine virtuali classiche nella sottoscrizione valutando le macchine virtuali che non vengono usate durante i periodi non di punta, ad esempio dopo le ore, e la chiusura automatica se l'utilizzo del processore è inferiore a una percentuale specificata.

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%. Questo scenario è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

È possibile abilitare e indirizzare l'azione a una sottoscrizione e a un gruppo di risorse oppure usare un elenco specifico di macchine virtuali.

Quando si esegue il **AutoStop_CreateAlert_Parent** Runbook, verifica che esistano la sottoscrizione di destinazione, i gruppi di risorse e le macchine virtuali. Se le macchine virtuali esistono, il Runbook chiama il Runbook **AutoStop_CreateAlert_Child** per ogni macchina virtuale verificata dal Runbook padre. Questo runbook figlio esegue le operazioni seguenti:

* Crea una regola di avviso della metrica per ogni macchina virtuale verificata.

* Attiva il **AutoStop_VM_Child** Runbook per una determinata macchina virtuale se la CPU scende al di sotto della soglia configurata per l'intervallo di tempo specificato. Questo runbook tenta quindi di arrestare la macchina virtuale.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Per indirizzare l'azione di arresto automatico a tutte le macchine virtuali in una sottoscrizione

1. Verificare che la `External_Stop_ResourceGroupNames` variabile sia vuota o impostata su * (carattere jolly).

2. [Passaggio facoltativo] Se si desidera escludere alcune macchine virtuali dalla chiusura automatica, è possibile aggiungere alla `External_ExcludeVMNames` variabile un elenco delimitato da virgole di nomi di VM.

3. Abilitare la `Schedule_AutoStop_CreateAlert_Parent` pianificazione da eseguire per creare le regole di avviso per l'arresto della macchina virtuale necessarie per tutte le macchine virtuali nella sottoscrizione. L'esecuzione di questo tipo di pianificazione consente di creare nuove regole di avviso per le metriche quando vengono aggiunte nuove macchine virtuali alla sottoscrizione.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Per indirizzare l'azione di arresto automatico a tutte le macchine virtuali in un gruppo di risorse o in più gruppi di risorse

1. Aggiungere un elenco delimitato da virgole di nomi di gruppi `External_Stop_ResourceGroupNames` di risorse alla variabile.

2. Se si vogliono escludere alcune macchine virtuali dalla chiusura automatica, è possibile aggiungere un elenco delimitato da virgole di nomi di VM alla `External_ExcludeVMNames` variabile.

3. Abilitare l'esecuzione della pianificazione **Schedule_AutoStop_CreateAlert_Parent** per creare le regole di avviso per l'arresto della macchina virtuale necessarie per tutte le macchine virtuali nei gruppi di risorse. L'esecuzione di questa operazione in base a una pianificazione consente di creare nuove regole di avviso per le metriche quando le nuove VM vengono aggiunte ai gruppi di risorse.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Per specificare come destinazione l'azione di arresto autostop in un elenco di macchine virtuali

1. Creare una nuova [pianificazione](shared-resources/schedules.md#create-a-schedule) e collegarla all' **AutoStop_CreateAlert_Parent** Runbook, aggiungendo un elenco delimitato da virgole di nomi di VM `VMList` al parametro.

2. Facoltativamente, se si desidera escludere alcune macchine virtuali dalla chiusura automatica, è possibile aggiungere alla `External_ExcludeVMNames` variabile un elenco delimitato da virgole di nomi di VM.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche tramite posta elettronica dopo la distribuzione della soluzione, modificare il gruppo di azioni creato durante la distribuzione.  

> [!NOTE]
> Le sottoscrizioni nel cloud di Azure per enti pubblici non supportano la funzionalità di posta elettronica di questa soluzione.

1. Nella portale di Azure passare a **monitoraggio**, quindi gruppi di **azioni**. Selezionare il gruppo di azioni denominato **StartStop_VM_Notication**.

    ![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-monitor.png)

2. Nella pagina **StartStop_VM_Notification**, fare clic su **Modifica dettagli** in **Dettagli**. Questo apre la pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). Aggiornare l'indirizzo di posta elettronica e fare clic su **OK** per salvare le modifiche.

    ![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/change-email.png)

    In alternativa è possibile aggiungere azioni aggiuntive al gruppo di azione, per altre informazioni sui gruppi di azioni, vedere [gruppi di azioni](../azure-monitor/platform/action-groups.md)

Di seguito è riportato un messaggio di posta elettronica di esempio che viene inviato quando la soluzione arresta macchine virtuali.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Aggiungere o escludere macchine virtuali

È possibile aggiungere macchine virtuali a cui applicare la soluzione oppure escludere determinate macchine dalla soluzione.

### <a name="add-a-vm"></a>Aggiungere una macchina virtuale

Sono disponibili due opzioni che è possibile usare per verificare che una macchina virtuale sia inclusa nella soluzione di avvio/arresto durante l'esecuzione.

* Ogni [manuali operativi](automation-solution-vm-management.md#runbooks) padre della soluzione ha un `VMList` parametro. È possibile passare un elenco delimitato da virgole di nomi di VM a questo parametro quando si pianificano i Runbook padre appropriati per la situazione e queste VM verranno incluse durante l'esecuzione della soluzione.

* Per selezionare più macchine virtuali, `External_Start_ResourceGroupNames` impostare `External_Stop_ResourceGroupNames` e con i nomi dei gruppi di risorse che contengono le VM che si vuole avviare o arrestare. È anche possibile impostare le variabili su un valore di `*` per eseguire la soluzione su tutti i gruppi di risorse nella sottoscrizione.

### <a name="exclude-a-vm"></a>Escludere una macchina virtuale

Per escludere una macchina virtuale dalla soluzione, è possibile aggiungerla alla `External_ExcludeVMNames` variabile. Questa variabile è un elenco delimitato da virgole di VM specifiche da escludere dalla soluzione Start/Stop. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco delimitato da virgole, le macchine virtuali che sono impostate per essere escluse potrebbero essere avviate o arrestate inavvertitamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificare le pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md). Per l'avvio e l'arresto di macchine virtuali devono essere definite due pianificazioni separate.

È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali a una data ora. In questo scenario è sufficiente creare una pianificazione di **arresto** e nessuna pianificazione **iniziale** corrispondente. A questo scopo, è necessario:

1. Assicurarsi di aver aggiunto i gruppi di risorse per l'arresto delle macchine virtuali nella `External_Stop_ResourceGroupNames` variabile.

2. Creare una pianificazione personalizzata per l'ora di arresto delle macchine virtuali.

3. Passare al runbook **ScheduledStartStop_Parent** e fare clic su **Pianificazione**. In questo modo è possibile selezionare la pianificazione creata nel passaggio precedente.

4. Selezionare **parametri ed eseguire le impostazioni** e impostare il campo dell' **azione** su **Interrompi**.

5. Selezionare **OK** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come risolvere i problemi di Avvio/Arresto di macchine virtuali durante gli orari di minore attività, vedere [risoluzione dei problemi di avvio/arresto di macchine virtuali](troubleshoot/start-stop-vm.md).

* [Esaminare](automation-solution-vm-management-logs.md) i record di automazione scritti nei log di monitoraggio di Azure e le query di ricerca nei log di esempio per analizzare lo stato dei processi del Runbook di automazione dalle macchine virtuali di avvio/arresto.
