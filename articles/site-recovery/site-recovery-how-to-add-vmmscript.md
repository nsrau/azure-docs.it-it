---
title: Come aggiungere script a un piano di ripristino in Azure Site Recovery | Microsoft Docs
description: Descrive i prerequisiti per l'aggiunta di un nuovo script a un piano di ripristino in VMM in Azure
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>Aggiungere script VMM a un piano di ripristino


Questo articolo contiene informazioni sulla creazione e l'aggiunta di script VMM a piani di ripristino in [Azure Site Recovery](site-recovery-overview.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>Prerequisiti per l'aggiunta di uno script a un piano di ripristino

È possibile usare gli script di PowerShell nei piani di ripristino. È necessario crearli e inserirli nella libreria VMM in modo che siano accessibili dal piano di ripristino. Di seguito sono riportate alcune considerazioni di cui tenere conto durante la stesura dello script.

* Assicurarsi di usare blocchi try-catch in modo che le eccezioni vengano gestite normalmente.
    - Se si verifica un'eccezione nello script, l'esecuzione viene interrotta e l'attività viene mostrata come non riuscita.
    - Se si verifica un errore, qualsiasi parte rimanente dello script non viene eseguita.
    - Se si verifica un errore quando si esegue un failover non pianificato, il piano di ripristino continua.
    - Se si verifica un errore quando si esegue un failover pianificato, il piano di ripristino si interrompe. È necessario correggere lo script, verificare che venga eseguito come previsto e quindi eseguire nuovamente il piano di ripristino.
        - Il comando Write-Host non funziona in uno script del piano di ripristino e lo script avrà esito negativo. Per creare l'output, creare uno script del proxy che a sua volta esegue lo script principale. Assicurarsi che tutto l'output venga reindirizzato utilizzando il comando >>.
        - Lo script si interrompe se non viene restituito in 600 secondi.
        - Se viene scritto qualcosa in STDERR, lo script viene classificato come non completato. Queste informazioni vengono visualizzate nei dettagli dell'esecuzione di script.

* Gli script in un piano di ripristino vengono eseguiti nel contesto dell'account del servizio VMM. Assicurarsi che questo account disponga delle autorizzazioni di lettura per la condivisione remota in cui si trova lo script. Testare lo script da eseguire a livello di privilegi dell'account di servizio VMM.
* I cmdlet di VMM vengono forniti in un modulo di Windows PowerShell. Il modulo viene installato quando si installa la console VMM. È possibile caricarlo nello script, usando il comando seguente nello script:
   - Import-Module -Name virtualmachinemanager. [Altre informazioni](https://technet.microsoft.com/library/hh875013.aspx).
* Assicurarsi di disporre di almeno un server di libreria nella distribuzione di VMM. Per impostazione predefinita il percorso della condivisione di libreria per un server VMM si trova in locale nel server VMM con il nome di cartella MSCVMMLibrary.
    * Se il percorso della condivisione di libreria è remoto (o locale ma non condiviso con MSCVMMLibrary), configurare la condivisione come segue (usando \\libserver2.contoso.com\share\ come esempio):
      * Aprire l'editor del Registro di sistema e passare a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Modificare il valore **ScriptLibraryPath** e inserirlo come \\libserver2.contoso.com\share\. Specificare il nome di dominio completo. Fornire le autorizzazioni per il percorso di condivisione. Si noti che questo è il nodo radice della condivisione. **Per controllare, è possibile sfogliare la raccolta nel nodo della radice in VMM. Il percorso che verrà visualizzato sarà la radice del percorso, ovvero il percorso necessario da usare nella variabile**.
      * Assicurarsi di testare lo script con un account utente che disponga delle stesse autorizzazioni come account del servizio VMM. In questo modo si verifica che gli script testati autonomamente vengano eseguiti nello stesso modo in cui verrebbero eseguiti nei piani di ripristino. Nel server VMM, impostare i criteri di esecuzione in modo venga ignorato quanto segue:
        * Aprire la console di **Windows PowerShell a 64 bit** con privilegi elevati.
        * Digitare: **Set-executionpolicy bypass**. [Altre informazioni](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> È consigliabile impostare i criteri di esecuzione su Bypass solo in PowerShell a 64 bit. Se si esegue questa impostazione in PowerShell a 32 bit, gli script non vengono eseguiti.

## <a name="add-the-script-to-the-vmm-library"></a>Aggiungere lo script alla libreria VMM

Se si dispone di un sito di origine VMM, è possibile creare uno script nel server VMM e includerlo nel piano di ripristino.

1. Creare una nuova cartella nella condivisione di libreria. Ad esempio, \<VMMServerName>\MSSCVMMLibrary\RPScripts. Posizionarla nei server VMM di origine e destinazione.
2. Creare lo script (ad esempio RPScript) e verificare che funzioni come previsto.
3. Inserire lo script nel percorso \<VMMServerName>\MSSCVMMLibrary nei server VMM di origine e di destinazione.

## <a name="add-the-script-to-a-recovery-plan"></a>Aggiungere lo script a un piano di ripristino

È possibile aggiungere lo script al gruppo dopo aver aggiunto a quest'ultimo macchine virtuali o gruppi di replica.

1. Aprire il piano di ripristino.
2. Fare clic su un elemento nell'elenco **Passaggio**, quindi fare clic su **Script** o **Azione manuale**.
3. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Usare i pulsanti **Move Up** (Sposta su) e **Sposta giù** per spostare la posizione dello script verso l'alto o verso il basso.
4. Se si aggiunge uno script VMM, selezionare **Failover to VMM script** (Failover nello script VMM). In **Percorso script**, digitare il percorso relativo alla condivisione. Nell'esempio VMM di seguito specificare il percorso: **\RPScripts\RPScript.PS1**.
5. Se si aggiunge un runbook di automazione di Azure, specificare l'account di Automazione di Azure in cui si trova il runbook e selezionare lo script di runbook di Azure appropriato.
6. Eseguire un failover di test del piano di ripristino per assicurarsi che lo script funzioni come previsto.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-failover.md) sull'esecuzione dei failover.
