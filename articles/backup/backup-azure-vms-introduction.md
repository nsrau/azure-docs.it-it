---
title: Informazioni sul backup di macchine virtuali di Azure
description: In questo articolo viene illustrato come il servizio Backup di Azure esegue il backup delle macchine virtuali di Azure e come seguire le procedure consigliate.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415974"
---
# <a name="an-overview-of-azure-vm-backup"></a>Panoramica del backup della macchina virtuale di AzureAn overview of Azure VM backup

Questo articolo descrive come il servizio Backup di Azure esegue il backup delle macchine virtuali (VM) di Azure.This article describes how the [Azure Backup service](backup-introduction-to-azure-backup.md) backs up Azure virtual machines (VMs).

Backup di Azure fornisce backup indipendenti e isolati per evitare la distruzione involontaria dei dati nelle macchine virtuali. I backup vengono archiviati in un insieme di credenziali di Servizi di ripristino con la gestione predefinita dei punti di ripristino. La configurazione e il ridimensionamento sono semplici, i backup sono ottimizzati e puoi facilmente eseguire il ripristino in base alle esigenze.

Come parte del processo di backup, [viene creato](#snapshot-creation)uno snapshot e i dati vengono trasferiti all'insieme di credenziali di Servizi di ripristino senza alcun impatto sui carichi di lavoro di produzione. Lo snapshot fornisce diversi livelli di coerenza, come descritto [di seguito.](#snapshot-consistency)

Backup di Azure include inoltre offerte specializzate per carichi di lavoro di database come [SQL Server](backup-azure-sql-database.md) e [SAP HANA](sap-hana-db-about.md) che sono in grado di riconoscere il carico di lavoro, offrono 15 minuti di RPO (obiettivo del punto di ripristino) e consentono il backup e il ripristino di singoli database.

## <a name="backup-process"></a>Processo di backup

Ecco come Backup di Azure completa un backup per le macchine virtuali di Azure:Here's how Azure Backup completes a backup for Azure VMs:

1. Per le macchine virtuali di Azure selezionate per il backup, Backup di Azure avvia un processo di backup in base alla pianificazione di backup specificata.
1. Durante il primo backup, viene installata un'estensione di backup nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, viene installata [l'estensione VMSnapshot.For](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) Windows VMs, the VMSnapshot extension is installed.
    - Per le macchine virtuali Linux, viene installata [l'estensione VMSnapshotLinux.For](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) Linux VMs, the VMSnapshotLinux extension is installed.
1. Per le macchine virtuali Windows in esecuzione, Backup si coordina con il servizio Copia Shadow del volume (VSS) di Windows per creare uno snapshot coerente con l'app della macchina virtuale.
    - Per impostazione predefinita, Backup esegue backup VSS completi.
    - Se il backup non è in grado di creare uno snapshot coerente con l'app, crea uno snapshot coerente con il file dell'archiviazione sottostante (perché non viene eseguita alcuna scrittura dell'applicazione mentre la macchina virtuale viene arrestata).
1. Per le macchine virtuali Linux, Backup esegue un backup coerente con i file. Per gli snapshot coerenti con l'app, devi personalizzare manualmente gli script pre/post.
1. Dopo che Backup esegue lo snapshot, trasferisce i dati all'insieme di credenziali.
    - Il backup viene ottimizzato eseguendo il backup di ogni disco DELLA macchina virtuale in parallelo.
    - Per ogni disco di cui viene eseguito il backup, Backup di Azure legge i blocchi sul disco e identifica e trasferisce solo i blocchi di dati modificati (il delta) dal backup precedente.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Il tempo di backup totale per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
1. Le modifiche apportate a una macchina virtuale Windows dopo l'abilitazione di Backup di Azure sono:Changes made to a Windows VM after Azure Backup is enabled on it are:
    - Nella macchina virtuale è installato il file ridistribuibile (x64) di Microsoft Visual C
    - Tipo di avvio del servizio Copia Shadow del Volume (VSS) modificato in automatico da manuale
    - È stato aggiunto il servizio Windows IaaSVmProvider

1. Al termine del trasferimento dei dati, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Crittografia dei backup delle macchine virtuali di AzureEncryption of Azure VM backups

Quando si esegue il backup di macchine virtuali di Azure con Backup di Azure, viene eseguita la crittografia dei dati inattivi delle macchine virtuali con la crittografia del servizio di archiviazione. Azure Backup can also back up Azure VMs that are encrypted by using Azure Disk Encryption.

**Crittografia** | **Dettagli** | **Supporto**
--- | --- | ---
**Crittografia disco di AzureAzure Disk Encryption** | Azure Disk Encryption encrypts both OS and data disks for Azure VMs.<br/><br/> Crittografia disco di Azure si integra con le chiavi di crittografia BitLocker (BEK), che vengono salvaguardate in un insieme di credenziali delle chiavi come segreti. Crittografia disco di Azure si integra anche con le chiavi di crittografia della chiave (KEK) dell'insieme dei messaggi di credenziali delle chiavi di Azure.Azure Disk Encryption also integrates with Azure Key Vault key encryption keys encryption keys keys (KEKs). | Backup di Azure supporta il backup di macchine virtuali di Azure gestite e non gestite crittografate solo con BEK o con BEK insieme a KEK.<br/><br/> Sia i BEK che i KK vengono sottoposti a backup e crittografati.<br/><br/> Poiché viene eseguito il backup di CHIAVEek e BEK, gli utenti con le autorizzazioni necessarie possono ripristinare le chiavi e i segreti nell'insieme di credenziali delle chiavi, se necessario. Questi utenti possono anche ripristinare la macchina virtuale crittografata.<br/><br/> Le chiavi e i segreti crittografati non possono essere letti da utenti non autorizzati o da Azure.Encrypted keys and secrets can't be read by unauthorized users or by Azure.
**Crittografia del servizio di archiviazione** | Con SSE, Archiviazione di Azure offre la crittografia inattivi crittografando automaticamente i dati prima dell'archiviazione. Archiviazione di Azure decrittografa anche i dati prima di recuperarlo. | Azure Backup uses SSE for at-rest encryption of Azure VMs.

Per le macchine virtuali di Azure gestite e non gestite, Backup supporta entrambe le macchine virtuali crittografate solo con BEK o macchine virtuali crittografate con BEK insieme a KEK.

I BEK (segreti) di cui è stato eseguito il backup e le chiavi KEK (chiavi) sono crittografati. Possono essere letti e utilizzati solo quando vengono ripristinati nell'insieme di credenziali delle chiavi da utenti autorizzati. Né gli utenti non autorizzati, né Azure possono leggere o usare chiavi o segreti di cui è stato eseguito il backup.

BeK sono anche il backup. Pertanto, in caso di smarrimento dei BEK, gli utenti autorizzati possono ripristinare i BEK nell'insieme di credenziali delle chiavi e ripristinare le macchine virtuali crittografate. Solo gli utenti con il livello di autorizzazioni necessario possono eseguire il backup e il ripristino di macchine virtuali crittografate o chiavi e segreti.

## <a name="snapshot-creation"></a>Creazione di snapshot

Backup di Azure crea snapshot in base alla pianificazione del backup.

- **Macchine virtuali Windows:** Per le macchine virtuali Windows, il servizio Backup si coordina con VSS per creare uno snapshot coerente con l'app dei dischi delle macchine virtuali.  Per impostazione predefinita, Backup di Azure esegue un backup VSS completo (tronca i log dell'applicazione, ad esempio SQL Server, al momento del backup per ottenere il backup coerente a livello di applicazione).  Se si usa un database di SQL Server nel backup della macchina virtuale di Azure, è possibile modificare l'impostazione per eseguire un backup di copia VSS (per conservare i log). Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **Macchine virtuali Linux:** Per creare snapshot coerenti con le app delle macchine virtuali Linux, usare il framework di pre-script e post-script Linux per scrivere script personalizzati per garantire la coerenza.

  - Backup di Azure richiama solo gli script pre/post scritti dall'utente.
  - Se i pre-script e i post-script vengono eseguiti correttamente, Backup di Azure contrassegna il punto di ripristino come coerente con l'applicazione. Tuttavia, quando si utilizzano script personalizzati, si è in ultima analisi responsabili della coerenza dell'applicazione.
  - [Ulteriori informazioni](backup-azure-linux-app-consistent.md) su come configurare gli script.

## <a name="snapshot-consistency"></a>Coerenza degli snapshot

Nella tabella seguente vengono illustrati i diversi tipi di coerenza dello snapshot:

**Snapshot** | **Dettagli** | **Recupero** | **Considerazioni**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con l'app usano un writer VSS (o script precedenti/post per Linux) per garantire la coerenza dei dati dell'app prima che venga eseguito un backup. | Quando si ripristina una macchina virtuale con uno snapshot coerente con l'app, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente. | Windows: Tutti i writer VSS hanno avuto esito positivo<br/><br/> Linux: gli script pre/post sono configurati e hanno avuto esito positivo
**Coerente con il file system** | I backup coerenti con il file system garantiscono coerenza eseguendo un'istantanea di tutti i file contemporaneamente.<br/><br/> | Quando si ripristina una macchina virtuale con uno snapshot coerente del file system, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti. | Windows: Alcuni writer VSS non sono riusciti <br/><br/> Linux: predefinito (se gli script pre/post non sono configurati o non sono riusciti)
**Coerenza con l'arresto anomalo del sistema** | Gli snapshot compatibili con l'arresto anomalo dell'arresto in genere si verificano se una macchina virtuale di Azure viene arrestata al momento del backup. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup. | Inizia con il processo di avvio della macchina virtuale seguito da un controllo del disco per correggere gli errori di danneggiamento. Eventuali dati in memoria o operazioni di scrittura che non sono state trasferite su disco prima che l'arresto anomalo venga perso. Le app implementano una propria procedura di verifica dei dati. Ad esempio, un'app di database può usare il relativo log delle transazioni per la verifica. Se il log delle transazioni contiene voci che non sono nel database, il software di database esegue il rollback delle transazioni fino a quando i dati non sono coerenti. | Stato di arresto della macchina virtuale (arrestato/deallocato).

## <a name="backup-and-restore-considerations"></a>Considerazioni sul backup e il ripristino

**Considerazioni** | **Dettagli**
--- | ---
**Disco** | Il backup dei dischi delle macchine virtuali è parallelo. Ad esempio, se una macchina virtuale dispone di quattro dischi, il servizio Backup tenta di eseguire il backup di tutti e quattro i dischi in parallelo. Il backup è incrementale (solo i dati modificati).
**Pianificazione** |  Per ridurre il traffico di backup, eseguire il backup di macchine virtuali diverse in momenti diversi della giornata e assicurarsi che gli orari non si sovrappongano. Il backup di macchine virtuali nello stesso momento crea problemi di traffico.
**Preparazione dei backup** | Tenere presente il tempo necessario per preparare il backup. Il tempo di preparazione include l'installazione o l'aggiornamento dell'estensione di backup e l'attivazione di uno snapshot in base alla pianificazione del backup.
**Trasferimento dati** | Considerare il tempo necessario affinché Backup di Azure identifichi le modifiche incrementali del backup precedente.<br/><br/> In un backup incrementale, Backup di Azure determina le modifiche calcolando il checksum del blocco. Se un blocco viene modificato, viene contrassegnato per il trasferimento nel vault. Il servizio analizza i blocchi identificati per tentare di ridurre ulteriormente la quantità di dati da trasferire. Dopo aver valutato tutti i blocchi modificati, Backup di Azure trasferisce le modifiche nell'insieme di credenziali.<br/><br/> Può verificarsi un ritardo tra la creazione dello snapshot e la copia nell'insieme di credenziali.<br/><br/> Nelle ore di punta, l'elaborazione dei backup può richiedere fino a otto ore. Il tempo di backup per una macchina virtuale sarà inferiore a 24 ore per il backup giornaliero.
**Backup iniziale** | Anche se il tempo totale di backup per i backup incrementali è inferiore a 24 ore, questo potrebbe non essere il caso per il primo backup. Il tempo necessario per il backup iniziale dipenderà dalle dimensioni dei dati e dal momento in cui il backup viene elaborato.
**Coda di ripristino** | Backup di Azure elabora i processi di ripristino da più account di archiviazione contemporaneamente e inserisce le richieste di ripristino in una coda.
**Copia di ripristino** | Durante il processo di ripristino i dati vengono copiati dall'insieme di credenziali all'account di archiviazione.<br/><br/> Il tempo totale di ripristino dipende dalle operazioni di I/O al secondo (IOPS) e dalla velocità effettiva dell'account di archiviazione.<br/><br/> Per ridurre il tempo di copia, selezionare un account di archiviazione non impegnato con altre operazioni di lettura e scrittura di applicazioni.

### <a name="backup-performance"></a>Prestazioni del backup

Questi scenari comuni possono influire sul tempo di backup totale:These common scenarios can affect the total backup time:

- Aggiunta di un nuovo disco a una macchina virtuale di **Azure protetta:Adding a new** disk to a protected Azure VM: Se una macchina virtuale è sottoposta a backup incrementale e viene aggiunto un nuovo disco, il tempo di backup aumenterà. Il tempo totale di backup potrebbe durare più di 24 ore a causa della replica iniziale del nuovo disco, insieme alla replica delta dei dischi esistenti.
- **Dischi frammentati:** Le operazioni di backup sono più veloci quando le modifiche del disco sono contigue. Se invece le modifiche sono distribuite e frammentate su un disco, il backup sarà più lento.
- **Varianza del disco:** Se i dischi protetti sottoposti a backup incrementale hanno una varianza giornaliera superiore a 200 GB, il completamento del backup può richiedere molto tempo (più di otto ore).
- **Versioni di backup:** La versione più recente di Backup (nota come versione di ripristino immediato) utilizza un processo più ottimizzato rispetto al confronto dei checksum per identificare le modifiche. Ma se si utilizza Instant Restore e si è eliminato uno snapshot di backup, il backup passa al confronto dei checksum. In questo caso, l'operazione di backup supererà le 24 ore (o avrà esito negativo).

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano i backup delle macchine virtuali, è consigliabile seguire queste procedure:When you're configuring VM backups, we suggest following these practices:

- Modificare gli orari di pianificazione predefiniti impostati in un criterio. Ad esempio, se l'ora predefinita nel criterio è 12:00 AM, incrementare la temporizzazione di alcuni minuti in modo che le risorse vengano utilizzate in modo ottimale.
- Se si ripristinano macchine virtuali da un singolo insieme di credenziali, è consigliabile usare account di [archiviazione versione 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) diversi per assicurarsi che l'account di archiviazione di destinazione non venga limitato. Ad esempio, ogni macchina virtuale deve avere un account di archiviazione diverso. Ad esempio, se vengono ripristinate 10 macchine virtuali, usare 10 account di archiviazione diversi.
- Per il backup delle macchine virtuali che usano l'archiviazione Premium, con Ripristino immediato, è consigliabile allocare il *50%* di spazio libero dello spazio di archiviazione totale allocato, necessario **solo** per il primo backup. Il 50% di spazio libero non è un requisito per i backup al termine del primo backup
- Il limite per il numero di dischi per account di archiviazione è relativo al numero di accesso ai dischi da parte delle applicazioni in esecuzione in una macchina virtuale IaaS (Infrastructure as a Service). Come pratica generale, se sono presenti da 5 a 10 dischi o più in un singolo account di archiviazione, bilanciare il carico spostando alcuni dischi in account di archiviazione separati.

## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

La fatturazione non viene avviata fino al termine del primo backup riuscito. A questo punto inizia la fatturazione sia per le macchine virtuali di archiviazione sia per quelle protette. La fatturazione continua finché tutti i dati di backup per la macchina virtuale vengono archiviati in un insieme di credenziali. Se si arresta la protezione per una macchina virtuale, ma sono presenti dati di backup della macchina virtuale in un insieme di credenziali, la fatturazione continuerà.

La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati i dati di backup. Quando si arresta la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'istanza protetta usata per la fatturazione mensile.

Il calcolo delle dimensioni dell'istanza protetta si basa sulle dimensioni *effettive* della macchina virtuale. La dimensione della macchina virtuale è la somma di tutti i dati nella macchina virtuale, esclusa l'archiviazione temporanea. I prezzi si basano sui dati effettivi archiviati nei dischi dati, non sulla dimensione massima supportata per ogni disco dati collegato alla macchina virtuale.

Analogamente, la fattura di archiviazione dei backup si basa sulla quantità di dati archiviati in Backup di Azure, ovvero la somma dei dati effettivi in ogni punto di ripristino.

Ad esempio, si prenda una macchina virtuale di dimensioni A2-Standard con due dischi dati aggiuntivi con una dimensione massima di 32 TB ciascuno. La tabella seguente mostra i dati effettivi archiviati in ognuno di questi dischi:

**Disco** | **Dimensioni massime** | **Dati effettivi presenti**
--- | --- | ---
Disco del sistema operativo | 32 TB | 17 GB
Disco locale/temporaneo | 135 GB | 5 GB (non incluso per il backup)
Disco dati 1 | 32 TB| 30 GB
Disco dati 2 | 32 TB | 0 GB

In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB. Questa dimensione dell'istanza protetta (47 GB) diventa la base per la fattura mensile. Con l'aumentare della quantità di dati nella macchina virtuale, le dimensioni dell'istanza protetta usate per la fatturazione cambiano in modo che corrispondano.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, [preparare il backup della macchina virtuale di Azure.Now, prepare for Azure VM backup](backup-azure-arm-vms-prepare.md).
