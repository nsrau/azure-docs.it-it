---
title: 'Backup di Azure: backup coerente delle applicazioni di VM Linux | Microsoft Docs'
description: Usare gli script per garantire backup coerenti con l&quot;applicazione in Azure, per le macchine virtuali Linux. Gli script si applicano solo alle VM Linux in una distribuzione di Resource Manager, non si applicano alle VM di Windows o alle distribuzioni di Service Manager. In questo articolo vengono illustrati i passaggi per configurare gli script, inclusa la risoluzione dei problemi.
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: backup coerente delle app; backup coerente delle applicazioni di VM di Azure; backup di VM Linux; Backup di Azure
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 0f4ca1924531df890433ec092790e6bec7c41df0
ms.lasthandoff: 04/13/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Backup coerente delle applicazioni di VM Linux di Azure (Anteprima)

Questo articolo illustra il framework degli script di pre e post-backup di Linux e il relativo uso per eseguire backup coerenti delle applicazioni di VM Linux di Azure.

> [!Note]
> Il framework degli script di pre e post-backup è supportato solo per le macchine virtuali Linux distribuite di Resource Manager. Gli script per la coerenza con l'applicazione non sono supportati per le macchine virtuali distribuite di Service Manager o le macchine virtuali di Windows.
>

## <a name="how-the-framework-works"></a>Come funziona il framework

Il framework offre un'opzione per eseguire script di pre e post-backup durante la creazione di uno snapshot di VM. Lo script di pre-backup viene eseguito appena prima di acquisire lo snapshot della VM e lo script di post-backup viene eseguito subito dopo il completamento dello snapshot della VM. Questo offre agli utenti la flessibilità di controllare le applicazioni e l'ambiente durante l'esecuzione del backup della VM.

Uno scenario importante per questo framework è assicurare backup coerenti delle applicazioni delle VM. Lo script di pre-backup può chiamare API di applicazione native per disattivare gli IO e scaricare il contenuto in memoria sul disco. Questo assicura che le applicazioni dello snapshot siano coerenti, ovvero che l'applicazione si attiverà quando la VM sarà avviata dopo il ripristino. Gli script di post-backup possono essere usati per sbloccare gli IO mediante le API di applicazioni native, in modo che l'applicazione possa riprendere le normali operazioni post snapshot della VM.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passaggi per configurare gli script di pre e post-backup

1. Accedere alla VM Linux per eseguire il backup come utente root.

2. Scaricare VMSnapshotPluginConfig.json da [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e copiarlo nella cartella /etc/azure in tutte le VM di cui eseguire il backup. Creare la directory /etc/azure se non esiste già.

3. Copiare lo script di pre-backup e lo script di post-backup per l'applicazione su tutte le VM di cui eseguire il backup. È possibile copiare gli script in qualsiasi posizione nella VM, è necessario aggiornare il percorso completo dei file di script nel file VMSnapshotPluginConfig.json

4. Assicurarsi che siano disponibili le autorizzazioni seguenti per i file:

   - VMSnapshotPluginConfig.json: autorizzazione "600" ovvero solo l'utente "root" deve avere le autorizzazioni di "lettura" e "scrittura" per questo file, nessun utente deve avere autorizzazioni di "esecuzione".
   - File di script di pre-backup: autorizzazione "700" ovvero solo l'utente "root" deve avere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" per questo file.
   - Script di post-backup: autorizzazione "700" ovvero solo l'utente "root" deve avere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" per questo file.

   > [!Note]
   > Il framework offre notevoli potenzialità agli utenti, perciò è molto importante che sia completamente sicuro e solo l'utente "root" deve avere accesso ai file json e di script critici.
   > Se i requisiti indicati non sono soddisfatti, lo script non verrà eseguito e si otterrà un backup coerente con l'arresto anomalo del sistema/file system.
   >

5. Configurare VMSnapshotPluginConfig.json come indicato di seguito
    - **pluginName**: lasciare invariato il campo, altrimenti gli script potrebbero non funzionare come previsto.
    - **preScriptLocation**: fornire il percorso completo dello script di pre-backup nella VM di cui eseguire il backup.
    - **postScriptLocation**: fornire il percorso completo dello script di post-backup nella VM di cui eseguire il backup.
    - **preScriptParams**: qualsiasi parametro opzionale che deve essere passato allo script di pre-backup. Tutti i parametri devono essere messi fra virgolette e separati dalla virgola quando sono più di uno.
    - **postScriptParams**: qualsiasi parametro opzionale che deve essere passato allo script di post-backup. Tutti i parametri devono essere messi fra virgolette e separati dalla virgola quando sono più di uno.
    - **preScriptNoOfRetries**: numero di volte in cui lo script di pre-backup deve essere ritentato in caso di eventuali errori prima di terminare. 0 indica un solo tentativo, senza alcun nuovo tentativo in caso di errore.
    - **postScriptNoOfRetries**: numero di volte in cui lo script di post-backup deve essere ritentato in caso di eventuali errori prima di terminare. 0 indica un solo tentativo, senza alcun nuovo tentativo in caso di errore.
    - **timeoutInSeconds**: timeout individuali per lo script di pre-backup e lo script di post-backup.
    - **continueBackupOnFailure**: impostare questo valore su true se si desidera che Backup di Azure esegua il fallback a un backup coerente con l'arresto anomalo del sistema/file system in caso di errore dello script di pre-backup o post-backup. Impostando questo valore su false il backup viene interrotto in caso di errore dello script (tranne nel caso di VM con un solo disco, situazione in cui eseguirà il fallback su un backup coerente con l'arresto anomalo del sistema indipendentemente da questa impostazione).
    - **fsFreezeEnabled**: specifica se il comando fsfreeze di Linux deve essere chiamato durante la creazione dello snapshot della VM per garantire la coerenza del file system. Si consiglia di mantenere l'impostazione true, a meno che l'applicazione abbia legami di dipendenza con la disattivazione di fsfreeze.

6. Il framework di script è ora configurato, se il backup della VM è già configurato, il backup successivo chiamerà gli script e attiverà backup coerenti delle applicazioni. Se il backup della VM non è configurato, configurarlo facendo riferimento a [Backup di macchine virtuali di Azure in insiemi di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Accertarsi di aggiungere le funzioni di log appropriate negli script di pre e post-backup e controllare i log di script per risolvere eventuali problemi degli script. Se continuano a verificarsi problemi durante l'esecuzione degli script, vedere la tabella seguente per altre informazioni.

| Errore | Messaggio di errore | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Lo script di pre-backup ha restituito un errore perciò il backup delle applicazioni potrebbe non essere coerente.    | Controllare i log di errore dello script per risolvere il problema.|  
|    Post-ScriptExecutionFailed |    Lo script di post-backup ha restituito un errore che potrebbe compromettere lo stato dell'applicazione. |    Controllare i log di errore dello script per risolvere il problema e controllare lo stato dell'applicazione. |
| Pre-ScriptNotFound |    Lo script di pre-backup non è stato trovato nel percorso specificato nel file di configurazione VMSnapshotPluginConfig.json. |    Assicurarsi che lo script di pre-backup sia presente nel percorso specificato nel file di configurazione per garantire un backup delle applicazioni coerente.|
| Post-ScriptNotFound |    Lo script di post-backup non è stato trovato nel percorso specificato nel file di configurazione VMSnapshotPluginConfig.json |    Assicurarsi che lo script di post-backup sia presente nel percorso specificato nel file di configurazione per garantire un backup delle applicazioni coerente.|
| IncorrectPluginhostFile |    Il file Pluginhost incluso con l'estensione VmSnapshotLinux è danneggiato perciò non è possibile eseguire lo script di pre-backup e quello di post-backup e il backup delle applicazioni non sarà coerente.    | Disinstallare l'estensione VmSnapshotLinux che sarà automaticamente reinstallata con il backup successivo per risolvere il problema. |
| IncorrectJSONConfigFile | Il file VMSnapshotPluginConfig.json non è corretto, perciò non è possibile eseguire lo script di pre-backup e quello di post-backup e il backup delle applicazioni non sarà coerente | Scaricare la copia da [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) ed eseguire di nuovo la configurazione |
| InsufficientPermissionforPre-Script | Per eseguire gli script, l'utente root deve essere il proprietario del file e il file deve avere le autorizzazioni "700", ovvero solo il proprietario deve possedere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" | Assicurarsi che l'utente "root" sia il "proprietario" del file di script e che solo il proprietario abbia le autorizzazioni di "lettura", "scrittura" ed "esecuzione". |
| InsufficientPermissionforPost-Script | Per eseguire gli script, l'utente root deve essere il proprietario del file e il file deve avere le autorizzazioni "700", ovvero solo il proprietario deve possedere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" | Assicurarsi che l'utente "root" sia il "proprietario" del file di script e che solo il proprietario abbia le autorizzazioni di "lettura", "scrittura" ed "esecuzione". |
| Pre-ScriptTimeout | Si è verificato il time-out dell'esecuzione dello script di pre-backup per un backup coerente della applicazioni. | Controllare lo script e aumentare il timeout nel file VMSnapshotPluginConfig.json disponibile all'indirizzo /etc/azure. |
| Post-ScriptTimeout | Si è verificato il time-out dell'esecuzione dello script di post-backup per un backup coerente della applicazioni. | Controllare lo script e aumentare il timeout nel file VMSnapshotPluginConfig.json disponibile all'indirizzo /etc/azure. |

## <a name="next-steps"></a>Passaggi successivi
[Configurare il backup di una VM in un insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

