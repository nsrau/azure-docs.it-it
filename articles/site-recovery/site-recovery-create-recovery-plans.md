---
title: Creare piani di ripristino | Documentazione Microsoft
description: Creare piani di ripristino con Azure Site Recovery per eseguire il failover e ripristinare gruppi di server fisici e macchine virtuali.
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
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3cc2aa0ade25417a1e2a8fb96fc3a059349afa99


---
# <a name="create-recovery-plans"></a>Creare piani di ripristino
Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).

## <a name="overview"></a>Panoramica
L'articolo contiene informazioni sulla creazione e sulla personalizzazione dei piani di ripristino. 

I piani di ripristino sono costituiti da uno o più gruppi ordinati che contengono macchine virtuali o server fisici di cui si vuole eseguire il failover contemporaneamente. Per i piani di ripristino, procedere come segue:

* Definire gruppi di computer che eseguono il failover e quindi si avviano contemporaneamente.
* Creare le dipendenze tra i computer raggruppandoli insieme in un gruppo di piano di ripristino. Ad esempio, per eseguire il failover e visualizzare un'applicazione specifica, raggruppare le macchine virtuali per l'applicazione nello stesso gruppo di piano di ripristino.
* Automatizzare ed estendere il failover. È possibile eseguire un failover non pianificato o un test, pianificato, in un piano di ripristino. È possibile personalizzare i piani di ripristino con gli script di automazione di Azure e con azioni manuali.

I piani di ripristino vengono visualizzati nella relativa **sezione** nel portale di ripristino del sito.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Prima di iniziare
Tenere presente quanto segue:

* Un piano di ripristino non deve combinare le VM con schede di rete singole e multiple. Non è infatti consentito combinare queste VM in un servizio cloud di Azure.
* È possibile estendere i piani di ripristino con script e azioni manuali. Tenere presente quanto segue:
  
  * Scrivere gli script con Windows PowerShell.
  * Assicurarsi di usare blocchi try-catch in modo che le eccezioni vengano gestite normalmente. Se si verifica un'eccezione nello script, l'esecuzione viene interrotta e l'attività viene illustrato come non riuscita.  Se si verifica un errore, qualsiasi parte rimanente dello script non verrà eseguita. Se si verifica quando si esegue un failover non pianificato, il piano di ripristino continuerà. Se ciò si verifica quando si esegue un failover pianificato, il piano di ripristino verrà interrotto. In questo caso, correggere lo script, verificare che venga eseguito come previsto e quindi eseguire nuovamente il piano di ripristino.
  * Il comando Write-Host non funziona in uno script del piano di ripristino e lo script avrà esito negativo. Se si desidera creare l'output, creare uno script proxy che a sua volta esegue lo script principale e verificare che tutto l'output viene reindirizzato utilizzando il comando > >.
  * Lo script verifica il timeout se non viene restituito all'interno di 600 secondi.
  * Se viene scritto in STDERR, lo script verrà classificato come non completato. Queste informazioni verranno visualizzate nei dettagli dell'esecuzione di script.
  * Se si usa VMM nella distribuzione, si noti che:
    
    * Gli script in un piano di ripristino vengono eseguiti nel contesto dell'account del servizio VMM. Verificare che questo account disponga delle autorizzazioni di lettura sulla condivisione remota in cui si trova lo script e testare lo script da eseguire a livello di privilegi di account di servizio VMM.
    * I cmdlet di VMM vengono forniti in un modulo di Windows PowerShell. Il modulo Windows PowerShell di VMM viene installato quando si installa la console VMM. Il modulo VMM può essere caricato nello script utilizzando il seguente comando nello script: Import-Module-Name virtualmachinemanager. [Ulteriori informazioni](hhttps://technet.microsoft.com/library/hh875013.aspx).
    * Assicurarsi di disporre di almeno un server di libreria nella distribuzione di VMM. Per impostazione predefinita il percorso della condivisione di libreria per un server VMM si trova in locale nel server VMM con il nome di cartella MSCVMMLibrary.
    * Se il percorso della condivisione di libreria è remoto (o locale ma non condiviso con MSCVMMLibrary, configurare la condivisione come segue usando \\libserver2.contoso.com\share\ come esempio:
      * Aprire l'editor del Registro di sistema e passare a HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
      * Modificare il valore ScriptLibraryPath e inserire il valore \\\libserver2.contoso.com\share\.. Specificare il nome di dominio completo. Fornire le autorizzazioni per il percorso di condivisione.
      * Assicurarsi di testare lo script con un account utente che abbia le stesse autorizzazioni dell'account del servizio VMM, per garantire che gli script testati autonomi vengano eseguiti in modo analogo a come vengono eseguiti nei piani di ripristino. Nel server VMM, impostare i criteri di esecuzione in modo venga ignorato quanto segue:
        * Aprire la console di Windows PowerShell a 64 bit con privilegi elevati.
        * Digitare: **Set-executionpolicy bypass**. [Ulteriori informazioni](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino
Il modo in cui viene creato un piano di ripristino dipende dalla distribuzione di Site Recovery.

* **Replica delle VM VMware e dei server fisici**: si crea un piano e si aggiungono gruppi di replica contenenti macchine virtuali e server fisici a un piano di ripristino.
* **Replica delle VM Hyper-V (in cloud VMM)**: si crea un piano e si aggiungono macchine virtuali Hyper-V protette da un cloud VMM a un piano di ripristino.
* **Replica delle VM Hyper-V (non in cloud VMM)**: si crea un piano e si aggiungono macchine virtuali Hyper-V protette da un gruppo di protezione a un piano di ripristino.
* **Replica SAN**: si crea un piano e si aggiunge un gruppo di replica contenente macchine virtuali al piano di ripristino. Selezionare un gruppo di replica anziché le macchine virtuali specifiche perché tutte le macchine virtuali in un gruppo di replica devono eseguire il failover contemporaneamente (il failover viene prima eseguito a livello di archiviazione).

Creare un piano di ripristino come segue:

1. Fare clic sulla scheda **Piani di ripristino** > **Crea piano di ripristino**.
   Specificare un nome per il piano di ripristino, nonché per il server di origine e di destinazione Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino.
   
   * Se si esegue la replica da un server VMM in un altro server VMM, selezionare **Tipo di origine** > **VMM**, nonché i server VMM di origine e destinazione. Fare clic su **Hyper-V** per visualizzare i cloud configurati per l'utilizzo di Replica Hyper-V. 
   * Se si esegue la replica da un server VMM in un altro server VMM tramite SAN, selezionare **Tipo di origine** > **VMM** nonché i server VMM di origine e destinazione. Fare clic su **SAN** per visualizzare solo i cloud configurati per la replica SAN.
   * Se si esegue la replica da VMM in Azure selezionare **Tipo di origine** > **VMM**.  Selezionare il server VMM di origine e **Azure** come destinazione.
   * Se si esegue la replica da un sito di Hyper-V, selezionare **Tipo di origine** > **Sito Hyper-V**. Selezionare il sito come origine e **Azure **come destinazione.
   * Se si esegue la replica da VMware o un server fisico locale in Azure, selezionare un server di configurazione come origine e **Azure** come destinazione
2. In **Seleziona macchine virtuali** selezionare le macchine virtuali (o il gruppo di replica) che si vuole aggiungere al gruppo predefinito (gruppo 1) nel piano di ripristino.

## <a name="customize-recovery-plans"></a>Personalizzare piani di ripristino
Dopo aver aggiunto macchine virtuali protette o gruppi di replica al gruppo del piano di ripristino predefinito e dopo aver creato il piano, è possibile personalizzarlo:

* **Aggiungere nuovi gruppi**: è possibile aggiungere gruppi di piano di ripristino aggiuntivi. I gruppi vengono numerati nell'ordine in cui vengono aggiunti. È possibile aggiungere fino a sette gruppi. È possibile aggiungere più computer o gruppi di replica a questi nuovi gruppi. Si noti che una macchina virtuale o un gruppo di replica può essere incluso solo in un gruppo del piano di ripristino.
* **Aggiungere uno script **: è possibile aggiungere script prima o dopo un gruppo del piano di ripristino. Quando si aggiunge uno script, viene aggiunto un set di azioni per il gruppo. Ad esempio, verrà creato un set di passaggi preliminari per il gruppo 1 denominato: Gruppo 1: passaggi preliminari. Tutti i passaggi preliminari verranno elencati in questo set. Si noti che se si dispone di un server VMM distribuito, è possibile aggiungere solo uno script nel sito primario.
* **Aggiungere un'azione manuale**: è possibile aggiungere azioni manuali da eseguire prima o dopo un gruppo del piano di ripristino. Quando viene eseguito, il piano di ripristino si interrompe nel punto in cui è stata inserita l'azione manuale e in una finestra di dialogo viene richiesto di specificare il completamento dell'azione.

## <a name="extend-recovery-plans-with-scripts"></a>Estendere i piani di ripristino con gli script
È possibile aggiungere uno script per il piano di ripristino:

* Se si dispone di un sito di origine VMM, è possibile creare uno script nel server VMM e includerlo nel piano di ripristino.
* Se si esegue la replica in Azure è possibile integrare i runbook di automazione di Azure nel piano di ripristino

### <a name="create-a-vmm-script"></a>Creare uno script VMM
Creare lo script come segue:

1. Creare una nuova cartella nella condivisione di libreria, ad esempio \<NomeserverVMM>\MSSCVMMLibrary\RPScripts. Posizionarla nei server VMM di origine e destinazione.
2. Creare lo script (ad esempio RPScript) e verificare che funzioni come previsto.
3. Inserire lo script nel percorso \<NomeserverVMM>\MSSCVMMLibrary nei server VMM di origine e di destinazione.

### <a name="create-an-azure-automation-runbook"></a>Creare un runbook di automazione di Azure
È possibile estendere il piano di ripristino eseguendo un runbook di automazione di Azure come parte del piano. [Altre informazioni](site-recovery-runbook-automation.md).

### <a name="add-custom-settings-to-a-recovery-plan"></a>Aggiungere impostazioni personalizzate a un piano di ripristino
1. Aprire il piano di ripristino che si desidera personalizzare.
2. Fare clic per aggiungere una macchina virtuale o un nuovo gruppo.
3. Per aggiungere uno script o un'azione manuale, fare clic su qualsiasi elemento nell'elenco **Passaggio**, quindi fare clic su **Script** o **Azione manuale**. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Usare i pulsanti di comando per lo spostamento verso l'**alto** e verso il **basso** per spostare la posizione dello script verso l'alto o verso il basso.
4. Se si aggiunge uno script VMM, selezionare l’opzione relativa al **failover nello script VMM** e in **Percorso script** digitare il percorso relativo alla condivisione. Quindi, ad esempio se la condivisione si trova in: \\<VMMServerName>\MSSCVMMLibrary\RPScripts specificare il percorso: \RPScripts\RPScript.PS1.
5. Se si aggiunge un runbook di automazione di Azure, specificare l'**account di Automazione di Azure** in cui si trova il runbook e selezionare lo **script di runbook di Azure** appropriato.
6. Eseguire un failover del piano di ripristino per assicurarsi che lo script funzioni come previsto.

## <a name="next-steps"></a>Passaggi successivi
È possibile eseguire diversi tipi di failover nel piano di ripristino, inclusi un failover di test per verificare l'ambiente e failover pianificati o non pianificati. [Altre informazioni](site-recovery-failover.md)




<!--HONumber=Nov16_HO3-->


