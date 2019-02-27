---
title: Informazioni sul backup di macchine virtuali di Azure
description: Informazioni sul backup di macchine virtuali di Azure e su alcune procedure consigliate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c38c457bbf428d7252cf57168685201a2ca227ba
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446801"
---
# <a name="about-azure-vm-backup"></a>Informazioni sul backup di macchine virtuali di Azure

Questo articolo descrive come il [servizio Backup di Azure](backup-introduction-to-azure-backup.md) esegue il backup di macchine virtuali di Azure.

## <a name="backup-process"></a>Processo di backup

Ecco come il servizio Backup di Azure esegue un backup per le macchine virtuali di Azure.

1. Per le macchine virtuali di Azure selezionate per il backup, il servizio Backup di Azure avvia un processo di backup in base alla pianificazione specificata.
2. Durante il primo backup, viene installata un'estensione di backup nella macchina virtuale se è in esecuzione.
    - Per le macchine virtuali Windows viene installata l'estensione _VMSnapshot_.
    - Per le macchine virtuali Linux viene installata l'estensione_VMSnapshotLinux_.
3. Per le macchine virtuali Windows in esecuzione, il servizio Backup si coordina con il servizio VSS per creare uno snapshot della macchina virtuale coerente con l'app.
    - Per impostazione predefinita, Backup di Azure esegue backup VSS completi.
    - Se non riesce a creare uno snapshot coerente con le app, Backup di Azure crea uno snapshot coerente con i file per l'archiviazione sottostante (poiché non viene eseguita alcuna operazione di scrittura delle applicazioni durante l'arresto della macchina virtuale).
4. Per le macchine virtuali Linux, Backup di Azure crea un backup coerente con i file. Per gli snapshot coerenti con l'app è necessario personalizzare manualmente gli script pre/post.
5. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali. 
    - Il backup viene ottimizzato eseguendo in parallelo il backup di ogni disco di macchina virtuale.
    - Per ogni disco di cui viene eseguito il backup, Backup di Azure legge i blocchi su disco e identifica e trasferisce solo i blocchi di dati che sono stati modificati rispetto al backup precedente (differenziale).
    - Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Il tempo di backup totale per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.

6. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Crittografia dei backup di macchine virtuali di Azure

Quando si esegue il backup di macchine virtuali di Azure con Backup di Azure, viene eseguita la crittografia dei dati inattivi delle macchine virtuali con la crittografia del servizio di archiviazione. Backup di Azure inoltre può eseguire il backup di macchine virtuali di Azure crittografate con Crittografia dischi di Azure.


**Crittografia** | **Dettagli** | **Supporto**
--- | --- | ---
**Crittografia dischi di Azure** | Crittografia dischi di Azure consente di crittografare i dischi sia del sistema operativo sia di dati per le macchine virtuali di Azure.<br/><br/> Si integra con le chiavi di crittografia BitLocker (BEK), protette in un insieme di credenziali delle chiavi come segreti, o con le chiavi di crittografia delle chiavi di Azure Key Vault (KEK). | Backup di Azure supporta il backup delle macchine virtuali di Azure gestite e non gestite crittografate solo con chiavi BEK oppure sia con chiavi BEK che KEK.<br/><br/> Sia chiavi BEK che chiavi sottoposte a backup e crittografate.<br/><br/> Poiché le chiavi BEK e KEK vengono sottoposte a backup, se necessario gli utenti che dispongono delle autorizzazioni possono ripristinare le chiavi e i segreti nell'insieme di credenziali delle chiavi e recuperare la macchina virtuale crittografata.<br/><br/> Le chiavi e i segreti crittografati non possono essere letti da utenti non autorizzati o da Azure.
**Crittografia del servizio di archiviazione** | Con la crittografia del servizio di archiviazione, Archiviazione di Azure esegue automaticamente la crittografia dei dati inattivi prima di archiviarli e li decrittografa prima del recupero. | Backup di Azure usa la crittografia del servizio di archiviazione per la crittografia dei dati inattivi delle macchine virtuali di Azure.

- Il backup di macchine virtuali crittografate solo con chiavi BEK e con chiavi sia BEK che KEK è supportato per le macchine virtuali di Azure gestite e non gestite.
- I segreti BEK e le chiavi KEK sottoposti a backup vengono crittografati. Possono essere letti e usati solo se vengono ripristinati nell'insieme di credenziali delle chiavi da utenti autorizzati. Le chiavi o i segreti sottoposti a backup non possono essere letti o usati né da utenti non autorizzati né da Azure.
- Poiché anche le chiavi BEK vengono sottoposte a backup, se vengono perse, gli utenti autorizzati possono ripristinarle nell'insieme di credenziali delle chiavi e recuperare la macchina virtuale crittografata. 
- Solo gli utenti con il livello di autorizzazioni corretto possono eseguire il backup e il ripristino di macchine virtuali crittografate, nonché di chiavi e segreti.



## <a name="taking-snapshots"></a>Creazione di snapshot

Backup di Azure crea snapshot in base alla pianificazione di backup. 

- **Macchine virtuali Windows**: per le macchine virtuali Windows, il servizio Backup si coordina con il Servizio Copia Shadow del volume per ottenere uno snapshot dei dischi delle macchine virtuali coerente con le app.
    - Per impostazione predefinita, Backup di Azure esegue backup VSS completi. [Altre informazioni](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
    - Se si vuole modificare l'impostazione in modo che Backup di Azure esegua backup del Servizio Copia Shadow del volume, al prompt dei comandi impostare la chiave del Registro di sistema seguente: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Macchine virtuali Linux**: se si vuole creare snapshot della macchina virtuale Linux coerenti con l'app, usare il framework di script di pre-backup e post-backup di Linux per scrivere script personalizzati per garantire la coerenza.
    -  Backup di Azure richiama solo script di pre-backup e post-backup scritti dal cliente.
    - Se gli script di pre-backup e post-backup vengono eseguiti correttamente, Backup di Azure contrassegna il punto di ripristino come coerente con le applicazioni. È tuttavia il cliente a essere responsabile della coerenza con le applicazioni quando usa script personalizzati.
    - [Altre informazioni](backup-azure-linux-app-consistent.md) sulla configurazione degli script.


### <a name="snapshot-consistency"></a>Coerenza degli snapshot

La tabella seguente illustra i vari tipi di coerenza.

**Snapshot** | **Dettagli** | **Ripristino** | **Considerazioni**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con le app usano writer VSS (o script di pre/post-backup per Linux) che assicurano la coerenza dei dati delle app prima dell'esecuzione di un backup. | Quando si esegue il ripristino con uno snapshot coerente con le app, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente. | Windows: tutti i VSS writer sono riusciti correttamente<br/><br/> Linux: gli script pre/post sono stati configurati e sono riusciti correttamente
**Coerenza con il file system** | I backup coerenti con i file forniscono backup coerenti dei file su disco creando uno snapshot di tutti i file nello stesso momento.<br/><br/> | Quando si esegue il ripristino con uno snapshot coerente con i file, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti. | Windows: alcuni VSS writer non sono riusciti <br/><br/> Linux: predefinito (se gli script pre/post non sono stati configurati o non sono riusciti)
**Coerenza con l'arresto anomalo del sistema** | Un backup coerente con l'arresto anomalo del sistema si verifica spesso quando una macchina virtuale di Azure si arresta in fase di backup.  Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup.<br/><br/> Un punto di ripristino coerente con l'arresto anomalo del sistema non garantisce la coerenza dei dati per il sistema operativo o l'app. | Anche se non vi sono garanzie, la macchina virtuale viene avviata e viene eseguito un controllo del disco per correggere eventuali errori di danneggiamento. Le operazioni di scrittura o i dati in memoria che non sono stati trasferiti nel disco vengono persi. Le app implementano una propria procedura di verifica dei dati. Per un'app di database, ad esempio, se un log delle transazioni contiene voci non presenti nel database, il software del database esegue un rollback finché i dati non sono coerenti. | Macchina virtuale in stato di arresto


## <a name="restore-considerations"></a>Considerazioni sul ripristino 



**Considerazioni** | **Dettagli**
--- | ---
**Disco** | Il backup del disco di una macchina virtuale viene eseguito in parallelo. Se ad esempio una macchina virtuale ha quattro dischi, il servizio prova a eseguire il backup di tutti e quattro i dischi in parallelo. Il backup è incrementale (solo i dati modificati).
**Pianificazione** |  Per ridurre il traffico di backup, eseguire il backup di diverse macchine virtuali in diverse ore del giorno, senza sovrapposizioni. Il backup di macchine virtuali nello stesso momento crea problemi di traffico.
**Preparazione dei backup** | Tenere conto del tempo di preparazione dei backup, che include l'installazione o l'aggiornamento dell'estensione di backup, nonché la generazione di uno snapshot in base alla pianificazione del backup.
**Trasferimento dei dati** | Tenere conto del tempo necessario al servizio di backup per calcolare le modifiche incrementali rispetto al backup precedente.<br/><br/> In un backup incrementale, per determinare le modifiche il servizio calcola il checksum del blocco. Se un blocco è cambiato, viene identificato per l'invio nell'insieme di credenziali. Il servizio esamina i blocchi identificati per tentare di ridurre ulteriormente i dati da trasferire. Dopo la valutazione di tutti blocchi modificati, le modifiche vengono trasferite nell'insieme di credenziali.<br/><br/> Può verificarsi un ritardo tra la creazione dello snapshot e la copia nell'insieme di credenziali.<br/><br/> Nei momenti di picco l'elaborazione dei backup può richiedere fino a otto ore. Il tempo di backup per una macchina virtuale sarà inferiore a 24 ore per il backup giornaliero.
**Backup iniziale** | Anche se il tempo totale di backup inferiore a 24 ore è un valore valido per i backup incrementali, potrebbe non esserlo per il primo backup. Il tempo necessario dipende dalla dimensione dei dati e dal momento in cui viene eseguito il backup.
**Coda di ripristino** | Backup di Azure elabora i processi di ripristino da più account di archiviazione contemporaneamente e le richieste di ripristino vengono inserite in una coda.
**Copia di ripristino** | Durante il processo di ripristino i dati vengono copiati dall'insieme di credenziali all'account di archiviazione.<br/><br/> Il tempo di ripristino dipende dal numero di operazioni di I/O al secondo e dalla velocità effettiva dell'account di archiviazione.<br/><br/> Per ridurre il tempo di copia, selezionare un account di archiviazione non impegnato con altre operazioni di lettura e scrittura di applicazioni.


### <a name="backup-performance"></a>Prestazioni del backup

Questi scenari comuni possono influire sul tempo necessario per il backup:

- Aggiunta di un nuovo disco a una macchina virtuale di Azure protetta: se una macchina virtuale è in fase di backup incrementale e viene aggiunto un nuovo disco, è possibile che il backup duri più di 24 ore a causa della replica iniziale del nuovo disco insieme alla replica differenziale dei dischi esistenti.
- Dischi frammentati: le operazioni di backup sono più rapide se le modifiche dei dischi sono nella stessa posizione. Se invece le modifiche sono distribuite e frammentate su un disco, il backup sarà più lento. 
- Varianza del disco: se dischi protetti sottoposti a backup incrementale hanno una varianza giornaliera di più di 200 GB, il backup può richiedere molto tempo (più di otto ore) per il completamento. 
- Versioni del servizio di backup: se si usa la versione più recente del servizio di backup (nota come Ripristino istantaneo), viene usato un processo più ottimizzato rispetto al confronto checksum per confrontare le modifiche. Se si usa la versione più recente e si è eliminato uno snapshot del backup, il servizio di backup userà il confronto checksum e l'operazione di backup richiederà più di 24 ore oppure avrà esito negativo.

## <a name="best-practices"></a>Procedure consigliate 
Quando si configurano i backup per le macchine virtuali, è consigliabile seguire queste procedure:

- Valutare l'opportunità di modificare l'ora di pianificazione predefinita impostata in un criterio. Se ad esempio l'ora predefinita per il criterio è impostata su 00.00, prendere in considerazione un incremento in minuti affinché le risorse vengano usate in modo ottimale.
- Per il backup di una macchina virtuale Premium senza la funzionalità Instant RP, viene allocato circa il 50% dello spazio totale dell'account di archiviazione. Il servizio Backup richiede questo spazio per copiare lo snapshot nello stesso account di archiviazione e trasferirlo nell'insieme di credenziali.
- Per il ripristino di macchine virtuali da un unico insieme di credenziali, è consigliabile usare diversi[account di archiviazione v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) per assicurarsi che non vengano applicate limitazioni all'account di archiviazione di destinazione. Ad esempio ogni macchina virtuale deve avere un account di archiviazione diverso (per il ripristino di 10 macchine virtuali è consigliabile usare 10 account di archiviazione diversi).
- I ripristini dal livello di archiviazione 1 (snapshot) verranno completati in pochi minuti (dato che si tratta dello stesso account di archiviazione) rispetto a quelli dal livello di archiviazione 2 (insieme di credenziali) che possono richiedere alcune ore. È consigliabile usare la funzionalità [Ripristino istantaneo](backup-instant-restore-capability.md) per ripristini più rapidi in caso i dati siano disponibili nel livello 1 (se i dati devono essere ripristinati dall'insieme di credenziali, sarà necessario un po' di tempo).
- Il limite sul numero di dischi per account di archiviazione dipende dalla modalità in cui le applicazioni in esecuzione nella macchina virtuale IaaS accedono ai dischi. Verificare se più dischi sono ospitati in un singolo account di archiviazione. Come regola generale, se sono presenti da 5 a 10 o più dischi in un solo account di archiviazione, bilanciare il carico spostando alcuni dischi in account di archiviazione separati.

## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

- La fatturazione inizia solo dopo il completamento corretto del primo backup. A questo punto inizia la fatturazione sia per le macchine virtuali di archiviazione sia per quelle protette.
- La fatturazione continua fino a quando per la macchina virtuale sono presenti dati di backup archiviati in un insieme di credenziali. Se si arresta la protezione per una macchina virtuale, ma sono presenti dati di backup della macchina virtuale in un insieme di credenziali, la fatturazione continuerà.
- La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati tutti i dati di backup.
- Quando si arresta la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'istanza protetta usata per la fatturazione mensile.
- Il calcolo delle istanze protette si basa sulla dimensione *effettiva* della macchina virtuale, ovvero sul totale dei dati presenti nella macchina virtuale, esclusa l'archiviazione temporanea.
- Si basano viceversa sui dati effettivi archiviati nel disco dati.
- I prezzi per il backup delle macchine virtuali non si basano sulla dimensione massima supportata per ogni disco dati collegato alla macchina virtuale.
- Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata in Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si consideri ad esempio una macchina virtuale di dimensioni A2 standard dotata di due dischi dati aggiuntivi con capacità massima di 4 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

**Disco** | **Dimensioni massime** | **Dati effettivi presenti**
--- | --- | ---
Disco del sistema operativo | 4095 GB | 17 GB 
Disco locale/temporaneo | 135 GB | 5 GB (non incluso per il backup) 
Disco dati 1 | 4095 GB | 30 GB 
Disco dati 2 | 4095 GB | 0 GB 

- In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB.
- La dimensione dell'istanza protetta (47 GB) diventa la base per la fattura mensile.
- Con l'aumentare della quantità di dati presenti nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.


## <a name="next-steps"></a>Passaggi successivi

[Preparare](backup-azure-arm-vms-prepare.md) ora il backup delle macchine virtuali di Azure.
