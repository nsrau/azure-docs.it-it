---
title: Informazioni sul backup di macchine virtuali di Azure
description: Informazioni sul backup di macchine virtuali di Azure e su alcune procedure consigliate.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: dacurwin
ms.openlocfilehash: 07faf03ee9b12d1bf4a200de47d6df714c2248d9
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737157"
---
# <a name="an-overview-of-azure-vm-backup"></a>Panoramica del backup delle macchine virtuali di Azure

Questo articolo descrive il modo in cui il [servizio backup di Azure](backup-introduction-to-azure-backup.md) esegue il backup di macchine virtuali (VM) di Azure.

## <a name="backup-process"></a>Processo di backup

Ecco come backup di Azure completa un backup per le macchine virtuali di Azure:

1. Per le macchine virtuali di Azure selezionate per il backup, backup di Azure avvia un processo di backup in base alla pianificazione del backup specificata.
1. Durante il primo backup, un'estensione di backup viene installata nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, viene installata l'estensione _VMSnapshot_ .
    - Per le macchine virtuali Linux, è installata l'estensione _VMSnapshotLinux_ .
1. Per le macchine virtuali Windows in esecuzione, le coordinate di backup con Windows Servizio Copia Shadow del volume (VSS) per creare uno snapshot coerente con l'app della macchina virtuale.
    - Per impostazione predefinita, il backup esegue backup VSS completi.
    - Se il backup non è in grado di eseguire uno snapshot coerente con l'app, acquisisce uno snapshot coerente con il file dell'archiviazione sottostante, perché non vengono eseguite Scritture dell'applicazione durante l'arresto della macchina virtuale.
1. Per le macchine virtuali Linux, il backup esegue un backup coerente con i file. Per gli snapshot coerenti con l'app, è necessario personalizzare manualmente gli script pre/post.
1. Al termine dello snapshot, il backup trasferisce i dati all'insieme di credenziali.
    - Il backup viene ottimizzato eseguendo il backup di ogni disco della macchina virtuale in parallelo.
    - Per ogni disco di cui viene eseguito il backup, backup di Azure legge i blocchi sul disco e identifica e trasferisce solo i blocchi di dati modificati (il Delta) rispetto al backup precedente.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Il tempo totale di backup per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
 1. Le modifiche apportate a una macchina virtuale Windows dopo l'abilitazione di backup di Azure sono:
    -   Microsoft Visual C++ 2013 Redistributable (x64)-12.0.40660 è installato nella macchina virtuale
    -   Il tipo di avvio del servizio Copia Shadow del volume (VSS) è stato modificato in automatico da manuale
    -   Il servizio Windows IaaSVmProvider è stato aggiunto

1. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Crittografia dei backup delle macchine virtuali di Azure

Quando si esegue il backup di macchine virtuali di Azure con Backup di Azure, viene eseguita la crittografia dei dati inattivi delle macchine virtuali con la crittografia del servizio di archiviazione. Backup di Azure può anche eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure.

**Crittografia** | **Dettagli** | **Supporto**
--- | --- | ---
**Azure Disk Encryption** | Crittografia dischi di Azure crittografa i dischi dati e del sistema operativo per le macchine virtuali di Azure.<br/><br/> Crittografia dischi di Azure si integra con le chiavi di crittografia di BitLocker (BEKs), che vengono protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra inoltre con Azure Key Vault chiavi di crittografia della chiave (KEKs). | Backup di Azure supporta il backup di macchine virtuali di Azure gestite e non gestite crittografate solo con BEKs o con BEKs insieme a KEKs.<br/><br/> Viene eseguito il backup e la crittografia sia di BEKs che di KEKs.<br/><br/> Poiché viene eseguito il backup di KEKs e BEKs, gli utenti con le autorizzazioni necessarie possono ripristinare chiavi e segreti nell'insieme di credenziali delle chiavi, se necessario. Questi utenti possono anche ripristinare la macchina virtuale crittografata.<br/><br/> Le chiavi e i segreti crittografati non possono essere letti da utenti non autorizzati o da Azure.
**Crittografia del servizio di archiviazione** | Con SSE, archiviazione di Azure offre la crittografia dei dati inattivi crittografando automaticamente i dati prima di archiviarli. Archiviazione di Azure decrittografa anche i dati prima di recuperarli. | Backup di Azure usa SSE per la crittografia inattiva delle VM di Azure.

Per le macchine virtuali di Azure gestite e non gestite, il backup supporta le macchine virtuali crittografate solo con BEKs o le VM crittografate con BEKs insieme a KEKs.

I BEKs (segreti) e KEKs (chiavi) di cui è stato eseguito il backup sono crittografati. Possono essere letti e usati solo quando vengono ripristinati nell'insieme di credenziali delle chiavi da parte di utenti autorizzati. Né utenti non autorizzati né Azure possono leggere o usare chiavi o segreti sottoposti a backup.

Viene anche eseguito il backup di BEKs. Quindi, se i BEKs vengono persi, gli utenti autorizzati possono ripristinare il BEKs nell'insieme di credenziali delle chiavi e recuperare le macchine virtuali crittografate. Solo gli utenti con il livello di autorizzazione necessario possono eseguire il backup e il ripristino di VM crittografate, chiavi e segreti.

## <a name="snapshot-creation"></a>Creazione di snapshot

Backup di Azure acquisisce gli snapshot in base alla pianificazione del backup.

- **Macchine virtuali Windows:** Per le macchine virtuali Windows, il servizio di backup coordina con VSS per eseguire uno snapshot coerente con l'app dei dischi delle macchine virtuali.

  - Per impostazione predefinita, Backup di Azure esegue backup VSS completi. [Altre informazioni](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
  - Per modificare l'impostazione in modo che backup di Azure accetti backup di copia VSS, impostare la chiave del registro di sistema seguente da un prompt dei comandi:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent"/v USEVSSCOPYBACKUP/t REG_SZ/d TRUE/f**

- **Macchine virtuali Linux:** Per eseguire snapshot coerenti con l'app di macchine virtuali Linux, usare il Framework di pre-script e post-script di Linux per scrivere script personalizzati per garantire la coerenza.

  - Backup di Azure richiama solo gli script pre/post scritti dall'utente.
  - Se gli script di pre e post-backup vengono eseguiti correttamente, backup di Azure contrassegna il punto di ripristino come coerente con l'applicazione. Tuttavia, quando si usano script personalizzati, si è responsabili della coerenza dell'applicazione.
  - [Altre](backup-azure-linux-app-consistent.md) informazioni su come configurare gli script.

### <a name="snapshot-consistency"></a>Coerenza degli snapshot

La tabella seguente illustra i diversi tipi di coerenza degli snapshot:

**Snapshot** | **Dettagli** | **Ripristino** | **Considerazioni**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con l'app usano un VSS writer (o script pre/post per Linux) per assicurare la coerenza dei dati dell'app prima che venga eseguito un backup. | Quando si esegue il ripristino di una macchina virtuale con uno snapshot coerente con l'app, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente. | Windows: tutti i VSS writer sono riusciti correttamente<br/><br/> Linux: gli script pre/post sono stati configurati e sono riusciti correttamente
**Coerente con il file System** | I backup coerenti con il file System garantiscono la coerenza eseguendo uno snapshot di tutti i file nello stesso momento.<br/><br/> | Quando si esegue il ripristino di una macchina virtuale con uno snapshot coerente con il file System, viene avviata la macchina virtuale. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti. | Windows: alcuni VSS writer non sono riusciti <br/><br/> Linux: Impostazione predefinita (se gli script pre/post non sono configurati o non riusciti)
**Coerenza con l'arresto anomalo del sistema** | Gli snapshot coerenti con l'arresto anomalo del sistema si verificano in genere se una macchina virtuale di Azure si arresta al momento del backup. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup.<br/><br/> Un punto di ripristino coerente con l'arresto anomalo del sistema non garantisce la coerenza dei dati per il sistema operativo o l'app. | Sebbene non esistano garanzie, la macchina virtuale viene in genere avviata e quindi avvia un controllo del disco per correggere gli errori di danneggiamento. Tutti i dati in memoria o le operazioni di scrittura che non sono stati trasferiti sul disco prima dell'arresto anomalo vengono persi. Le app implementano una propria procedura di verifica dei dati. Ad esempio, un'app di database può utilizzare il log delle transazioni per la verifica. Se il log delle transazioni contiene voci che non sono presenti nel database, il software del database esegue il rollback delle transazioni fino a quando i dati non sono coerenti. | Macchina virtuale in stato di arresto

## <a name="backup-and-restore-considerations"></a>Considerazioni su backup e ripristino

**Considerazioni** | **Dettagli**
--- | ---
**Disco** | Il backup dei dischi delle macchine virtuali è parallelo. Se, ad esempio, una macchina virtuale dispone di quattro dischi, il servizio di backup tenterà di eseguire il backup di tutti e quattro i dischi in parallelo. Il backup è incrementale (solo i dati modificati).
**Pianificazione** |  Per ridurre il traffico di backup, eseguire il backup di macchine virtuali diverse in momenti diversi del giorno e verificare che i tempi non si sovrappongano. Il backup di macchine virtuali nello stesso momento crea problemi di traffico.
**Preparazione dei backup** | Tenere presente il tempo necessario per preparare il backup. Il tempo di preparazione include l'installazione o l'aggiornamento dell'estensione di backup e l'attivazione di uno snapshot in base alla pianificazione del backup.
**Trasferimento dei dati** | Prendere in considerazione il tempo necessario per il backup di Azure per identificare le modifiche incrementali dal backup precedente.<br/><br/> In un backup incrementale, backup di Azure determina le modifiche calcolando il checksum del blocco. Se viene modificato un blocco, questo viene contrassegnato per il trasferimento all'insieme di credenziali. Il servizio analizza i blocchi identificati per tentare di ridurre ulteriormente la quantità di dati da trasferire. Dopo aver valutato tutti i blocchi modificati, backup di Azure trasferisce le modifiche nell'insieme di credenziali.<br/><br/> Può verificarsi un ritardo tra la creazione dello snapshot e la copia nell'insieme di credenziali.<br/><br/> In momenti di picco, possono essere necessarie fino a otto ore per l'elaborazione dei backup. Il tempo di backup per una macchina virtuale sarà inferiore a 24 ore per il backup giornaliero.
**Backup iniziale** | Sebbene il tempo totale di backup per i backup incrementali sia inferiore a 24 ore, questo potrebbe non essere il caso del primo backup. Il tempo necessario per il backup iniziale dipenderà dalla dimensione dei dati e dal momento in cui viene elaborato il backup.
**Coda di ripristino** | Backup di Azure elabora i processi di ripristino da più account di archiviazione contemporaneamente e inserisce le richieste di ripristino in una coda.
**Copia di ripristino** | Durante il processo di ripristino i dati vengono copiati dall'insieme di credenziali all'account di archiviazione.<br/><br/> Il tempo totale di ripristino dipende dal numero di operazioni di I/O al secondo (IOPS) e dalla velocità effettiva dell'account di archiviazione.<br/><br/> Per ridurre il tempo di copia, selezionare un account di archiviazione non impegnato con altre operazioni di lettura e scrittura di applicazioni.

### <a name="backup-performance"></a>Prestazioni del backup

Questi scenari comuni possono influire sul tempo di backup totale:

- **Aggiunta di un nuovo disco a una macchina virtuale di Azure protetta:** Se una macchina virtuale è in fase di backup incrementale e viene aggiunto un nuovo disco, il tempo di backup aumenterà. Il tempo di backup totale potrebbe durare più di 24 ore a causa della replica iniziale del nuovo disco, insieme a delta replication di dischi esistenti.
- **Dischi frammentati:** Le operazioni di backup sono più veloci quando le modifiche del disco sono contigue. Se invece le modifiche sono distribuite e frammentate su un disco, il backup sarà più lento.
- **Varianza del disco:** Se i dischi protetti di cui è in corso il backup incrementale hanno una varianza giornaliera superiore a 200 GB, il backup può richiedere molto tempo (più di otto ore) per il completamento.
- **Versioni di backup:** La versione più recente di backup, nota come versione di ripristino istantaneo, usa un processo più ottimizzato rispetto al confronto tra checksum per l'identificazione delle modifiche. Tuttavia, se si usa il ripristino istantaneo ed è stato eliminato uno snapshot di backup, il backup passa a checksum checksum (confronto checksum). In questo caso, l'operazione di backup avrà una maggiore o uguale a 24 ore.

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano i backup delle VM, è consigliabile seguire queste procedure:

- Modificare i tempi di pianificazione predefiniti impostati in un criterio. Se, ad esempio, l'ora predefinita nel criterio è 12:00 AM, incrementare l'intervallo di alcuni minuti in modo che le risorse vengano utilizzate in modo ottimale.
- Se si ripristinano le macchine virtuali da un unico insieme di credenziali, è consigliabile usare [account di archiviazione di uso generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) diversi per assicurarsi che l'account di archiviazione di destinazione non venga limitato. Ogni macchina virtuale, ad esempio, deve avere un account di archiviazione diverso. Se, ad esempio, vengono ripristinate 10 macchine virtuali, usare 10 account di archiviazione diversi.
- Per il backup delle macchine virtuali che usano archiviazione Premium, con il ripristino immediato è consigliabile allocare lo spazio disponibile del *50%* dello spazio di archiviazione totale allocato, che è necessario **solo** per il primo backup. Il 50% di spazio disponibile non è un requisito per i backup dopo il completamento del primo backup
- Il ripristino da un livello di archiviazione V1 per utilizzo generico (snapshot) verrà completato in pochi minuti perché lo snapshot si trova nello stesso account di archiviazione. Il ripristino dal livello di archiviazione per utilizzo generico V2 (Vault) può richiedere ore. Nei casi in cui i dati sono disponibili nell'archiviazione per utilizzo generico V1, è consigliabile usare la funzionalità di [ripristino immediato](backup-instant-restore-capability.md) per i ripristini più veloci. Se è necessario ripristinare i dati da un insieme di credenziali, il tempo necessario è maggiore.
- Il limite per il numero di dischi per account di archiviazione è relativo alla frequenza di accesso ai dischi da parte delle applicazioni in esecuzione in una macchina virtuale IaaS (Infrastructure as a Service). Come procedura generale, se in un singolo account di archiviazione sono presenti 5-10 o più dischi, bilanciare il carico spostando alcuni dischi in account di archiviazione separati.

## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

La fatturazione non viene avviata fino al completamento del primo backup completato. A questo punto inizia la fatturazione sia per le macchine virtuali di archiviazione sia per quelle protette. La fatturazione continua fino a quando i dati di backup per la macchina virtuale vengono archiviati in un insieme di credenziali. Se si arresta la protezione per una macchina virtuale, ma sono presenti dati di backup della macchina virtuale in un insieme di credenziali, la fatturazione continuerà.

La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati i dati di backup. Quando si arresta la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'istanza protetta usata per la fatturazione mensile.

Il calcolo delle dimensioni dell'istanza protetta è basato sulle dimensioni effettive della macchina virtuale. Le dimensioni della VM corrispondono alla somma di tutti i dati della macchina virtuale, esclusa l'archiviazione temporanea. I prezzi sono basati sui dati effettivi archiviati nei dischi dati, non sulle dimensioni massime supportate per ogni disco dati collegato alla macchina virtuale.

Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviati in backup di Azure, ovvero la somma dei dati effettivi in ogni punto di ripristino.

Ad esempio, è consigliabile usare una macchina virtuale di dimensioni a2 con due dischi dati aggiuntivi con una dimensione massima di 4 TB. La tabella seguente mostra i dati effettivi archiviati in ciascuno di questi dischi:

**Disco** | **Dimensioni massime** | **Dati effettivi presenti**
--- | --- | ---
Disco sistema operativo | 4095 GB | 17 GB
Disco locale/temporaneo | 135 GB | 5 GB (non incluso per il backup)
Disco dati 1 | 4095 GB | 30 GB
Disco dati 2 | 4095 GB | 0 GB

In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB. Questa dimensione dell'istanza protetta (47 GB) diventa la base per la fattura mensile. Con la crescita della quantità di dati nella macchina virtuale, le dimensioni dell'istanza protetta utilizzate per la fatturazione cambiano.

## <a name="next-steps"></a>Passaggi successivi

Preparare ora il [backup delle VM di Azure](backup-azure-arm-vms-prepare.md).
