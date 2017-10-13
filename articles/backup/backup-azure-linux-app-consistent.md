---
title: 'Backup di Azure: backup coerente con le applicazioni di VM Linux | Microsoft Docs'
description: Usare gli script per garantire backup coerenti con l'applicazione in Azure, per le macchine virtuali Linux. Gli script si applicano solo alle VM Linux in una distribuzione di Resource Manager, non si applicano alle VM di Windows o alle distribuzioni di Service Manager. In questo articolo vengono illustrati i passaggi per configurare gli script, inclusa la risoluzione dei problemi.
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
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Backup coerente con le applicazioni di VM Linux di Azure (anteprima)

Questo articolo illustra il framework degli script di pre e post-backup di Linux e il relativo uso per eseguire backup coerenti con le applicazioni di VM Linux di Azure.

> [!Note]
> Il framework degli script di pre e post-backup è supportato solo per le macchine virtuali Linux distribuite di Azure Resource Manager. Gli script per la coerenza con l'applicazione non sono supportati per le macchine virtuali distribuite di Service Manager o le macchine virtuali di Windows.
>

## <a name="how-the-framework-works"></a>Come funziona il framework

Il framework offre un'opzione per eseguire script di pre e post-backup durante la creazione di snapshot delle macchine virtuali. Gli script pre-backup vengono eseguiti immediatamente prima della creazione dello snapshot della macchina virtuale e gli script post-backup vengono eseguiti immediatamente dopo la creazione dello snapshot della macchina virtuale. Questo offre la flessibilità di controllare l'applicazione e l'ambiente quando si creano snapshot delle macchine virtuali.

In questo scenario, è importante assicurare backup coerenti con le applicazioni delle macchine virtuali. Lo script di pre-backup può richiamare API native delle applicazioni per disattivare gli I/O e scaricare sul disco il contenuto in memoria. Ciò garantisce che lo snapshot sia coerente con le applicazioni (ossia, che l'applicazione venga avviata quando viene avviata la macchina virtuale dopo il ripristino). Lo script di post-backup può essere utilizzato per sbloccare gli I/O. Consente questo mediante le API native delle applicazioni, in modo che l'applicazione possa riprendere le normali operazioni successivamente allo snapshot della macchina virtuale.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Passaggi per configurare gli script di pre e post-backup

1. Accedere come utente root alla macchina virtuale Linux VM di cui si vuole eseguire il backup.

2. Scaricare **VMSnapshotScriptPluginConfig.json** da [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) e quindi copiarlo nella cartella **/etc/azure** in tutte le macchine virtuali di cui si intende eseguire il backup. Creare la directory **/etc/azure** se non esiste già.

3. Copiare lo script di pre-backup e lo script di post-backup per l'applicazione su tutte le macchine virtuali di cui eseguire il backup. È possibile copiare gli script in qualsiasi posizione nella macchina virtuale. Assicurarsi di aggiornare il percorso completo del file di script nel file **VMSnapshotScriptPluginConfig.json**.

4. Assicurarsi che siano disponibili le autorizzazioni seguenti per i file:

   - **VMSnapshotScriptPluginConfig.json**: autorizzazione "600". Ad esempio solo l'utente "root" deve avere le autorizzazioni di "lettura" e "scrittura" per questo file, nessun utente deve avere autorizzazioni di "esecuzione".

   - **File script di pre-backup**: autorizzazione "700".  Ad esempio, solo l'utente "root" deve avere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" per questo file.
  
   - **Script di post-backup**: autorizzazione "700". Ad esempio, solo l'utente "root" deve avere le autorizzazioni di "lettura", "scrittura" ed "esecuzione" per questo file.

   > [!Important]
   > Il framework offre notevoli potenzialità agli utenti. È importante assicurare che solo l'utente "root" abbia accesso ai file di script e JSON critici.
   > Se i requisiti precedenti non sono soddisfatti, lo script non viene eseguito. In questo modo si genera un backup coerente con file system e arresto anomalo.
   >

5. Configurare **VMSnapshotScriptPluginConfig.json** come illustrato di seguito:
    - **pluginName**: lasciare invariato il campo, altrimenti gli script potrebbero non funzionare come previsto.

    - **preScriptLocation**: specificare il percorso completo dello script di pre-backup nella VM di cui si eseguirà il backup.

    - **postScriptLocation**: specificare il percorso completo dello script di post-backup nella VM di cui si eseguirà il backup.

    - **preScriptParams**: specificare i parametri facoltativi da passare allo script di pre-backup. Tutti i parametri devono essere racchiusi tra virgolette e, se sono presenti più parametri, devono essere separati da virgole.

    - **postScriptParams**: specificare i parametri facoltativi da passare allo script di post-backup. Tutti i parametri devono essere racchiusi tra virgolette e, se sono presenti più parametri, devono essere separati da virgole.

    - **preScriptNoOfRetries**: impostare il numero di volte in cui lo script di pre-backup deve essere ritentato se è presente un errore prima di terminare. Zero indica un solo tentativo, senza alcun nuovo tentativo in caso di errore.

    - **postScriptNoOfRetries**: impostare il numero di volte in cui lo script di post-backup deve essere ritentato se è presente un errore prima di terminare. Zero indica un solo tentativo, senza alcun nuovo tentativo in caso di errore.
    
    - **timeoutInSeconds**: specificare i timeout individuali per lo script di pre-backup e lo script di post-backup.

    - **continueBackupOnFailure**: impostare questo valore su **true** se si desidera che Backup di Azure esegua il fallback a un backup coerente con file system/arresto anomalo in caso di errore dello script di pre-backup o post-backup. Impostando questo valore su **false** il backup viene interrotto in caso di errore dello script (tranne nel caso in cui sia presenta una macchina virtuale con un solo disco che esegue il fallback su un backup coerente con l'arresto anomalo indipendentemente da questa impostazione).

    - **fsFreezeEnabled**: specificare se il comando fsfreeze di Linux deve essere chiamato durante la creazione dello snapshot della macchina virtuale per garantire la coerenza del file system. Si consiglia di mantenere questa impostazione su **true**, a meno che l'applicazione abbia legami di dipendenza con la disattivazione di fsfreeze.

6. Il framework di script è ora configurato. Se il backup della macchina virtuale è già configurato, il backup successivo richiamerà gli script e attiverà backup coerenti con le applicazioni. Se il backup della macchina virtuale non è configurato, configurarlo facendo riferimento a [Backup di macchine virtuali di Azure in insiemi di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Accertarsi di aggiungere le funzioni di log appropriate negli script di pre e post-backup e controllare i log di script per risolvere eventuali problemi degli script. Se continuano a verificarsi problemi durante l'esecuzione degli script, vedere la tabella seguente per altre informazioni.

| Errore | Messaggio di errore | Azione consigliata |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Lo script di pre-backup ha restituito un errore perciò il backup potrebbe non essere coerente con le applicazioni.   | Controllare i log di errore dello script per risolvere il problema.|  
|   Post-ScriptExecutionFailed |    Lo script di post-backup ha restituito un errore che potrebbe compromettere lo stato dell'applicazione. |    Controllare i log di errore dello script per risolvere il problema e verificare lo stato dell'applicazione. |
| Pre-ScriptNotFound |  Lo script di pre-backup non è stato trovato nel percorso specificato nel file di configurazione **VMSnapshotScriptPluginConfig.json**. |   Assicurarsi che lo script di pre-backup sia presente nel percorso specificato nel file di configurazione per garantire un backup coerente con le applicazioni.|
| Post-ScriptNotFound | Lo script di post-backup non è stato trovato nel percorso specificato nel file di configurazione **VMSnapshotScriptPluginConfig.json**. |   Assicurarsi che lo script di post-backup sia presente nel percorso specificato nel file di configurazione per garantire un backup coerente con le applicazioni.|
| IncorrectPluginhostFile | Il file **Pluginhost** incluso con l'estensione VmSnapshotLinux è danneggiato perciò non è possibile eseguire gli script di pre e post-backup e il backup non sarà coerente con le applicazioni. | Disinstallare l'estensione **VmSnapshotLinux** che sarà automaticamente reinstallata con il backup successivo per risolvere il problema. |
| IncorrectJSONConfigFile | Il file **VMSnapshotScriptPluginConfig.json** non è corretto, perciò non è possibile eseguire gli script di pre e post-backup e il backup non sarà coerente con le applicazioni. | Scaricare la copia da [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) ed eseguirne di nuovo la configurazione. |
| InsufficientPermissionforPre-Script | Per eseguire gli script, l'utente "root" deve essere il proprietario del file e il file deve avere le autorizzazioni "700" (ovvero solo il "proprietario" deve possedere le autorizzazioni di "lettura", "scrittura" ed "esecuzione"). | Assicurarsi che l'utente "root" sia il "proprietario" del file di script e che solo il "proprietario" abbia le autorizzazioni di "lettura", "scrittura" ed "esecuzione". |
| InsufficientPermissionforPost-Script | Per eseguire gli script, l'utente root deve essere il proprietario del file e il file deve avere le autorizzazioni "700" (ovvero solo il "proprietario" deve possedere le autorizzazioni di "lettura", "scrittura" ed "esecuzione"). | Assicurarsi che l'utente "root" sia il "proprietario" del file di script e che solo il "proprietario" abbia le autorizzazioni di "lettura", "scrittura" ed "esecuzione". |
| Pre-ScriptTimeout | Si è verificato il time-out dell'esecuzione dello script di pre-backup per un backup coerente della applicazioni. | Controllare lo script e aumentare il timeout nel file **VMSnapshotScriptPluginConfig.json** disponibile all'indirizzo **/etc/azure**. |
| Post-ScriptTimeout | Si è verificato il time-out dell'esecuzione dello script di post-backup per un backup coerente della applicazioni. | Controllare lo script e aumentare il timeout nel file **VMSnapshotScriptPluginConfig.json** disponibile all'indirizzo **/etc/azure**. |

## <a name="next-steps"></a>Passaggi successivi
[Configurare il backup di una VM in un insieme di credenziali di Servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
