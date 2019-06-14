---
title: Informazioni sul backup di macchine virtuali di Azure
description: Informazioni sul backup di macchine virtuali di Azure e su alcune procedure consigliate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 93be913182db56941c346ef0cad47f70c0d614c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706840"
---
# <a name="about-azure-vm-backup"></a>Informazioni sul backup di macchine virtuali di Azure

Questo articolo descrive come la [servizio Backup di Azure](backup-introduction-to-azure-backup.md) esegue il backup di macchine virtuali di Azure (VM).

## <a name="backup-process"></a>Processo di backup

Ecco come Backup di Azure esegue un backup per macchine virtuali di Azure:

1. Per le VM di Azure che sono selezionati per il backup, Backup di Azure avvia un processo di backup in base alla pianificazione di backup specificata.
1. Durante il primo backup, un'estensione di backup viene installata nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, il _VMSnapshot_ estensione viene installata.
    - Per le macchine virtuali Linux, il _VMSnapshotLinux_ estensione viene installata.
1. Per le macchine virtuali Windows in esecuzione, Backup coordina con Windows Volume Copia Shadow Service (VSS) per creare un snapshot coerenti con l'app della macchina virtuale.
    - Per impostazione predefinita, Backup esegue backup VSS completi.
    - Se il Backup non è possibile richiedere uno snapshot coerente con l'app, viene eseguito uno snapshot coerenti con i file della risorsa di archiviazione sottostante (poiché mentre la VM viene arrestata, si verifica alcuna scrittura di applicazione).
1. Per le macchine virtuali Linux, Backup esegue un backup coerenti con i file. Per gli snapshot coerenti con l'app, è necessario personalizzare manualmente gli script di pre/post.
1. Dopo il Backup ha acquisito lo snapshot, di trasferire i dati nell'insieme di credenziali.
    - Il backup viene ottimizzato attraverso il backup di ogni disco della macchina virtuale in parallelo.
    - Per ogni disco che viene eseguito il backup, Backup di Azure legge i blocchi del disco e identifica e trasferisce solo i blocchi di dati modificati (delta) dal backup precedente.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Tempo totale di backup per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
 1. Le modifiche apportate a una VM di Windows dopo che è abilitato il Backup di Azure sono:
    -   Microsoft Visual C++ 2013 Redistributable (x64) - 12.0.40660 viene installato nella macchina virtuale
    -   Tipo di avvio del servizio Copia Shadow del Volume (VSS) modificato in automatico da manuale
    -   Viene aggiunto per servizio IaaSVmProvider Windows

1. Una volta completato il trasferimento dei dati, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Crittografia dei backup di macchine Virtuali di Azure

Quando si esegue il backup di macchine virtuali di Azure con Backup di Azure, viene eseguita la crittografia dei dati inattivi delle macchine virtuali con la crittografia del servizio di archiviazione. Backup di Azure può anche eseguire il backup di macchine virtuali di Azure che vengono crittografati usando crittografia dischi di Azure.

**Crittografia** | **Dettagli** | **Supporto**
--- | --- | ---
**Azure Disk Encryption** | Crittografia dischi di Azure consente di crittografare i dischi del sistema operativo sia i dati per le macchine virtuali di Azure.<br/><br/> Crittografia dischi di Azure si integra con le chiavi di crittografia di BitLocker (BEKs), che sono protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra anche con le chiavi di crittografia della chiave di Azure Key Vault (scambio). | Backup di Azure supporta il backup di macchine virtuali Azure gestite e crittografate BEKs solo o con BEKs insieme a scambio.<br/><br/> BEKs sia scambio e è sottoposti a backup e crittografati.<br/><br/> Poiché scambio e BEKs viene eseguito il backup, gli utenti con le autorizzazioni necessarie possono ripristinare le chiavi e segreti nuovamente l'insieme di credenziali delle chiavi se necessario. Questi utenti possono anche ripristinare la macchina virtuale crittografata.<br/><br/> Impossibile leggere i segreti e chiavi crittografate da utenti non autorizzati o da Azure.
**Crittografia del servizio di archiviazione** | Con la crittografia SSE, archiviazione di Azure fornisce la crittografia a riposo grazie alla crittografia automatica dei dati prima dell'archiviazione. Archiviazione di Azure, inoltre, decrittografa i dati prima di recuperarli. | Backup di Azure Usa la crittografia lato server per la crittografia dei dati inattivi delle macchine virtuali di Azure.

Per le VM di Azure gestiti e non gestiti, Backup supporta sia le macchine virtuali crittografate solo con BEKs o le macchine virtuali crittografate con BEKs insieme a scambio.

Il backup BEKs (segreti) e scambio (chiavi) viene crittografati. Possono essere letti e usati solo quando si è ripristinati al key vault dagli utenti autorizzati. Gli utenti non autorizzati né Azure può leggere oppure usare il backup chiavi o segreti.

BEKs viene anche eseguito il backup. Pertanto, se il BEKs vengono perse, gli utenti autorizzati possono ripristinare il BEKs all'insieme di credenziali chiave e recuperare le macchine virtuali crittografate. Solo gli utenti con il livello di autorizzazioni necessario possono eseguire backup e ripristino crittografate le macchine virtuali o le chiavi e segreti.

## <a name="snapshot-creation"></a>Creazione di snapshot

Backup di Azure esegue gli snapshot in base alla pianificazione del backup.

- **Macchine virtuali Windows:** Per le macchine virtuali Windows, il servizio di Backup si coordina con VSS per creare un snapshot coerenti con l'app dei dischi delle macchine Virtuali.

  - Per impostazione predefinita, Backup di Azure esegue backup VSS completi. [Altre informazioni](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
  - Per modificare l'impostazione in modo che il Backup di Azure i backup VSS di copia, impostare la chiave del Registro di sistema seguente da un prompt dei comandi:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Macchine virtuali Linux:** Per sfruttare gli snapshot coerenti con l'app delle macchine virtuali Linux, usare lo pre-script di Linux e framework per scrivere i propri script personalizzati per garantire la coerenza di post-script.

  - Backup di Azure richiama solo gli script di pre/post scritti dall'utente.
  - Se gli script pre e post-script eseguiti correttamente, Azure Backup contrassegna il punto di ripristino come coerente con l'applicazione. Tuttavia, quando si usa script personalizzati, si è responsabili della coerenza dell'applicazione.
  - [Altre informazioni](backup-azure-linux-app-consistent.md) su come configurare gli script.

### <a name="snapshot-consistency"></a>Coerenza degli snapshot

La tabella seguente illustra i diversi tipi di coerenza snapshot:

**Snapshot** | **Dettagli** | **Ripristino** | **Considerazioni**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Snapshot coerenti con l'App Usa un writer VSS (o gli script di pre/post per Linux) per garantire la coerenza dei dati dell'app prima che venga eseguita una copia di backup. | Quando si esegue il ripristino di una macchina virtuale con uno snapshot coerente con l'app, la macchina virtuale viene avviato. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente. | Windows: tutti i VSS writer sono riusciti correttamente<br/><br/> Linux: gli script pre/post sono stati configurati e sono riusciti correttamente
**Coerente con il file system** | Backup coerenti con file system per fornire coerenza creare uno snapshot di tutti i file nello stesso momento.<br/><br/> | Quando si esegue il ripristino di una macchina virtuale con uno snapshot coerente con file system, la macchina virtuale viene avviato. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti. | Windows: alcuni VSS writer non sono riusciti <br/><br/> Linux: Predefinito (se gli script di pre/post non sono configurati o non riusciti)
**Coerenza con l'arresto anomalo del sistema** | Snapshot coerenti con l'arresto anomalo del sistema in genere verificarsi se si arresta una macchina virtuale di Azure al momento del backup. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup.<br/><br/> Un punto di ripristino coerente con l'arresto anomalo del sistema non garantisce la coerenza dei dati per il sistema operativo o l'app. | Anche se non vi sono garanzie, la macchina virtuale viene in genere avviato, e quindi avvia una verifica del disco per risolvere errori di danneggiamento. Eventuali dati in memoria o le operazioni di scrittura che non sono state trasferite su disco prima dell'arresto anomalo vengono persi. Le app implementano una propria procedura di verifica dei dati. Ad esempio, un'app di database può usare relativo log delle transazioni per la verifica. Se il log delle transazioni contiene voci non presenti nel database, il software del database esegue il rollup transazioni indietro fino a quando i dati sono coerenti. | Macchina virtuale in stato di arresto

## <a name="backup-and-restore-considerations"></a>Considerazioni sul backup e ripristino

**Considerazioni** | **Dettagli**
--- | ---
**Disco** | Backup dei dischi delle macchine Virtuali è parallelo. Ad esempio, se una macchina virtuale contiene quattro dischi, il servizio di Backup tenta di eseguire il backup di tutti e quattro i dischi in parallelo. Il backup è incrementale (solo i dati modificati).
**Pianificazione** |  Per ridurre il traffico di backup, eseguire il backup di macchine virtuali diverse in momenti diversi del giorno e assicurarsi che i tempi non si sovrappongano. Il backup di macchine virtuali nello stesso momento crea problemi di traffico.
**Preparazione dei backup** | Tenere presente il tempo necessario per preparare il backup. Tempo di preparazione include l'installazione o l'aggiornamento dell'estensione di backup e generazione di uno snapshot in base alla pianificazione del backup.
**Trasferimento dei dati** | Prendere in considerazione il tempo necessario per il Backup di Azure identificare le modifiche incrementali dal backup precedente.<br/><br/> In un backup incrementale, Backup di Azure determina le modifiche calcolando i checksum del blocco. Se viene modificato un blocco, viene contrassegnato per il trasferimento nell'insieme di credenziali. Il servizio analizza blocchi identificati per tentare di ridurre la quantità di dati da trasferire. Dopo aver valutato tutti i blocchi modificati, Backup di Azure trasferisce le modifiche nell'insieme di credenziali.<br/><br/> Può verificarsi un ritardo tra la creazione dello snapshot e la copia nell'insieme di credenziali.<br/><br/> Nei periodi di picco, può richiedere fino a otto ore per i backup da elaborare. Il tempo di backup per una macchina virtuale sarà inferiore a 24 ore per il backup giornaliero.
**Backup iniziale** | Anche se il tempo di backup totale per i backup incrementali è inferiore a 24 ore, che potrebbe non essere il caso per il primo backup. Il tempo necessario per il backup iniziale verrà dipendono dalle dimensioni dei dati e quando viene elaborato il backup.
**Coda di ripristino** | Azure Backup esegue ripristini da più account di archiviazione nello stesso momento e inserisce le richieste di ripristino in una coda.
**Copia di ripristino** | Durante il processo di ripristino i dati vengono copiati dall'insieme di credenziali all'account di archiviazione.<br/><br/> Il tempo totale di ripristino varia a seconda di operazioni dei / o al secondo (IOPS) e la velocità effettiva dell'account di archiviazione.<br/><br/> Per ridurre il tempo di copia, selezionare un account di archiviazione non impegnato con altre operazioni di lettura e scrittura di applicazioni.

### <a name="backup-performance"></a>Prestazioni del backup

Questi scenari comuni possono influenzare l'ora di backup totale:

- **Aggiunta di un nuovo disco a una macchina virtuale di Azure protetta:** Se una macchina virtuale è in fase di backup incrementale e viene aggiunto un nuovo disco, si aumenterà il tempo di backup. Il tempo totale di backup potrà durare più di 24 ore a causa di replica iniziale del nuovo disco, con la replica differenziale dei dischi esistenti.
- **Frammentato dischi:** Operazioni di backup sono più veloci quando le modifiche ai dischi siano contigue. Se invece le modifiche sono distribuite e frammentate su un disco, il backup sarà più lento.
- **Varianza del disco:** Se i dischi che sono in fase di backup incrementale è protetto hanno una varianza giornaliera di più di 200 GB, backup può richiedere molto tempo (più di otto ore) per il completamento.
- **Versioni del backup:** La versione più recente del Backup (noto come la versione di ripristino istantaneo) usa un processo più ottimizzato confronto checksum per l'identificazione delle modifiche. Ma se si usa il ripristino immediato e sia eliminato uno snapshot di backup, backup passa a confronto checksum. In questo caso, l'operazione di backup verrà superare le 24 ore (o avere esito negativo).

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano i backup delle VM, è consigliabile seguire queste procedure:

- Modificare gli orari di pianificazione predefiniti che vengono impostati in un criterio. Ad esempio, se l'ora predefinita nei criteri è 12:00 AM, incrementare i tempi di alcuni minuti in modo che le risorse vengono usate in modo ottimale.
- Per il backup delle macchine virtuali che usano archiviazione premium, è consigliabile eseguire la versione più recente di Backup di Azure ([ripristino istantaneo](backup-instant-restore-capability.md)). Se non si esegue la versione più recente, il Backup consente di allocare circa 50% di spazio di archiviazione totale. Il servizio Backup richiede questo spazio per copiare lo snapshot per lo stesso account di archiviazione e di trasferirla nell'insieme di credenziali.
- Se si sta ripristinando le macchine virtuali da un unico insieme di credenziali, è consigliabile utilizzare differenti [gli account di archiviazione per utilizzo generico v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) per assicurarsi che l'account di archiviazione di destinazione non viene applicata la limitazione. Ad esempio, ogni macchina virtuale deve avere un account di archiviazione diverso. Ad esempio, se vengono ripristinate 10 macchine virtuali, usare 10 account di archiviazione diverso.
- I ripristini da un livello di archiviazione per utilizzo generico v1 (snapshot) verranno completati in pochi minuti perché lo snapshot è nello stesso account di archiviazione. Ripristini dal livello di archiviazione per utilizzo generico v2 (insieme di credenziali) possono richiedere ore. Nei casi in cui i dati sono disponibili in archiviazione per utilizzo generico v1, è consigliabile usare la [ripristino istantaneo](backup-instant-restore-capability.md) funzionalità per ripristini più rapidi. (Se è necessario ripristinare i dati da un insieme di credenziali, quindi richiederà più tempo.)
- Il limite sul numero di dischi per account di archiviazione è relativo alla frequenza di utilizzo dischi accessibili da applicazioni che sono in esecuzione in un'infrastruttura come servizio (IaaS) della macchina virtuale. Come regola generale, se sono presenti in un singolo account di archiviazione dischi da 5 a 10 o più, il bilanciamento del carico spostando alcuni dischi per separare gli account di archiviazione.

## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

La fatturazione non inizia fino al completamento del primo backup ha esito positivo. A questo punto inizia la fatturazione sia per le macchine virtuali di archiviazione sia per quelle protette. La fatturazione continua fino a quando i dati di backup per la macchina virtuale viene archiviati in un insieme di credenziali. Se si arresta la protezione per una macchina virtuale, ma sono presenti dati di backup della macchina virtuale in un insieme di credenziali, la fatturazione continuerà.

La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati i dati di backup. Quando si arresta la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'istanza protetta usata per la fatturazione mensile.

Calcolo delle dimensioni di istanze protette si basa sul *effettivi* le dimensioni della macchina virtuale. Le dimensioni della macchina virtuale sono la somma di tutti i dati nella macchina virtuale, escluso l'archiviazione temporanea. Prezzi sono basati sui dati effettivi che ha archiviato sui dischi dati, non su un massimo supportato dimensioni di ogni disco dati collegato alla macchina virtuale.

Analogamente, la fattura per l'archiviazione di backup è basata sulla quantità di dati archiviati in Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Ad esempio, richiedere una macchina virtuale con dimensioni A2-Standard che dispone di due dischi dati aggiuntivi con una dimensione massima di 4 TB. Nella tabella seguente mostra i dati effettivi archiviati in ciascuno di questi dischi:

**Disco** | **Dimensioni massime** | **Dati effettivi presenti**
--- | --- | ---
Disco del sistema operativo | 4095 GB | 17 GB
Disco locale/temporaneo | 135 GB | 5 GB (non incluso per il backup)
Disco dati 1 | 4095 GB | 30 GB
Disco dati 2 | 4095 GB | 0 GB

In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB. Dimensione dell'istanza protetta (47 GB) diventa la base per la fatturazione mensile. Man mano che aumenta la quantità di dati nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano in modo che corrispondano.

## <a name="next-steps"></a>Passaggi successivi

A questo punto [preparare per il backup di macchine Virtuali di Azure](backup-azure-arm-vms-prepare.md).
