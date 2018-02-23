---
title: Creare e personalizzare piani di ripristino per il failover e il ripristino in Azure Site Recovery | Microsoft Docs
description: Descrive come creare e personalizzare i piani di ripristino in Azure Site Recovery, eseguire il failover e ripristinare le macchine virtuali e i server fisici
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>Creare piani di ripristino


L'articolo contiene informazioni sulla creazione e la personalizzazione dei piani di ripristino in [Azure Site Recovery](site-recovery-overview.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Per creare i piani di ripristino, procedere come segue:

* Definire gruppi di computer che eseguono il failover insieme e quindi si avviano contemporaneamente.
* Creare le dipendenze tra i computer raggruppandoli in un gruppo di piano di ripristino. Ad esempio, per eseguire il failover e visualizzare un'applicazione specifica, raggruppare tutte le macchine virtuali per l'applicazione nello stesso gruppo di piano di ripristino.
* Eseguire un failover. È possibile eseguire un failover non pianificato o un test, pianificato, in un piano di ripristino.

## <a name="why-use-recovery-plans"></a>Perché usare i piani di ripristino

I piani di ripristino consentono di pianificare un processo di ripristino sistematico creando piccole unità indipendenti che è possibile gestire. Queste unità rappresenteranno in genere un'applicazione nell'ambiente. I piani di ripristino non solo consentono di definire la sequenza di avvio delle macchine virtuali, ma anche di automatizzare le attività comuni durante il ripristino.


**Per controllare di essere preparati per la migrazione cloud o il ripristino di emergenza, è essenzialmente possibile assicurarsi che ogni applicazione faccia parte di un piano di ripristino e che di ogni piano di ripristino venga testato il ripristino in Microsoft Azure. Con questa preparazione, è possibile eseguire in tutta sicurezza la migrazione o il failover del data center completo in Microsoft Azure.**
 
Di seguito sono elencate le tre proposte di valore chiave di un piano di ripristino:

### <a name="model-an-application-to-capture-dependencies"></a>Modellare un'applicazione per acquisire le dipendenze

Un piano di ripristino è un gruppo di macchine virtuali, comprendenti in genere un'applicazione, che effettuano insieme il failover. Usando i costrutti del piano di ripristino, è possibile migliorare questo gruppo per acquisire le proprietà specifiche dell'applicazione.
 
Si consideri, ad esempio, una tipica applicazione a tre livelli con

* un back-end SQL
* un middleware
* un front-end Web

Il piano di ripristino può essere personalizzato per assicurarsi che le macchine virtuali diventino disponibili nell'ordine corretto dopo un failover. Per primo deve diventare disponibile il back-end SQL, quindi il middleware e infine il front-end Web. Tale ordine assicura che l'applicazione sia funzionante nel momento in cui l'ultima macchina virtuale diventa disponibile. Il middleware ad esempio, quando diventa disponibile, prova a connettersi al livello SQL e, in base al piano di ripristino, il livello SQL è già in esecuzione. Il fatto che i server front-end diventino disponibili per ultimi assicura anche che gli utenti finali non si connettano per errore all'URL dell'applicazione finché tutti i componenti non sono operativi e l'applicazione non è pronta ad accettare le richieste. Per compilare queste dipendenze, è possibile personalizzare il piano di ripristino per aggiungere altri gruppi. Selezionare una macchina virtuale e modificarne il gruppo per spostarla da un gruppo all'altro.

![Piano di ripristino di esempio](./media/site-recovery-create-recovery-plans/rp.png)

Dopo avere completato la personalizzazione, è possibile visualizzare il passaggio esatto del ripristino. Ecco l'ordine dei passaggi eseguiti durante il failover di un piano di ripristino:

* Il primo passaggio è un arresto che prova a disattivare le macchine virtuali in locale (tranne che nel failover di test in cui l'esecuzione del sito primario non deve essere interrotta)
* Viene quindi attivato il failover parallelo di tutte le macchine virtuali del piano di ripristino. Il passaggio di failover prepara i dischi delle macchine virtuali dai dati replicati.
* I gruppi di avvio vengono infine eseguiti in ordine, a partire dalle macchine virtuali di ogni gruppo: prima il gruppo 1, quindi il gruppo 2 e infine il gruppo 3. Se in un gruppo sono presenti più macchine virtuali (ad esempio, in un front-end Web con bilanciamento del carico), vengono avviate tutte in parallelo.

**La sequenza dei gruppi assicura che le dipendenze tra i diversi livelli applicazione vengano rispettate e il parallelismo, ove appropriato, migliori l'obiettivo del tempo di ripristino dell'applicazione.**

   > [!NOTE]
   > I computer che fanno parte di un unico gruppo effettueranno il failover in parallelo. I computer che fanno parte di gruppi diversi effettueranno il failover in base all'odine dei gruppi. Il failover dei computer del gruppo 2 verrà eseguito solo dopo il failover e l'avvio di tutti i computer del gruppo 1.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatizzare la maggior parte delle attività di ripristino per ridurre l'obiettivo del tempo di ripristino

Il ripristino di applicazioni di grandi dimensioni può essere un'attività complessa. È anche difficile ricordare i passaggi di personalizzazione esatti dopo il failover o la migrazione. A volte è qualcuno che non conosce la complessità dell'applicazione a dover attivare il failover. Ricordare troppi passaggi manuali in momenti di caos è difficile e può causare errori. Un piano di ripristino consente di automatizzare le azioni che è necessario eseguire in ogni passaggio, con i runbook di Automazione di Microsoft Azure. Con i runbook, è possibile automatizzare le comuni attività di ripristino come negli esempi riportati di seguito. Per le attività che non possono essere automatizzate, i piani di ripristino consentono anche di inserire azioni manuali.

* Attività sulla macchina virtuale di Azure dopo il failover: sono in genere necessarie per potersi connettere alla macchina virtuale, ad esempio:
    * Creare un IP pubblico nella macchina virtuale dopo il failover
    * Assegnare un gruppo di sicurezza di rete alla scheda di interfaccia di rete della macchina virtuale di cui è stato effettuato il failover
    * Aggiungere un servizio di bilanciamento del carico a un set di disponibilità
* Attività all'interno della macchina virtuale dopo il failover: riconfigurano l'applicazione in modo che continui a funzionare correttamente nel nuovo ambiente, ad esempio:
    * Modificare la stringa di connessione di database all'interno della macchina virtuale
    * Modifica la configurazione o le regole del server Web

**Con un piano di ripristino completo che automatizza le attività dopo il ripristino usando i runbook di automazione, è possibile effettuare il failover con un clic e ottimizzare l'obiettivo del tempo di ripristino.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Testare il failover per essere pronti per una situazione di emergenza

Un piano di ripristino può essere usato per attivare un failover o un failover di test. È sempre consigliabile completare un failover di test sull'applicazione prima di effettuare un failover. Il failover di test consente di controllare se l'applicazione diventerà disponibile nel sito di ripristino.  Se manca qualcosa, è possibile attivare facilmente una pulizia e ripetere il failover di test. Effettuare il failover di test più volte fino a quando non si è certi che l'applicazione viene ripristinata senza problemi.

![Piano di ripristino di test](./media/site-recovery-create-recovery-plans/rptest.png)

**Poiché ogni applicazione è diversa, per ognuna è necessario creare un piano di ripristino personalizzato. Inoltre, in questo mondo dinamico dei data center, le applicazioni e le dipendenze sono in continuo mutamento. Effettuare il failover di test dell'applicazione ogni trimestre per controllare che il piano di ripristino sia ancora valido.**

## <a name="how-to-create-a-recovery-plan"></a>Come creare un piano di ripristino

1. Fare clic su **Piani di ripristino** > **Crea piano di ripristino**.
   Specificare un nome per il piano di ripristino, nonché per il server di origine e di destinazione Nella posizione di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Scegliere un'origine e una destinazione in base alle macchine virtuali che si vuole inserire nel piano di ripristino. 

   |Scenario                   |Sorgente               |Destinazione           |
   |---------------------------|---------------------|-----------------|
   |Da Azure ad Azure             |Area di Azure         |Area di Azure     |
   |Da VMware ad Azure            |Server di configurazione |Azure            |
   |Da VMM ad Azure               |Nome descrittivo VMM    |Azure            |
   |Da sito Hyper-V ad Azure      |Nome del sito Hyper-V    |Azure            |
   |Da computer fisici ad Azure |Server di configurazione |Azure            |
   |Da VMM a VMM                 |Nome descrittivo VMM    |Nome descrittivo VMM|

   > [!NOTE]
   > Un piano di ripristino può contenere macchine virtuali che hanno la stessa origine e la stessa destinazione. Le macchine virtuali di VMware e VMM non possono far parte dello stesso piano di ripristino. Le macchine virtuali VMware e i computer fisici possono tuttavia essere aggiunti allo stesso piano perché l'origine per entrambi è un server di configurazione.

2. In **Seleziona macchine virtuali** selezionare le macchine virtuali (o il gruppo di replica) che si vuole aggiungere al gruppo predefinito (gruppo 1) nel piano di ripristino. Sarà possibile selezionare solo le macchine virtuali che erano protette nell'origine (selezionate nel piano di ripristino) e che sono protette nella destinazione (selezionate nel piano di ripristino).

## <a name="how-to-customize-and-extend-recovery-plans"></a>Come personalizzare ed estendere i piani di ripristino

È possibile personalizzare ed estendere i piani di ripristino accedendo al pannello della risorsa piano di ripristino di Site Recovery e facendo clic sulla scheda Personalizza.

È possibile personalizzare ed estendere i piani di ripristino:

- **Aggiungere nuovi gruppi**: è possibile aggiungere gruppi del piano di ripristino aggiuntivi (fino a sette) al gruppo predefinito e quindi aggiungere più computer o gruppi di replica ai gruppi del piano di ripristino. I gruppi vengono numerati nell'ordine in cui vengono aggiunti. Una macchina virtuale o un gruppo di replica può essere incluso solo in un gruppo del piano di ripristino.
- **Aggiungere un'azione manuale**: è possibile aggiungere azioni manuali da eseguire prima o dopo un gruppo del piano di ripristino. Quando viene eseguito il piano di ripristino, questo si interrompe in corrispondenza del punto in cui è stata inserita l'azione manuale. Una finestra di dialogo richiede all'utente di specificare che l'azione manuale è stata completata.
- **Aggiungere uno script**: è possibile aggiungere script che vengono eseguiti prima o dopo un gruppo del piano di ripristino. Quando si aggiunge uno script, viene aggiunto un nuovo set di azioni per il gruppo. Ad esempio, verrà creato un set di passaggi preliminari per il gruppo 1 denominato: Gruppo 1: passaggi preliminari. Tutti i passaggi preliminari vengono elencati in questo set. Se si dispone di un server VMM distribuito, è possibile aggiungere solo uno script nel sito primario. [Altre informazioni](site-recovery-how-to-add-vmmscript.md).
- **Aggiungere runbook di Azure**: è possibile estendere i piani di ripristino con i runbook di Azure. Ad esempio, per automatizzare le attività o per creare un ripristino in un solo passaggio. [Altre informazioni](site-recovery-runbook-automation.md).


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Come aggiungere uno script, un runbook o un'azione manuale a un piano

Dopo aver aggiunto macchine virtuali o gruppi di replica al gruppo del piano di ripristino predefinito e dopo aver creato il piano, è possibile aggiungere uno script o un'azione manuale.

1. Aprire il piano di ripristino.
2. Fare clic su un elemento nell'elenco **Passaggio**, quindi fare clic su **Script** o **Azione manuale**.
3. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Per spostare la posizione dello script verso l'alto o verso il basso, usare i pulsanti **Sposta su** e **Sposta giù**.
4. Se si aggiunge uno script VMM, selezionare **Failover to VMM script** (Failover nello script VMM). In **Percorso script**, digitare il percorso relativo alla condivisione. Nell'esempio VMM di seguito specificare il percorso: **\RPScripts\RPScript.PS1**.
5. Se si aggiunge un runbook di automazione di Azure, specificare l'account di Automazione di Azure in cui si trova il runbook e selezionare lo script di runbook di Azure appropriato.
6. Per verificare che lo script funzioni come previsto, effettuare un failover del piano di ripristino.

Le opzioni di script o runbook sono disponibili solo negli scenari seguenti quando si effettua un failover o un failback. Un'azione manuale è disponibile sia per il failover che per il failback.


|Scenario               |Failover |Failback |
|-----------------------|---------|---------|
|Da Azure ad Azure         |Runbook |Runbook  |
|Da VMware ad Azure        |Runbook |ND       | 
|Da VMM ad Azure           |Runbook |Script   |
|Da sito Hyper-V ad Azure  |Runbook |ND       |
|Da VMM a VMM             |Script   |Script   |


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.

Guardare questo video per vedere il piano di ripristino in azione.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
