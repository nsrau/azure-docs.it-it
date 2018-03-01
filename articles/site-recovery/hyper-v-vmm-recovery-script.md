---
title: Aggiungere uno script a un piano di ripristino in Azure Site Recovery | Microsoft Docs
description: Informazioni sui prerequisiti per l'aggiunta di un nuovo script di System Center Virtual Machine Manager (VMM) a un piano di ripristino in Azure.
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
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Aggiungere uno script VMM a un piano di ripristino

In questo articolo viene descritto come creare uno script di System Center Virtual Machine Manager (VMM) e aggiungerlo a un piano di ripristino in [Azure Site Recovery](site-recovery-overview.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>prerequisiti

È possibile usare gli script di PowerShell nei piani di ripristino. Perché sia possibile accedervi dal piano di ripristino, è necessario crearli e inserirli nella libreria VMM. Durante la scrittura di uno script, tenere presenti le considerazioni seguenti:

* Assicurarsi di usare blocchi try-catch in modo che le eccezioni vengano gestite normalmente.
    - Se si verifica un'eccezione nello script, l'esecuzione viene interrotta e l'attività viene mostrata come non riuscita.
    - Se si verifica un errore, la parte rimanente dello script non viene eseguita.
    - Se si verifica un errore quando si esegue un failover non pianificato, il piano di ripristino continua.
    - Se si verifica un errore quando si esegue un failover pianificato, il piano di ripristino si interrompe. Correggere lo script, verificare che venga eseguito come previsto e quindi eseguire nuovamente il piano di ripristino.
        - Il comando `Write-Host` non funziona nello script di un piano di ripristino. Se si usa il comando `Write-Host` in uno script, lo script non riesce. Per creare l'output, creare uno script del proxy che a sua volta esegue lo script principale. Per assicurarsi che tutto l'output venga reindirizzato usare il comando **\>\>**.
        - Lo script si interrompe se non viene restituito in 600 secondi.
        - Se viene scritto qualcosa in STDERR, lo script viene classificato come non completato. Queste informazioni vengono visualizzate nei dettagli dell'esecuzione di script.

* Gli script in un piano di ripristino vengono eseguiti nel contesto dell'account del servizio VMM. Assicurarsi che questo account disponga delle autorizzazioni di lettura per la condivisione remota in cui si trova lo script. Testare lo script in modo che venga eseguito con lo stesso livello di diritti utente dell'account del servizio VMM.
* I cmdlet di VMM vengono forniti in un modulo di Windows PowerShell. Il modulo viene installato quando si installa la console VMM. Per caricare il modulo nello script, usare il comando seguente nello script: 

    `Import-Module -Name virtualmachinemanager`

    Per altre informazioni, vedere [Introduzione a Windows PowerShell e VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Assicurarsi di disporre di almeno un server di libreria nella distribuzione di VMM. Per impostazione predefinita il percorso di condivisione della libreria per un server VMM si trova in locale nel server VMM con il nome di cartella MSCVMMLibrary.

  Se il percorso della condivisione della libreria è remoto (o locale ma non condiviso con MSCVMMLibrary), configurare la condivisione come segue, usando \\libserver2.contoso.com\share\ come esempio:
  
  1. Aprire l'editor del Registro di sistema e passare a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  2. Modificare il valore di **ScriptLibraryPath** in **\\\libserver2.contoso.com\share\\**. Specificare il nome di dominio completo. Fornire le autorizzazioni per il percorso di condivisione. Si tratta del nodo radice della condivisione. Per verificare la presenza del nodo radice, in VMM passare al nodo radice nella libreria. Il percorso che si apre è la radice del percorso. Si tratta del percorso da usare nella variabile.

  3. Testare lo script usando un account utente con lo stesso livello di diritti utente dell'account del servizio VMM. Usando questi diritti utente, è possibile verificare che gli script autonomi testati vengano eseguiti nello stesso modo in cui vengono eseguiti nei piani di ripristino. Nel server VMM impostare i criteri di esecuzione come segue in modo che vengano ignorati:

     a. Aprire la console di **Windows PowerShell a 64 bit** come amministratore.
     
     b. Digitare **Set-executionpolicy bypass**. Per altre informazioni, vedere [Uso del cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Impostare **Set-executionpolicy bypass** solo nella console di PowerShell a 64 bit. Se si esegue l'impostazione per la console di PowerShell a 32 bit, gli script non vengono eseguiti.

## <a name="add-the-script-to-the-vmm-library"></a>Aggiungere lo script alla libreria VMM

Se si dispone di un sito di origine VMM, è possibile creare uno script nel server VMM e includerlo nel piano di ripristino.

1. Nella condivisione di libreria creare una nuova cartella. Ad esempio, \<Nome server VMM>\MSSCVMMLibrary\RPScripts. Posizionare la cartella nei server VMM di origine e destinazione.
2. Creare lo script. Assegnare un nome, ad esempio RPScript. Verificare che lo script funzioni come previsto.
3. Inserire lo script nella cartella \<Nome server VMM>\MSSCVMMLibrary nei server VMM di origine e destinazione.

## <a name="add-the-script-to-a-recovery-plan"></a>Aggiungere lo script a un piano di ripristino

Dopo avere aggiunto macchine virtuali o gruppi di replica a un piano di ripristino e avere creato il piano, è possibile aggiungere lo script al gruppo.

1. Aprire il piano di ripristino.
2. Nell'elenco **Passaggio** selezionare una voce. Selezionare quindi **Script** o **Azione manuale**.
3. Specificare se si desidera aggiungere lo script o l’azione prima o dopo l'elemento selezionato. Per spostare lo script verso l'alto o verso il basso nell'elenco, usare i pulsanti **Sposta su** e **Sposta giù**.
4. Se si aggiunge uno script VMM, selezionare **Failover to VMM script** (Failover nello script VMM). In **Percorso script** immettere il percorso relativo della condivisione. Ad esempio, **\RPScripts\RPScript.PS1**.
5. Se si aggiunge un runbook di automazione di Azure, specificare l'account di Automazione di Azure in cui si trova il runbook e selezionare lo script di runbook di Azure che si desidera usare.
6. Per verificare che lo script funzioni come previsto, eseguire un failover di prova del piano di ripristino.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'[esecuzione dei failover](site-recovery-failover.md).

