---
title: Creare piani di ripristino per il failover e il ripristino in Azure Site Recovery | Microsoft Docs
description: Viene descritto come creare e personalizzare i piani di ripristino per eseguire il failover e il ripristino delle macchine virtuali e dei server fisici in Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 9ab51cb8e11df43ba2157b11e25a1f29b19e4da9
ms.openlocfilehash: e36f19e9c429c0e4b42e96b28b1ba995bd1bf167
ms.lasthandoff: 02/15/2017


---
# <a name="create-recovery-plans"></a>Creare piani di ripristino


L'articolo contiene informazioni sulla creazione e la personalizzazione dei piani di ripristino in [Azure Site Recovery?](site-recovery-overview.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Per i piani di ripristino, procedere come segue:

* Definire gruppi di computer che eseguono il failover insieme e quindi si avviano contemporaneamente.
* Creare le dipendenze tra i computer raggruppandoli in un gruppo di piano di ripristino. Ad esempio, per eseguire il failover e visualizzare un'applicazione specifica, raggruppare tutte le macchine virtuali per l'applicazione nello stesso gruppo di piano di ripristino.
* Failover. È possibile eseguire un failover non pianificato o un test, pianificato, in un piano di ripristino.


## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Fare clic su **Piani di ripristino** > **Crea piano di ripristino**.
   Specificare un nome per il piano di ripristino, nonché per il server di origine e di destinazione Nella posizione di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino.

    - Per la replica da un server VMM in un altro server VMM, selezionare **Tipo origine** > **VMM**, nonché i server VMM di origine e destinazione. Fare clic su **Hyper-V** per visualizzare i cloud protetti.
    - Per la replica da VMM ad Azure, selezionare **Tipo origine** > **VMM**.  Selezionare il server VMM di origine e **Azure** come destinazione.
    - Per la replica Hyper-V in Azure (senza VMM), selezionare **Tipo origine** > **Sito Hyper-V**. Selezionare il sito come origine e **Azure **come destinazione.
    - Per una VM VMware o un server fisico locale in Azure, selezionare un server di configurazione come origine e **Azure** come destinazione.
2. In **Seleziona macchine virtuali** selezionare le macchine virtuali (o il gruppo di replica) che si vuole aggiungere al gruppo predefinito (gruppo 1) nel piano di ripristino.

## <a name="customize-and-extend-recovery-plans"></a>Personalizzare ed estendere i piani di ripristino

È possibile personalizzare ed estendere i piani di ripristino:

- **Aggiungere nuovi gruppi**: è possibile aggiungere gruppi del piano di ripristino aggiuntivi (fino a sette) al gruppo predefinito e quindi aggiungere più computer o gruppi di replica ai gruppi del piano di ripristino. I gruppi vengono numerati nell'ordine in cui vengono aggiunti. Una macchina virtuale o un gruppo di replica può essere incluso solo in un gruppo del piano di ripristino.
- **Aggiungere un'azione manuale**: è possibile aggiungere azioni manuali da eseguire prima o dopo un gruppo del piano di ripristino. Quando viene eseguito il piano di ripristino, questo si interrompe in corrispondenza del punto in cui è stata inserita l'azione manuale. Una finestra di dialogo richiede all'utente di specificare che l'azione manuale è stata completata.
- **Aggiungere uno script **: è possibile aggiungere script che vengono eseguiti prima o dopo un gruppo del piano di ripristino. Quando si aggiunge uno script, viene aggiunto un nuovo set di azioni per il gruppo. Ad esempio, verrà creato un set di passaggi preliminari per il gruppo 1 denominato: Gruppo 1: passaggi preliminari. Tutti i passaggi preliminari verranno elencati in questo set. Se si dispone di un server VMM distribuito, è possibile aggiungere solo uno script nel sito primario.
- **Aggiungere runbook di Azure**: è possibile estendere i piani di ripristino con i runbook di Azure. Ad esempio, per automatizzare le attività o per creare un ripristino in un solo passaggio. [Altre informazioni](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Aggiungere script

È possibile usare gli script di PowerShell nei piani di ripristino.

 - Assicurarsi di usare blocchi try-catch in modo che le eccezioni vengano gestite normalmente.
    - Se si verifica un'eccezione nello script, l'esecuzione viene interrotta e l'attività viene mostrata come non riuscita.
    - Se si verifica un errore, qualsiasi parte rimanente dello script non viene eseguita.
    - Se si verifica un errore quando si esegue un failover non pianificato, il piano di ripristino continua.
    - Se si verifica un errore quando si esegue un failover pianificato, il piano di ripristino si interrompe. È necessario correggere lo script, verificare che venga eseguito come previsto e quindi eseguire nuovamente il piano di ripristino.
- Il comando Write-Host non funziona in uno script del piano di ripristino e lo script avrà esito negativo. Per creare l'output, creare uno script del proxy che a sua volta esegue lo script principale. Assicurarsi che tutto l'output venga reindirizzato utilizzando il comando >>.
  * Lo script si interrompe se non viene restituito in 600 secondi.
  * Se viene scritto qualcosa in STDERR, lo script viene classificato come non completato. Queste informazioni vengono visualizzate nei dettagli dell'esecuzione di script.

Se si usa VMM nella distribuzione:

* Gli script in un piano di ripristino vengono eseguiti nel contesto dell'account del servizio VMM. Assicurarsi che questo account disponga delle autorizzazioni di lettura per la condivisione remota in cui si trova lo script. Testare lo script da eseguire a livello di privilegi dell'account di servizio VMM.
* I cmdlet di VMM vengono forniti in un modulo di Windows PowerShell. Il modulo viene installato quando si installa la console VMM. È possibile caricarlo nello script, usando il comando seguente nello script: 
   - Import-Module -Name virtualmachinemanager. [Altre informazioni](https://technet.microsoft.com/library/hh875013.aspx).
* Assicurarsi di disporre di almeno un server di libreria nella distribuzione di VMM. Per impostazione predefinita il percorso della condivisione di libreria per un server VMM si trova in locale nel server VMM con il nome di cartella MSCVMMLibrary.
    * Se il percorso della condivisione di libreria è remoto (o locale ma non condiviso con MSCVMMLibrary), configurare la condivisione come segue (usando \\libserver2.contoso.com\share\ come esempio):
      * Aprire l'editor del Registro di sistema e passare a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Modificare il valore **ScriptLibraryPath** e inserire il valore \\libserver2.contoso.com\share\.. Specificare il nome di dominio completo. Fornire le autorizzazioni per il percorso di condivisione.
      * Assicurarsi di testare lo script con un account utente che disponga delle stesse autorizzazioni come account del servizio VMM. In questo modo si verifica che gli script testati autonomamente vengano eseguiti nello stesso modo in cui verrebbero eseguiti nei piani di ripristino. Nel server VMM, impostare i criteri di esecuzione in modo venga ignorato quanto segue:
        * Aprire la console di Windows PowerShell a 64 bit con privilegi elevati.
        * Digitare: **Set-executionpolicy bypass**. [Altre informazioni](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Aggiungere uno script o un'azione manuale a un piano

Dopo aver aggiunto macchine virtuali o gruppi di replica al gruppo del piano di ripristino predefinito e dopo aver creato il piano, è possibile aggiungere uno script.

1. Aprire il piano di ripristino.
2. Fare clic su un elemento nell'elenco **Passaggio**, quindi fare clic su **Script** o **Azione manuale**.
3. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Usare i pulsanti **Move Up** (Sposta su) e **Sposta giù** per spostare la posizione dello script verso l'alto o verso il basso.
4. Se si aggiunge uno script VMM, selezionare **Failover to VMM script** (Failover nello script VMM). In **Percorso script**, digitare il percorso relativo alla condivisione. Nell'esempio VMM di seguito specificare il percorso: **\RPScripts\RPScript.PS1**.
5. Se si aggiunge un runbook di automazione di Azure, specificare l'account di Automazione di Azure in cui si trova il runbook e selezionare lo script di runbook di Azure appropriato.
6. Eseguire un failover del piano di ripristino per assicurarsi che lo script funzioni come previsto.


### <a name="vmm-script"></a>Script VMM

Se si dispone di un sito di origine VMM, è possibile creare uno script nel server VMM e includerlo nel piano di ripristino.

1. Creare una nuova cartella nella condivisione di libreria. Ad esempio, \<VMMServerName>\MSSCVMMLibrary\RPScripts. Posizionarla nei server VMM di origine e destinazione.
2. Creare lo script (ad esempio RPScript) e verificare che funzioni come previsto.
3. Inserire lo script nel percorso \<VMMServerName>\MSSCVMMLibrary nei server VMM di origine e di destinazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.

