---
title: Configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività
description: Questo articolo descrive come configurare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività per il supporto di diversi casi d'uso o scenari.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3fdb3b2b23d07b79a8e9979450bee653d646196c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182802"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Questo articolo descrive come configurare la funzionalità [Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md) per il supporto degli scenari descritti. È anche possibile apprendere come:

* [Configurare le notifiche di posta elettronica](#configure-email-notifications)
* [Aggiungere una macchina virtuale](#add-a-vm)
* [Escludere una macchina virtuale](#exclude-a-vm)
* [Modificare le pianificazioni di avvio e arresto](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: avviare/arrestare una macchina virtuale in base a una pianificazione

Questo scenario è la configurazione predefinita quando si distribuisce Avvio/Arresto di macchine virtuali durante gli orari di minore attività per la prima volta. È ad esempio possibile configurare la funzionalità per arrestare tutte le macchine virtuali in una sottoscrizione quando si lascia il luogo di lavoro la sera e avviarle al mattino quando si torna in ufficio. Quando si configurano le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM** durante la distribuzione, queste avviano e arrestano le macchine virtuali di destinazione. 

La configurazione della funzionalità per il solo arresto delle macchine virtuali è supportata. Per informazioni su come configurare una pianificazione personalizzata, vedere [Modificare le pianificazioni di avvio e arresto](#modify-the-startup-and-shutdown-schedules).

> [!NOTE]
> Il fuso orario usato dalla funzionalità è il fuso orario corrente quando si configura il parametro dell'orario di pianificazione. In Automazione di Azure viene tuttavia archiviato in formato UTC. Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione dei computer.

Per gestire le macchine virtuali nell'ambito, configurare le variabili: `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`.

È possibile consentire di specificare come destinazione dell'azione una sottoscrizione e un gruppo di risorse oppure un elenco specifico di macchine virtuali, ma non entrambi.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` per specificare le macchine virtuali di destinazione.

2. Abilitare e aggiornare le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Eseguire il runbook **ScheduledStartStop_Parent** con il campo del parametro **ACTION** impostato su **start** e il campo del parametro **WHATIF** impostato su True per visualizzare in anteprima le modifiche.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il **ScheduledStartStop_Parent** runbook con **azione** impostata su **Avvia**.

2. Aggiungere un elenco delimitato da virgole di macchine virtuali (senza spazi) nel campo del parametro **VMList** . Un elenco di esempio è `vm1,vm2,vm3` .

3. Impostare il campo del parametro **WHATIF** su true.

4. Configurare la `External_ExcludeVMNames` variabile con un elenco delimitato da virgole di macchine virtuali (VM1, VM2, VM3), senza spazi tra valori delimitati da virgole.

5. Questo scenario non rispetta le variabili `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupnames`. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificare un runbook in Automazione di Azure](shared-resources/schedules.md).

    > [!NOTE]
    > Il valore per **Target ResourceGroup Names** viene archiviato come valori per `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse. Per l'azione di avvio usare `External_Start_ResourceGroupNames` e usare `External_Stop_ResourceGroupNames` per l'azione di arresto. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: avviare/arrestare le macchine virtuali in sequenza usando i tag

In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Aggiungere un tag `sequencestart` e `sequencestop` con valori interi positivi alle macchine virtuali indicate nelle variabili `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`. Le azioni di avvio e arresto vengono eseguite in ordine crescente. Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/tag-portal.md) e [Assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/tag-cli.md).

2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.

3. Eseguire il runbook **SequencedStartStop_Parent** con **ACTION** impostato su **start** e **WHATIF** impostato su True per vedere in anteprima le modifiche.

4. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, è possibile eseguire manualmente il runbook con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente in base alla pianificazione prestabilita.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Specificare la destinazione delle azioni di avvio e arresto in base a un elenco di macchine virtuali

1. Aggiungere un tag `sequencestart` e `sequencestop` con valori interi positivi alle macchine virtuali che si prevede di aggiungere al parametro `VMList`.

2. Eseguire il **SequencedStartStop_Parent** runbook con **azione** impostata su **Avvia**.

3. Aggiungere un elenco delimitato da virgole di macchine virtuali (senza spazi) nel campo del parametro **VMList** . Un elenco di esempio è `vm1,vm2,vm3` .

4. Impostare **WHATIF** su true. 

5. Configurare la `External_ExcludeVMNames` variabile con un elenco delimitato da virgole di macchine virtuali, senza spazi tra valori delimitati da virgole.

6. Questo scenario non rispetta le variabili `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupnames`. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificare un runbook in Automazione di Azure](shared-resources/schedules.md).

7. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, eseguire manualmente **monitoring-and-diagnostics/monitoring-action-groupsrunbook** con il parametro impostato su **False**. In alternativa, consentire l'esecuzione automatica delle pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** di Automazione in base alla pianificazione prestabilita.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: automatizzare l'avvio o l'arresto in base all'utilizzo della CPU

Avvio/Arresto di macchine virtuali durante gli orari di minore attività può contribuire a gestire il costo dell'esecuzione di macchine virtuali Azure Resource Manager e classiche nella sottoscrizione, valutando le macchine che non vengono usate durante i periodi non di punta, ad esempio dopo l'orario di lavoro, e arrestandole automaticamente se l'utilizzo del processore è inferiore a una percentuale specificata.

Per impostazione predefinita, la funzionalità è preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%. Questo scenario è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

È possibile abilitare e indirizzare l'azione a una sottoscrizione e a un gruppo di risorse oppure usare un elenco specifico di macchine virtuali.

Quando si esegue **AutoStop_CreateAlert_Parent**, questo runbook verifica che esistano la sottoscrizione, i gruppi di risorse e le macchine virtuali di destinazione. Se le macchine virtuali esistono, il runbook chiama il runbook **AutoStop_CreateAlert_Child** per ogni macchina virtuale verificata dal runbook padre. Questo runbook figlio:

* Crea una regola di avviso per la metrica per ogni macchina virtuale verificata.
* Attiva il runbook **AutoStop_VM_Child** per una determinata macchina virtuale se la CPU scende al di sotto della soglia configurata per l'intervallo di tempo specificato. 
* Tenta di arrestare la macchina virtuale.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Indirizzare l'azione autostop a tutte le macchine virtuali in una sottoscrizione

1. Verificare che la variabile `External_Stop_ResourceGroupNames` sia vuota o impostata su * (carattere jolly).

2. [Facoltativo] Se si vogliono escludere alcune macchine virtuali dall'azione autostop, è possibile aggiungere un elenco delimitato da virgole di nomi di VM alla variabile `External_ExcludeVMNames`.

3. Abilitare la pianificazione dell'esecuzione di **Schedule_AutoStop_CreateAlert_Parent** per creare le necessarie regole di avviso per le metriche di arresto delle VM per tutte le macchine virtuali nella sottoscrizione. L'esecuzione di questo tipo di pianificazione consente di creare nuove regole di avviso per le metriche quando vengono aggiunte nuove macchine virtuali alla sottoscrizione.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Indirizzare l'azione autostop verso tutte le macchine virtuali in un gruppo di risorse o in più gruppi di risorse

1. Aggiungere un elenco delimitato da virgole di nomi di gruppi di risorse alla variabile `External_Stop_ResourceGroupNames`.

2. Se si vogliono escludere alcune macchine virtuali dall'azione autostop, è possibile aggiungere un elenco delimitato da virgole di nomi di VM alla variabile `External_ExcludeVMNames`.

3. Abilitare la pianificazione dell'esecuzione di **Schedule_AutoStop_CreateAlert_Parent** per creare le necessarie regole di avviso per le metriche di arresto delle VM per tutte le macchine virtuali nei gruppi di risorse. L'esecuzione di questa operazione in base a una pianificazione consente di creare nuove regole di avviso per le metriche quando nuove VM vengono aggiunte ai gruppi di risorse.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Indirizzare l'azione autostop verso un elenco di macchine virtuali

1. Creare una nuova [pianificazione](shared-resources/schedules.md#create-a-schedule) e collegarla al runbook **AutoStop_CreateAlert_Parent** aggiungendo un elenco delimitato da virgole di nomi di VM al parametro `VMList`.

2. Facoltativamente, se si vuole escludere alcune macchine virtuali dall'azione di arresto, è possibile aggiungere alla variabile un elenco delimitato da virgole di nomi di VM (senza spazi) `External_ExcludeVMNames` .

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche tramite posta elettronica dopo la distribuzione di Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è possibile modificare il gruppo di azioni creato durante la distribuzione.  

> [!NOTE]
> Le sottoscrizioni nel cloud di Azure per enti pubblici non supportano la funzionalità di posta elettronica di questa soluzione.

1. Nel portale di Azure passare a **Monitoraggio**, **Gruppi di azioni**. Selezionare il gruppo di azioni denominato **StartStop_VM_Notication**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Screenshot della pagina Monitoraggio-gruppi di azione.":::

2. Nella pagina StartStop_VM_Notification fare clic su **Modifica dettagli** in **Dettagli**. Si aprirà la pagina Email/SMS/Push/Voice (Posta elettronica/SMS/Push/Voce). Aggiornare l'indirizzo di posta elettronica e fare clic su **OK** per salvare le modifiche.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Screenshot della pagina posta elettronica/SMS/push/Voice che mostra un esempio di indirizzo di posta elettronica aggiornato.":::

    In alternativa è possibile aggiungere azioni aggiuntive al gruppo di azione, per altre informazioni sui gruppi di azioni, vedere [gruppi di azioni](../azure-monitor/platform/action-groups.md)

Di seguito è riportato un messaggio di posta elettronica di esempio che viene inviato quando la funzionalità arresta macchine virtuali.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Screenshot di un messaggio di posta elettronica di esempio inviato quando la funzionalità arresta le macchine virtuali.":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Aggiungere o escludere macchine virtuali

La funzionalità consente di aggiungere macchine virtuali da includere o escludere. 

### <a name="add-a-vm"></a>Aggiungere una macchina virtuale

Esistono due modi per assicurarsi che una macchina virtuale sia inclusa quando viene eseguita la funzionalità:

* Ogni [runbook](automation-solution-vm-management.md#runbooks) padre della funzionalità ha un parametro `VMList`. È possibile passare un elenco delimitato da virgole di nomi di VM (senza spazi) a questo parametro quando si pianificano i Runbook padre appropriati per la situazione specifica e queste macchine virtuali verranno incluse durante l'esecuzione della funzionalità.

* Per selezionare più macchine virtuali, impostare `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` con i nomi dei gruppi di risorse che contengono le macchine virtuali che si vogliono avviare o arrestare. È anche possibile impostare le variabili su un valore di `*` per eseguire la funzionalità su tutti i gruppi di risorse nella sottoscrizione.

### <a name="exclude-a-vm"></a>Escludere una macchina virtuale

Per escludere una VM da Avvio/Arresto di macchine virtuali durante gli orari di minore attività, è possibile aggiungerne il nome alla variabile `External_ExcludeVMNames`. Questa variabile è un elenco delimitato da virgole di VM specifiche (senza spazi) da escludere dalla funzionalità. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco, le macchine virtuali configurate per l'esclusione potrebbero essere inavvertitamente avviate o arrestate.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificare le pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa funzionalità viene seguita la stessa procedura illustrata in [Pianificare un runbook in Automazione di Azure](shared-resources/schedules.md). Sono necessarie pianificazioni separate per avviare e arrestare le macchine virtuali.

È possibile configurare la funzionalità per eseguire solamente l'arresto delle macchine virtuali a una data ora. In questo scenario è sufficiente definire una pianificazione di arresto, senza la pianificazione di avvio corrispondente. 

1. Assicurarsi di aver aggiunto i gruppi di risorse per l'arresto delle macchine virtuali nella variabile `External_Stop_ResourceGroupNames`.

2. Creare una pianificazione personalizzata per l'ora di arresto delle macchine virtuali.

3. Passare al runbook **ScheduledStartStop_Parent** e fare clic su **Pianificazione**. In questo modo è possibile selezionare la pianificazione creata nel passaggio precedente.

4. Selezionare **Parametri e impostazioni di esecuzione** e impostare il campo **ACTION** su **Stop**.

5. Selezionare **OK** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per monitorare la funzionalità durante l'operatività, vedere [Eseguire query sui log da Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-logs.md).
* Per risolvere i problemi durante la gestione delle macchine virtuali, vedere [Risolvere i problemi relativi ad Avvio/Arresto di macchine virtuali durante gli orari di minore attività](troubleshoot/start-stop-vm.md).