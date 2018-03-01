---
title: Creare e personalizzare un piano di ripristino per il failover e il ripristino in Azure Site Recovery | Microsoft Docs
description: Informazioni su come creare e personalizzare piani di ripristino in Azure Site Recovery. Questo articolo illustra come effettuare il failover e il ripristino di macchine virtuali e server fisici.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>Creare un piano di ripristino con Site Recovery

Questo articolo illustra come creare e personalizzare un piano di ripristino in [Azure Site Recovery](site-recovery-overview.md).

Creare un piano di ripristino per eseguire le operazioni seguenti:

* Definire gruppi di computer che effettuano il failover insieme e successivamente si avviano contemporaneamente.
* Creare le dipendenze tra i computer raggruppandoli insieme in un gruppo di piano di ripristino. Per effettuare, ad esempio, il failover di un'applicazione specifica e renderla nuovamente disponibile, raggruppare tutte le macchine virtuali per l'applicazione nello stesso gruppo del piano di ripristino.
* Eseguire un failover. È possibile eseguire un failover non pianificato o un test, pianificato, in un piano di ripristino.

## <a name="why-use-a-recovery-plan"></a>Vantaggi dell'utilizzo di un piano di ripristino

I piani di ripristino consentono di pianificare un processo di ripristino sistematico creando piccole unità indipendenti più facili da gestire. Queste unità rappresentano in genere un'applicazione nell'ambiente in uso. Un piano di ripristino aiuta a definire la sequenza in cui avviare le macchine virtuali. Consente anche di automatizzare attività comunemente eseguite durante il ripristino.

> [!TIP]
> Un modo per controllare se si è pronti a eseguire la migrazione nel cloud o un ripristino di emergenza è di assicurarsi che ognuna delle proprie applicazioni sia inclusa in un piano di ripristino. Occorre inoltre assicurarsi che ogni piano di ripristino sia testato per il ripristino in Azure. Con questa preparazione, è possibile eseguire in tutta sicurezza la migrazione o il failover del data center completo in Microsoft Azure.
 
Un piano di ripristino presenta tre proposte di valore chiave:

* Modellare un'applicazione per acquisire le dipendenze
* Automatizzare la maggior parte delle attività di ripristino per ridurre l'obiettivo del tempo di ripristino
* Testare il failover per essere pronti per una situazione di emergenza.

### <a name="model-an-application-to-capture-dependencies"></a>Modellare un'applicazione per acquisire le dipendenze

Un piano di ripristino è costituito da un gruppo di macchine virtuali, comprendenti in genere un'applicazione, che effettuano insieme il failover. Usando i costrutti del piano di ripristino, è possibile migliorare un gruppo di piano di ripristino per acquisire le proprietà specifiche dell'applicazione. 

In questo articolo viene usata una tipica applicazione a tre livelli, con un back-end SQL, del middleware e un front-end Web. È possibile personalizzare il piano di ripristino per assicurarsi che, dopo il failover, le macchine virtuali si avviino nell'ordine corretto. Il back-end SQL deve essere avviato per primo, seguito dal middleware e successivamente dal front-end Web. Tale ordine assicura che l'applicazione sia operativa nel momento in cui viene avviata l'ultima macchina virtuale. 

All'avvio, il middleware ad esempio tenta di connettersi al livello SQL. Il piano di ripristino assicura che il livello SQL sia già in esecuzione. L'avvio del server front-end come ultimo passaggio assicura anche che gli utenti finali non si connettano all'URL dell'applicazione prima che tutti i componenti siano operativi e l'applicazione sia pronta ad accettare richieste. Per creare queste dipendenze, personalizzare il piano di ripristino per aggiungere gruppi e selezionare una macchina virtuale. Per spostare una macchina virtuale tra due gruppi, modificare il gruppo della macchina virtuale.

![Schermata di un piano di ripristino di esempio in Site Recovery](./media/site-recovery-create-recovery-plans/rp.png)

Dopo avere completato la personalizzazione, è possibile visualizzare i passaggi esatti del ripristino. Ecco l'ordine dei passaggi eseguiti durante il failover di un piano di ripristino:

1. Un passaggio di arresto tenta di spegnere le macchine virtuali locali (eccetto in un failover di test, durante il quale l'esecuzione del sito primario deve continuare).
2. Il tentativo di arresto attiva il failover parallelo di tutte le macchine virtuali incluse nel piano di ripristino. Il passaggio di failover prepara i dischi delle macchine virtuali usando i dati replicati.
3. I gruppi di avvio vengono eseguiti nell'ordine stabilito e avviano le macchine virtuali in ogni gruppo. Viene eseguito per primo il Gruppo 1, quindi viene eseguito il Gruppo 2 e infine il Gruppo 3. Se in un gruppo sono presenti più macchine virtuali (ad esempio, un front-end Web con bilanciamento del carico), queste vengono avviate in parallelo.

> [!TIP]
> La sequenziazione tra gruppi assicura che le dipendenze tra i vari livelli di applicazione siano rispettate. Il parallelismo, laddove appropriato, migliora l'obiettivo RTO di ripristino dell'applicazione.

   > [!NOTE]
   > I computer che fanno parte di un unico gruppo effettuano il failover in parallelo. I computer che fanno parte di gruppi diversi effettuano il failover in base all'ordine dei gruppi. Il failover dei computer del Gruppo 1 verrà effettuato solo dopo il failover e l'avvio di tutti i computer del Gruppo 2.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatizzare la maggior parte delle attività di ripristino per ridurre l'obiettivo del tempo di ripristino

Il ripristino di applicazioni di grandi dimensioni può essere un'attività complessa. Dover ricordare molti passaggi manuali in situazioni di emergenza è difficile e aumenta il rischio di errori. È inoltre possibile che ad attivare il failover sia un utente che non è a conoscenza degli aspetti complessi dell'applicazione. 

Il piano di ripristino consente di automatizzare le azioni che è necessario eseguire a ogni passaggio. È possibile impostare le azioni necessarie usando i runbook di Automazione di Azure. Con i runbook è possibile automatizzare le attività di ripristino comuni, come le attività negli esempi riportati di seguito. Per le attività che non possono essere automatizzate, è possibile inserire azioni manuali nei piani di ripristino.

* **Attività sulla macchina virtuale di Azure dopo il failover**: queste attività sono in genere necessarie per potersi connettere alla macchina virtuale. Esempi:
    * Creare un indirizzo IP pubblico nella macchina virtuale dopo il failover.
    * Assegnare un gruppo di sicurezza di rete alla scheda di rete nella macchina virtuale sottoposta a failover.
    * Aggiungere un servizio di bilanciamento del carico a un set di disponibilità.
* **Attività all'interno della macchina virtuale dopo il failover**: queste attività riconfigurano l'applicazione in modo che continui a funzionare correttamente nel nuovo ambiente. Esempi:
    * Modificare la stringa di connessione del database all'interno della macchina virtuale.
    * Modificare la configurazione o le regole del server Web.

> [!TIP]
> Con un piano di ripristino completo che automatizza le attività dopo il ripristino usando i runbook di Automazione, è possibile effettuare il failover con un clic e ottimizzare l'obiettivo RTO.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Testare il failover per essere pronti per una situazione di emergenza

Un piano di ripristino può essere usato per attivare un failover o un failover di test. È sempre consigliabile completare un failover di test sull'applicazione prima di effettuare un failover vero e proprio. Il failover di test aiuta a controllare se l'applicazione diventa disponibile nel sito di ripristino. Se manca qualcosa nella configurazione, è sufficiente eseguire una pulizia e ripetere il failover di test. Effettuare il failover di test più volte fino a quando non si è certi che l'applicazione viene ripristinata senza problemi.

![Schermata di un piano di ripristino di esempio di test in Site Recovery](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Poiché ogni applicazione è diversa, per ognuna è necessario creare un piano di ripristino personalizzato. 
>
> Nel moderno ambiente dinamico incentrato sui data center, inoltre, le applicazioni e le relative dipendenze cambiano molto spesso. Per assicurarsi che il piano di ripristino sia sempre attuale, occorre effettuare un failover di test delle applicazioni ogni trimestre.

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Fare clic su **Piani di ripristino** > **Crea piano di ripristino**.
   Specificare un nome per il piano di ripristino, nonché per il server di origine e di destinazione Nella posizione di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Scegliere un'origine e una destinazione in base alle macchine virtuali che si vuole inserire nel piano di ripristino. 

   |Scenario                   |Sorgente               |Destinazione           |
   |---------------------------|---------------------|-----------------|
   |Da Azure ad Azure             |Area di Azure         |Area di Azure     |
   |Da VMware ad Azure            |Server di configurazione |Azure            |
   |Da Virtual Machine Manager (VMM) ad Azure               |Nome visualizzato di VMM    |Azure            |
   |Da sito Hyper-V ad Azure      |Nome del sito Hyper-V    |Azure            |
   |Da computer fisici ad Azure |Server di configurazione |Azure            |
   |Da VMM a VMM                 |Nome descrittivo VMM    |Nome visualizzato di VMM|

   > [!NOTE]
   > Un piano di ripristino può contenere macchine virtuali che hanno la stessa origine e la stessa destinazione. Le macchine virtuali VMware e System Center Virtual Machine Manager (VMM) non possono essere incluse nello stesso piano di ripristino. È invece possibile aggiungere macchine virtuali VMware e computer fisici nello stesso piano di ripristino. L'origine per entrambi i computer è in questo caso un server di configurazione.

2. In **Seleziona macchine virtuali** selezionare le macchine virtuali (o il gruppo di replica) che si vuole aggiungere al gruppo predefinito (Gruppo 1) nel piano di ripristino. È possibile selezionare solo le macchine virtuali che erano protette nell'origine (selezionate nel piano di ripristino) e che sono protette nella destinazione (selezionate nel piano di ripristino).

## <a name="customize-and-extend-recovery-plans"></a>Personalizzare ed estendere i piani di ripristino

Per personalizzare ed estendere i piani di ripristino, andare al riquadro delle risorse del piano di ripristino di Site Recovery. Selezionare la scheda **Personalizza**. È possibile personalizzare ed estendere i piani di ripristino in base alle opzioni seguenti:

- **Aggiungere nuovi gruppi**: è possibile aggiungere nel piano di ripristino fino a sette gruppi oltre a quello predefinito. Si può successivamente aggiungere altri computer o gruppi di replica a questi nuovi gruppi. I gruppi vengono numerati nell'ordine in cui vengono aggiunti. È possibile includere una macchina virtuale o un gruppo di replica solo in un gruppo del piano di ripristino.
- **Aggiungere un'azione manuale**: è possibile aggiungere azioni manuali da eseguire prima o dopo un gruppo del piano di ripristino. Quando viene eseguito il piano di ripristino, questo si interrompe in corrispondenza del punto in cui è stata inserita l'azione manuale. Una finestra di dialogo richiede all'utente di specificare che l'azione manuale è stata completata.
- **Aggiungere uno script**: è possibile aggiungere script che vengono eseguiti prima o dopo un gruppo del piano di ripristino. Quando si aggiunge uno script, viene aggiunto un nuovo set di azioni per il gruppo. Viene ad esempio, creato un set di passaggi preliminari per il Gruppo 1 denominato *Gruppo 1: passaggi preliminari*. Tutti i passaggi preliminari vengono elencati in questo set. È possibile aggiungere uno script nel sito primario solo se è stato distribuito un server VMM. Per altre informazioni, vedere [Add a VMM script to a recovery plan](site-recovery-how-to-add-vmmscript.md) (Aggiungere uno script VMM a un piano di ripristino).
- **Aggiungere runbook di Azure**: è possibile estendere i piani di ripristino usando i runbook di Azure. È ad esempio possibile usare un runbook per automatizzare attività o per creare un ripristino che richiede un solo passaggio. Per altre informazioni, vedere [Add Azure Automation runbooks to recovery plans](site-recovery-runbook-automation.md) (Aggiungere runbooks di Automazione di Azure a piani di ripristino).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Aggiungere uno script, un runbook o un'azione manuale a un piano

Dopo aver aggiunto macchine virtuali o gruppi di replica a un gruppo del piano di ripristino predefinito, è possibile aggiungervi uno script o un'azione manuale.

1. Aprire il piano di ripristino.
2. Nell'elenco **Passaggio** selezionare una voce. Selezionare quindi **Script** o **Azione manuale**.
3. Specificare se si vuole aggiungere lo script o l'azione prima o dopo la voce selezionata. Per spostare lo script verso l'alto o verso il basso nell'elenco, usare i pulsanti **Sposta su** e **Sposta giù**.
4. Se si aggiunge uno script VMM, selezionare **Failover to VMM script** (Failover nello script VMM). In **Percorso script** immettere il percorso relativo della condivisione, ad esempio **\RPScripts\RPScript.PS1**.
5. Se si aggiunge un runbook di Automazione, specificare l'account di Automazione in cui si trova il runbook. Selezionare quindi lo script di runbook di Azure.
6. Per verificare che lo script funzioni come previsto, effettuare un failover del piano di ripristino.

Le opzioni di script o runbook sono disponibili solo negli scenari seguenti e quando si effettua un failover o un failback. Un'azione manuale è disponibile sia per il failover che per il failback.


|Scenario               |Failover |Failback |
|-----------------------|---------|---------|
|Da Azure ad Azure         |Runbook |Runbook  |
|Da VMware ad Azure        |Runbook |ND       | 
|Da VMM ad Azure           |Runbook |Script   |
|Da sito Hyper-V ad Azure  |Runbook |ND       |
|Da VMM a VMM             |Script   |Script   |


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'[esecuzione di failover](site-recovery-failover.md).  
* Per vedere il piano di ripristino in azione, guardare questo video:
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
