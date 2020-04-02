---
title: Configurare la soluzione Start/Stop VMs
description: In questo articolo viene descritto come configurare la soluzione Start/Stop VM durante le ore non orari per supportare diversi casi d'uso o scenari.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550383"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Come configurare la soluzione Start/Stop VMs durante le ore non lavorative

Con la soluzione Start/Stop VMs durante le ore non lavorative, è possibile:With the Start/Stop VMs during off hours solution, you can:

- [Pianificare l'avvio e l'arresto delle macchine virtuali.](#schedule)
- Pianificare l'avvio e l'arresto delle macchine virtuali in ordine crescente [usando i tag](#tags) di Azure (non supportati per le macchine virtuali classiche).
- Arrestare automaticamente le macchine virtuali in base a [un utilizzo ridotto della CPU.](#cpuutil)

In questo articolo viene descritto come configurare correttamente la soluzione per supportare questi scenari. È inoltre possibile apprendere come eseguire altre impostazioni di configurazione comuni per la soluzione, ad esempio:You can also learn how to perform other common configuration settings for the solution, such as:

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

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.

2. Abilitare e aggiornare le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Eseguire il runbook **ScheduledStartStop_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il runbook **ScheduledStartStop_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.

2. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).

3. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

    > [!NOTE]
    > Il valore per **Target ResourceGroup Names** (Nomi dei gruppi di risorse di destinazione) viene archiviato come valore sia per **External_Start_ResourceGroupNames** che per **External_Stop_ResourceGroupNames**. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse. Per l'azione di avvio, usare **External_Start_ResourceGroupNames** e, per l'azione di arresto, usare **External_Stop_ResourceGroupNames**. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: avviare/arrestare le macchine virtuali in sequenza usando i tag

In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine. A tale scopo, seguire questa procedura:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Aggiungere un **sequencestart** e un tag **sequencestop** con un valore intero positivo alle macchine virtuali destinate a **variabili External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames.** Le azioni di avvio e arresto vengono eseguite in ordine crescente. Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).

2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.

3. Eseguire il runbook **SequencedStartStop_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

4. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, è possibile eseguire manualmente il runbook con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Aggiungere un tag **sequencestart** e un tag **sequencestop** con un valore intero positivo alle macchine virtuali che si intende aggiungere al parametro **VMList**.

2. Eseguire il runbook **SequencedStartStop_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.

3. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).

4. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

5. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, è possibile eseguire manualmente il runbook monitoring-and-diagnostics/monitoring-action-groups con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatizzare l'avvio/arresto in base all'utilizzo della CPU

Questa soluzione consente di gestire il costo dell'esecuzione di Azure Resource Manager e delle macchine virtuali classiche nella sottoscrizione valutando le macchine virtuali non usate durante i periodi non di punta, ad esempio dopo l'orario di ufficio, e arrestandole automaticamente se l'utilizzo del processore è inferiore a una percentuale specificata.

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%. Questo scenario è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

È possibile abilitare e impostare come destinazione l'azione su una sottoscrizione e un gruppo di risorse oppure scegliere come destinazione un elenco specifico di macchine virtuali.

Quando si esegue il runbook **AutoStop_CreateAlert_Parent,** viene verificato che la sottoscrizione di destinazione, i gruppi di risorse e le macchine virtuali esistano. Se le macchine virtuali esistono, chiama quindi il runbook **AutoStop_CreateAlert_Child** per ogni macchina virtuale verificata dal runbook padre. Questo runbook figlio esegue le operazioni seguenti:This child runbook performs the following:

* Crea una regola di avviso metrica per ogni macchina virtuale verificata.

* Attiva il **runbook AutoStop_VM_Child** per una determinata macchina virtuale nel caso in cui la CPU scappi al di sotto della soglia configurata per l'intervallo di tempo specificato. Questo runbook tenta quindi di arrestare la macchina virtuale.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Per impostare come destinazione l'azione di arresto automatico per tutte le macchine virtuali in una sottoscrizioneTo target the auto stop action against all VMs in a subscription

1. Assicurarsi che la variabile **External_Stop_ResourceGroupNames** sia vuota o impostata su (carattere jolly).

2. [Passaggio facoltativo] Se si desidera escludere alcune macchine virtuali dall'arresto automatico, è possibile aggiungere un elenco separato da virgole di nomi di macchine virtuali alla variabile **External_ExcludeVMNames.**

3. Abilitare la **pianificazione del Schedule_AutoStop_CreateAlert_Parent** per l'esecuzione per creare le regole di avviso della metrica di arresto della macchina virtuale necessarie per tutte le macchine virtuali nella sottoscrizione. L'esecuzione di questo metodo in base a una pianificazione consentirà di creare nuove regole di avviso di metrica man mano che vengono aggiunte nuove macchine virtuali alla sottoscrizione.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>Per impostare come destinazione l'azione di arresto automatico su tutte le macchine virtuali in un gruppo di risorse o più gruppi di risorseTo target the auto stop action against all VMs in a resource group/multiple resource groups

1. Aggiungere un elenco separato comune di nomi di gruppi di risorse alla variabile **External_Stop_ResourceGroupNames.**

2. Facoltativamente, se si desidera escludere alcune delle macchine virtuali dall'arresto automatico, è possibile aggiungere un elenco separato da virgole di nomi di macchine virtuali alla variabile **External_ExcludeVMNames.**

3. Abilitare la **pianificazione Schedule_AutoStop_CreateAlert_Parent** per l'esecuzione per creare le regole di avviso della **metrica di arresto della macchina virtuale** necessarie per tutte le macchine virtuali nei gruppi di risorse. L'esecuzione di questa funzionalità in base a una pianificazione consente di creare nuove regole di avviso di metrica man mano che vengono aggiunte nuove macchine virtuali ai gruppi di risorse.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>To Target the auto stop action to a list of VMs

1. Creare una nuova pianificazione e collegarla al runbook **AutoStop_CreateAlert_Parent,** aggiungendo un elenco separato da virgole di nomi di macchine virtuali al parametro **VMList.Create** a new [Schedule](shared-resources/schedules.md#creating-a-schedule) and link it to the AutoStop_CreateAlert_Parent runbook, adding a comma separated list of VM names to the VMList parameter.

2. Facoltativamente, se si desidera escludere alcune macchine virtuali dall'arresto automatico, è possibile aggiungere un elenco separato da virgole di nomi di macchine virtuali alla variabile **External_ExcludeVMNames.**

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche di posta elettronica dopo la distribuzione della soluzione, modificare il gruppo di azioni che è stato creato durante la distribuzione.  

> [!NOTE]
> Le sottoscrizioni nel cloud di Azure per enti pubblici non supportano la funzionalità di posta elettronica di questa soluzione.

1. Nel portale di Azure, passare a monitoraggio -> gruppi di azioni. Selezionare il gruppo di azione intitolato **StartStop_VM_Notication**.

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

* Ogni [runbook](automation-solution-vm-management.md#runbooks) padre della soluzione dispone di un parametro **VMList.** È possibile passare un elenco separato da virgole di nomi di macchine virtuali a questo parametro quando si pianifica il runbook padre appropriato per la situazione e queste macchine virtuali verranno incluse durante l'esecuzione della soluzione.

* Per selezionare più macchine virtuali, impostare **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** con i nomi dei gruppi di risorse che contengono le macchine virtuali da avviare o arrestare. È anche possibile impostare questo valore su `*` per fare in modo che la soluzione venga applicata a tutti i gruppi di risorse nella sottoscrizione.

### <a name="exclude-a-vm"></a>Escludere una macchina virtuale

Per escludere una macchina virtuale dalla soluzione, è possibile aggiungerla alla variabile **External_ExcludeVMNames**. Questa variabile è un elenco separato da virgole di macchine virtuali specifiche da escludere dalla soluzione Start/Stop. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco separato da virgole, le macchine virtuali che sono impostate per l'esclusione possono essere avviate o arrestate inavvertitamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificare le pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md). Per l'avvio e l'arresto di macchine virtuali devono essere definite due pianificazioni separate.

È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali a una data ora. In questo scenario è sufficiente definire una pianificazione di **arresto**, senza la pianificazione di **avvio** corrispondente. A questo scopo, è necessario:

1. Verificare di aver aggiunto i gruppi di risorse per le macchine virtuali da arrestare nella variabile **External_Stop_ResourceGroupNames**.

2. Creare una pianificazione personalizzata per l'ora di arresto delle macchine virtuali.

3. Passare al runbook **ScheduledStartStop_Parent** e fare clic su **Pianificazione**. In questo modo è possibile selezionare la pianificazione creata nel passaggio precedente.

4. Selezionare **Parametri ed eseguire le impostazioni** e impostare il parametro **ACTION** su **Stop**.

5. Selezionare **OK** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come risolvere i problemi relativi all'avvio/arresto delle macchine virtuali durante le ore non lavorative, vedere [Risoluzione dei problemi relativi all'avvio/arresto](troubleshoot/start-stop-vm.md)delle macchine virtuali.

* [Esaminare](automation-solution-vm-management-logs.md) i record di automazione scritti in Log di monitoraggio di Azure e le query di ricerca log di esempio per analizzare lo stato dei processi di runbook di automazione da Start/Stop VMs.
