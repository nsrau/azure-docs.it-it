---
title: Creare e personalizzare piani per il ripristino di emergenza con Azure Site Recovery | Microsoft Docs
description: Informazioni su come creare e personalizzare piani per il ripristino di emergenza usando il servizio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: d52aa3b39a17c42c0f0e0cb669c69d336b41ba48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035823"
---
# <a name="create-and-customize-recovery-plans"></a>Creare e personalizzare piani di ripristino

Questo articolo illustra come creare e personalizzare un piano di ripristino in [Azure Site Recovery](site-recovery-overview.md). Prima di iniziare, vedere [altre informazioni](recovery-plan-overview.md) sui piani di ripristino.

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Nell'insieme di credenziali Servizi di ripristino selezionare **Piani di ripristino (Site Recovery)** > **+Piano di ripristino**.
2. In **Crea piano di ripristino** specificare un nome per il piano.
3. Scegliere un'origine e una destinazione in base ai computer nel piano e selezionare **Resource Manager** per il modello di distribuzione. Nella posizione di origine devono essere presenti computer abilitati per il failover e il ripristino. 

   **Failover** | **Origine** | **Destinazione** 
   --- | --- | ---
   Da Azure ad Azure | Area di Azure |Area di Azure
   Da VMware ad Azure | Server di configurazione | Azure
   Da computer fisici ad Azure | Server di configurazione | Azure   
   Da Hyper-V gestito da VMM ad Azure  | Nome visualizzato di VMM | Azure
   Da Hyper-V senza VMM ad Azure | Nome del sito Hyper-V | Azure
   Da VMM a VMM |Nome descrittivo VMM | Nome visualizzato di VMM 

   > [!NOTE]
   > Un piano di ripristino può contenere computer con la stessa origine e la stessa destinazione. Macchine virtuali VMware e Hyper-V gestite da VMM non possono trovarsi nello stesso piano. Macchine virtuali VMware e server fisici possono trovarsi nello stesso piano se l'origine è un server di configurazione.

2. In **Select items virtual machines** (Seleziona macchine virtuali elementi) selezionare i computer (o il gruppo di replica) da aggiungere al piano. Fare quindi clic su **OK**.
    - I computer vengono aggiunti al gruppo predefinito (Gruppo 1) nel piano. Dopo il failover, tutti i computer di questo gruppo vengono avviati contemporaneamente.
    - È possibile selezionare solo computer che si trovano nelle posizioni di origine e destinazione specificate. 
1. Fare clic su **OK** per creare il piano.

## <a name="add-a-group-to-a-plan"></a>Aggiungere un gruppo a un piano

È possibile creare gruppi aggiuntivi e aggiungere computer a diversi gruppi, in modo che sia possibile specificare un comportamento diverso per ogni gruppo. È ad esempio possibile indicare quando i computer di un gruppo devono avviarsi dopo il failover o specificare azioni personalizzate per ogni gruppo.

1. In **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano > **Personalizza**. Per impostazione predefinita, dopo la creazione di un piano, tutti i computer aggiunti a un piano dopo la creazione di questo vengono inseriti nel Gruppo 1 predefinito.
2. Fare clic su **+Gruppo**. Per impostazione predefinita, a un nuovo gruppo viene assegnato il numero di sequenza corrispondente nella procedura di creazione. È possibile avere fino a sette gruppi.
3. Selezionare il computer da spostare nel nuovo gruppo, fare clic su **Modifica gruppo**e quindi selezionare il nuovo gruppo. In alternativa, fare doppio clic sul nome del gruppo > **Elemento protetto** e aggiungere computer al gruppo. Un gruppo di computer o di replica può appartenere a un solo gruppo in un piano di ripristino.


## <a name="add-a-script-or-manual-action"></a>Aggiungere uno script o un'azione manuale

È possibile personalizzare un piano di ripristino tramite l'aggiunta di uno script o di un'azione manuale. Si noti che:

- Se si esegue la replica in Azure è possibile integrare i runbook di automazione di Azure nel piano di ripristino. [Altre informazioni](site-recovery-runbook-automation.md)
- Se si esegue la replica di macchine virtuali Hyper-V gestite da System Center VMM, è possibile creare uno script nel server VMM locale e includerlo nel piano di ripristino.
- Quando si aggiunge uno script, viene aggiunto un nuovo set di azioni per il gruppo. Viene ad esempio, creato un set di passaggi preliminari per il Gruppo 1 denominato *Gruppo 1: passaggi preliminari*. Tutti i passaggi preliminari vengono elencati in questo set. È possibile aggiungere uno script nel sito primario solo se è stato distribuito un server VMM.
- Se si aggiunge un'azione manuale, quando viene eseguito il piano di ripristino, questo si interrompe in corrispondenza del punto in cui è stata inserita l'azione manuale. Una finestra di dialogo richiede all'utente di specificare che l'azione manuale è stata completata.
- Per creare uno script nel server VMM, seguire le istruzioni in [questo articolo](hyper-v-vmm-recovery-script.md).
- Durante il failover gli script possono essere applicati nel sito secondario e durante il failback dal sito secondario al sito primario. Il supporto dipende dallo scenario di replica in uso:
    
    **Scenario** | **Failover** | **Failback**
    --- | --- | --- 
    Da Azure ad Azure  | Runbook | Runbook
    Da VMware ad Azure | Runbook | ND 
    Da Hyper-V con VMM ad Azure | Runbook | Script
    Da sito Hyper-V ad Azure | Runbook | ND
    Da VMM a VMM secondario | Script | Script

1. Nel piano di ripristino fare clic sul passaggio in cui deve essere aggiunta l'azione e specificare quando l'azione deve essere eseguita: a. Se si vuole che l'azione venga eseguita prima dell'avvio dei computer del gruppo dopo il failover, selezionare **Aggiungi pre-azione**.
    b. Se si vuole che l'azione venga eseguita dopo l'avvio dei computer del gruppo dopo il failover, selezionare **Aggiungi post-azione**. Per spostare l'azione verso l'alto o verso il basso, usare i pulsanti **Sposta su** e **Sposta giù**.
2. In **Inserisci azione** selezionare **Script** o **Azione manuale**.
3. Se si vuole aggiungere un'azione manuale, eseguire le operazioni seguenti" a. Digitare un nome per l'azione e le istruzioni per l'azione stessa. Queste istruzioni verranno visualizzate dalla persona che esegue il failover.
    b. Specificare se si vuole aggiungere l'azione manuale per tutti i tipi di failover (test, failover, failover pianificato), se pertinente. Fare quindi clic su **OK**.
4. Se si vuole aggiungere uno script, eseguire le operazioni seguenti: a. Se si aggiunge uno script VMM, selezionare l'opzione **Failover to VMM script** (Failover nello script VMM) e in **Percorso script** digitare il percorso relativo della condivisione. Se ad esempio la condivisione si trova in: \\<VMMServerName>\MSSCVMMLibrary\RPScripts, specificare il percorso \RPScripts\RPScript.PS1.
    b. Se si aggiunge un runbook di automazione di Azure, specificare l'**account di Automazione di Azure** in cui si trova il runbook e selezionare lo **script di runbook di Azure** appropriato.
5. Per verificare che lo script funzioni come previsto, eseguire un failover di test del piano di ripristino.

## <a name="watch-a-video"></a>Guardare un video

Ecco un video che illustra come creare un piano di ripristino.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[esecuzione dei failover](site-recovery-failover.md).  

    
