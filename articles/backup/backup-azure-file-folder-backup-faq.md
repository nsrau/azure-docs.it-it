---
title: Domande frequenti sull'agente di Backup di Azure | Microsoft Docs
description: Risposte alle domande frequenti sul funzionamento dell'agente di Backup di Azure e sui limiti di backup e conservazione.
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: backup e ripristino di emergenza; servizio Backup
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 227cdc87f3e2c8ed393145f4bbde7f74606bdf3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="questions-about-the-azure-backup-agent"></a>Domande sull'agente di Backup di Azure
Questo articolo contiene risposte a domande comuni che consentiranno di comprendere rapidamente i componenti dell'agente di Backup di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurare il backup
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Dov'è possibile scaricare l'agente di Backup di Azure più recente? <br/>
È possibile scaricare l'agente più recente per il backup di Windows Server, System Center DPM o dei client Windows da [qui](http://aka.ms/azurebackup_agent). Per eseguire il backup di una macchina virtuale usare l'agente di macchine virtuali, che installa automaticamente l'estensione appropriata. L'agente di VM è già presente nelle macchine virtuali create dalla raccolta di Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Quando si configura l'agente di Backup di Azure, viene richiesto di immettere le credenziali dell'insieme di credenziali. Le credenziali dell'insieme di credenziali scadono?
Sì, le credenziali dell'insieme di credenziali scadono dopo 48 ore. Se il file scade, accedere al portale di Azure e scaricare i file delle credenziali dell'insieme di credenziali dall'insieme di credenziali.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Da quali tipi di unità è possibile eseguire il backup di file e cartelle? <br/>
Non è possibile eseguire il backup delle unità o dei volumi seguenti:

* Supporto rimovibile: tutte le origini degli elementi di backup devono essere segnalate come corrette.
* Volumi di sola lettura: il volume deve essere accessibile in scrittura per garantire il funzionamento del servizio Copia Shadow del volume (VSS).
* Volumi offline: il volume deve essere online per garantire il funzionamento del servizio VSS.
* Condivisione di rete: il volume deve essere in locale nel server per poter essere sottoposto a backup online.
* Volumi protetti da BitLocker: il volume deve essere sbloccato prima di poter eseguire il backup.
* Identificazione del file system: NTFS è l'unico file system supportato.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Di quali tipi di file e cartelle è possibile eseguire il backup dal server?<br/>
Sono supportati i tipi seguenti:

* Crittografato
* Compresso
* Sparse
* Compresso + Sparse
* Collegamenti reali: non supportato, ignorato
* Punto di analisi: non supportato, ignorato
* Crittografato + Sparse: non supportato, ignorato
* Flusso compresso: non supportato, ignorato
* Flusso di tipo sparse: non supportato, ignorato

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>È possibile installare l'agente di Backup di Azure in una VM di Azure già supportata dal servizio Backup di Azure usando l'estensione per le VM? <br/>
Certo. Backup di Azure consente il backup a livello di VM per le VM di Azure usando l'estensione per le VM. Per proteggere file e cartelle nel sistema operativo Windows guest, installare l'agente di Backup di Azure nel sistema operativo Windows guest.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>È possibile installare l'agente di Backup di Azure in una VM di Azure per eseguire il backup di file e cartelle presenti nell'archivio temporaneo fornito dalla VM di Azure? <br/>
Sì. Installare Azure Backup Agent nel sistema operativo guest Windows ed eseguire il backup di file e cartelle in un'archiviazione temporanea. Dopo la cancellazione dei dati nell'archivio temporaneo, i processi di backup hanno esito negativo. Se poi i dati nell'archivio temporaneo sono stati eliminati, è possibile eseguire il ripristino solo in un archivio non temporaneo.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Qual è il requisito di dimensioni minime per la cartella della cache? <br/>
La dimensione della cartella della cache determina la quantità di dati sottoposti a backup. La cartella della cache deve essere circa il 5% dello spazio necessario per l'archivio dati.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Come si registra un server in un altro data center?<br/>
I dati di backup vengono inviati al data center dell'insieme di credenziali in cui sono registrati. Il modo più semplice per modificare il data center consiste nel disinstallare e reinstallare l'agente e registrarlo in un nuovo insieme di credenziali appartenente al data center desiderato.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>L'agente di Backup di Azure funziona in un server che usa la deduplicazione di Windows Server 2012? <br/>
Sì. Il servizio agente converte i dati deduplicati in dati normali quando si prepara l'operazione di backup. Quindi ottimizza i dati per il backup, crittografa i dati e infine invia i dati crittografati al servizio di backup online.

## <a name="backup"></a>Backup
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Come si modifica il percorso della cache specificato per l'agente di Backup di Azure?<br/>
Per modificare il percorso della cache, usare l'elenco seguente.

1. Arrestare il motore Backup eseguendo il comando seguente in un prompt dei comandi con privilegi elevati:

    ```PS C:\> Net stop obengine``` 
  
2. Non spostare i file. Copiare invece la cartella dello spazio della cache in un'altra unità con spazio sufficiente. Lo spazio della cache originale può essere rimosso dopo avere verificato che i backup usino il nuovo spazio della cache.
3. Aggiornare le voci del Registro di sistema seguenti con il percorso della nuova cartella dello spazio della cache.<br/>

    | Percorso del Registro | Chiave del Registro | Valore |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

4. Riavviare il motore Backup eseguendo il comando seguente in un prompt dei comandi con privilegi elevati:

    ```PS C:\> Net start obengine```

Una volta completata la creazione del backup nel nuovo percorso della cache, è possibile rimuovere la cartella della cache originale.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Dove è possibile salvare la cartella della cache perché l'agente di Backup di Azure funzioni come previsto?<br/>
I percorsi seguenti per la cartella della cache non sono consigliati:

* Condivisione di rete o supporti rimovibili: la cartella della cache deve essere locale nel server di cui eseguire il backup con il backup online. I percorsi di rete o i supporti rimovibili, ad esempio le unità USB, non sono supportati.
* Volumi offline: la cartella della cache deve essere online per il backup previsto con l'agente di Backup di Azure.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Esistono attributi della cartella della cache non supportati?<br/>
Per la cartella della cache non sono supportati gli attributi seguenti o le relative combinazioni:

* Crittografato
* De-duplicated
* Compresso
* Sparse
* Reparse-Point

La cartella della cache e il disco rigido virtuale dei metadati non hanno gli attributi necessari per l'agente di Backup di Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>È possibile modificare la quantità di larghezza di banda usata dal servizio Backup?<br/>
  Sì, usare l'opzione **Modifica proprietà** nell'agente di Backup per modificare la larghezza di banda. È possibile modificare la quantità di larghezza di banda e gli orari in cui si usa tale larghezza di banda. Per istruzioni dettagliate, vedere **[Abilitare la limitazione della larghezza di banda](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Gestire i backup
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Cosa accade se si rinomina un server Windows che esegue il backup dei dati in Azure?<br/>
Quando si rinomina un server, tutti i backup attualmente configurati vengono arrestati.
Registrare il nuovo nome del server con l'insieme di credenziali di backup. Quando si registra il nuovo nome con l'insieme di credenziali, la prima operazione di backup sarà *completa*. Se è necessario recuperare i dati sottoposti a backup nell'insieme di credenziali con il nome del server precedente, usare l'opzione [**Un altro server**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) nella procedura guidata **Ripristina dati**.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Qual è la lunghezza massima del percorso file specificabile nei criteri di backup usando l'agente di Backup di Azure? <br/>
L'agente di Backup di Azure si basa su NTFS. La [lunghezza del percorso del file è limitata dall'API Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Se il percorso dei file da proteggere ha una lunghezza superiore a quella consentita dall'API Windows, eseguire il backup della cartella padre o dell'unità disco.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Quali caratteri sono consentiti nel percorso file dei criteri di Backup di Azure che usano l'agente di Backup di Azure? <br>
 L'agente di Backup di Azure si basa su NTFS. Consente i [caratteri supportati da NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) come parte della specifica file. 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Perché viene visualizzato un avviso che segnala che non sono stati configurati backup di Azure per il server anche se si sono configurati criteri di backup? <br/>
Questo avviso viene generato quando le impostazioni di pianificazione di backup archiviate nel server locale non sono identiche alle impostazioni archiviate nell'insieme di credenziali di backup. Quando il server o le impostazioni sono state ripristinate a uno stato noto soddisfacente, le pianificazioni di backup possono perdere la sincronizzazione. Se viene visualizzato questo avviso, [riconfigurare i criteri di backup](backup-azure-manage-windows-server.md) e quindi **eseguire subito il backup** per risincronizzare il server locale con Azure.
