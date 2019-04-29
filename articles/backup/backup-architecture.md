---
title: Architettura di Backup di Azure
description: Panoramica dell'architettura, dei componenti e dei processi usati dal servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647406"
---
# <a name="azure-backup-architecture"></a>Architettura di Backup di Azure

È possibile usare la [servizio Backup di Azure](backup-overview.md) per eseguire il backup dei dati alla piattaforma cloud di Microsoft Azure. Questo articolo riepiloga l'architettura, i componenti e i processi di Backup di Azure. 

## <a name="what-does-azure-backup-do"></a>Che cosa fa Backup di Azure?

Backup di Azure esegue il backup di dati, lo stato della macchina e carichi di lavoro in esecuzione su macchine virtuali locali e istanze di macchina virtuale di Azure (VM). Esistono diversi scenari di Backup di Azure.

## <a name="how-does-azure-backup-work"></a>Come funziona Backup di Azure?

È possibile eseguire il backup di macchine e dati usando diversi metodi:

- **Backup di computer locali**:
    - È possibile eseguire il backup di macchine virtuali Windows locali direttamente in Azure usando l'agente Azure Backup Microsoft Azure Recovery Services (MARS). I computer Linux non sono supportati.
    - È possibile eseguire il backup di macchine virtuali locali a un server di backup (System Center Data Protection Manager (DPM) o Microsoft Azure Backup Server (MABS)). È quindi possibile eseguire il backup del server di backup per un insieme di credenziali di servizi di ripristino in Azure.

- **Backup delle macchine virtuali di Azure**:
    - È possibile eseguire il backup di macchine virtuali di Azure direttamente. Backup di Azure installa un'estensione di backup per l'agente di macchine Virtuali di Azure in cui è in esecuzione nella macchina virtuale. Questa estensione esegue il backup dell'intera macchina virtuale.
    - È possibile eseguire il backup di cartelle e file specifici nella macchina virtuale di Azure eseguendo l'agente MARS.
    - È possibile eseguire il backup di macchine virtuali di Azure per il backup di Microsoft AZURE è in esecuzione in Azure e puoi quindi eseguire il backup di MABS per un insieme di credenziali di servizi di ripristino.

Altre informazioni sulle [ciò che è possibile eseguire il backup](backup-overview.md) e circa [scenari di backup supportati](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Dove viene eseguito il backup dei dati?

Backup di Azure archivia i dati di backup in un insieme di credenziali di servizi di ripristino. Un insieme di credenziali è un'entità di archiviazione online in Azure, che viene usato per contenere dati, ad esempio copie di backup, i punti di ripristino e i criteri di backup.

Gli insiemi di credenziali di Recovery Services hanno le caratteristiche seguenti:

- Gli insiemi di credenziali semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.
- In ogni sottoscrizione di Azure è possibile creare fino a 500 insiemi di credenziali.
- È possibile monitorare gli elementi di backup in un insieme di credenziali, tra cui le macchine locali e macchine virtuali di Azure.
- È possibile gestire l'accesso dell'insieme di credenziali tramite il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di Azure.
- È necessario specificare come vengono replicati i dati nell'insieme di credenziali per la ridondanza:
    - **Archiviazione con ridondanza locale**. Per proteggersi da errori in un Data Center, è possibile usare l'archiviazione con ridondanza locale. L'archiviazione con ridondanza locale replica i dati in un'unità di scala di archiviazione. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
    - **Archiviazione con ridondanza geografica**: Per evitare interruzioni a livello di area, è possibile usare l'archiviazione con ridondanza geografica. Archiviazione con ridondanza geografica replica i dati in un'area secondaria. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) 
    - Per impostazione predefinita, gli insiemi di credenziali di servizi di ripristino usano l'archiviazione con ridondanza geografica. 

## <a name="backup-agents"></a>Agenti di backup

Backup di Azure offre diversi agenti di backup, a seconda del tipo di computer sottoposti a backup:

**Agent** | **Dettagli** 
--- | --- 
**Agente di MARS** | <ul><li>Viene eseguito nei computer Windows Server singoli in locale per eseguire il backup di file, cartelle e lo stato del sistema.</li> <li>Viene eseguito in macchine virtuali di Azure per eseguire il backup di file, cartelle e lo stato del sistema.</li> <li>Viene eseguito nei server DPM/MABS per eseguire il backup del disco di archiviazione locale di DPM/MABS in Azure.</li></ul> 
**Estensione per le macchine virtuali di Azure** | Viene eseguito in macchine virtuali di Azure per eseguirne il backup in un insieme di credenziali.

## <a name="backup-types"></a>Tipi di backup

La tabella seguente illustra i diversi tipi di backup e quando vengono utilizzati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Completo** | Un backup completo contiene l'intera origine dati. Richiede maggiore larghezza di banda di rete rispetto ai backup incrementale o differenziale. | Usato per il backup iniziale.
**Differenziale** |  Un backup differenziale archivia i blocchi modificati dopo il backup completo iniziale. Usa una quantità inferiore di rete e archiviazione e non mantenere copie ridondanti dei dati non modificati.<br/><br/> Non è efficiente poiché i blocchi di dati invariati tra i backup successivi vengono trasferiti e archiviati. | Non è usato da Backup di Azure.
**Incrementale** | Un backup incrementale archivia solo i blocchi di dati modificati dal backup precedente. Efficienza elevata per rete e archiviazione. <br/><br/> Con i backup incrementali, non è necessario integrare con backup completi. | Usato da DPM/MABS per i backup su disco e usato in tutti i backup in Azure.

## <a name="sql-server-backup-types"></a>Tipi di backup di SQL Server

La tabella seguente illustra i diversi tipi di backup usati per i database di SQL Server e con quale frequenza vengono utilizzati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Backup completo** | un backup completo è un backup eseguito per l'intero database. Contiene tutti i dati in un database specifico o in un set di file o filegroup. Un backup completo contiene anche log sufficiente per ripristinare i dati. | Al massimo, è possibile attivare un backup completo al giorno.<br/><br/> È possibile scegliere di eseguire un backup completo su un intervallo giornaliero o settimana.
**Backup differenziale** | Un backup differenziale si basa sul backup di dati completa più recente, precedente.<br/><br/> Acquisisce solo i dati che sono stati modificati dopo il backup completo. |  Al massimo, è possibile attivare un backup differenziale al giorno.<br/><br/> Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
**Backup del log delle transazioni** | un backup del log consente il ripristino temporizzato fino a uno specifico secondo. | Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

### <a name="comparison-of-backup-types"></a>Confronto tra i tipi di backup

L'utilizzo dell'archiviazione, l'obiettivo del tempo di ripristino (RTO) e l'utilizzo della rete variano per ogni tipo di backup. L'immagine seguente mostra un confronto tra i tipi di backup:

- Origine dati è costituito da 10 blocchi di archiviazione, A1-A10, che vengono eseguito il backup mensile.
- I blocchi A2, A3, A4 e A9 cambiano nel primo mese, mentre il blocco A5 cambia il mese successivo.
- Per i backup differenziali, nel secondo mese viene eseguito il backup dei blocchi modificati A2, A3, A4 e A9. Nel terzo mese, viene eseguito nuovamente il backup di questi stessi blocchi, insieme al blocco A5 modificato. Il backup continua a essere eseguito per i blocchi modificati fino al backup completo successivo.
- Per i backup incrementali, nel secondo mese, i blocchi A2, A3, A4 e A9 vengono contrassegnati come modificati e trasferiti. Nel terzo mese, viene contrassegnato e trasferito solo il blocco A5 modificato. 

![Immagine che mostra i confronti dei metodi di backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funzionalità di backup

La tabella seguente riepiloga le funzionalità supportate per i diversi tipi di backup:

**Funzionalità** | **Macchine Windows Server in locale (direct)** | **Macchine virtuali di Azure** | **Macchine o le app con DPM/MABS**
--- | --- | --- | ---
Backup nell'insieme di credenziali | ![Sì][green] | ![Sì][green] | ![Sì][green] 
Eseguire il backup su disco DPM/MABS, quindi ad Azure | | | ![Sì][green] 
Compressione dei dati inviati per il backup | ![Sì][green] | Durante il trasferimento dei dati non viene usata alcuna compressione. Leggero aumento dello spazio di archiviazione richiesto, ma ripristino più veloce.  | ![Sì][green] 
Backup incrementale |![Sì][green] |![Sì][green] |![Sì][green] 
Backup di dischi deduplicati | | | ![Parzialmente][yellow]<br/><br/> Solo per i server DPM/MABS distribuiti in locale. 

![Chiave della tabella](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architettura: backup diretto di macchine virtuali di Azure

1. Quando si abilita il backup per una macchina virtuale di Azure, viene eseguito un backup in base alla pianificazione specificata.
1. Durante il primo backup, un'estensione di backup viene installata nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, viene installato l'estensione VMSnapshot.
    - Per le macchine virtuali Linux, è installata l'estensione VMSnapshot Linux.
1. L'estensione crea uno snapshot a livello di archiviazione. 
    - Per le macchine virtuali Windows in esecuzione, Backup coordina con il Windows Volume servizio Copia Shadow (VSS) per creare un snapshot coerenti con l'app della macchina virtuale. Per impostazione predefinita, Backup esegue backup VSS completi. Se Backup di Azure non riesce a creare uno snapshot coerente con l'app, acquisisce uno snapshot coerente con i file.
    - Per le macchine virtuali Linux, Backup di uno snapshot coerenti con i file. Per gli snapshot coerenti con l'app, è necessario personalizzare manualmente gli script di pre/post.
    - Il backup viene ottimizzato eseguendo in parallelo il backup di ogni disco di macchina virtuale. Per ogni disco di cui si esegue il backup, Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati. 
1. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali. 
    - Solo i blocchi di dati che è stato modificato dopo l'ultimo backup vengono copiati.
    - I dati non vengono crittografati. Backup di Azure può eseguire il backup di macchine virtuali di Azure che sono state crittografate tramite crittografia dischi di Azure.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Nei periodi di picco, il backup potrebbe richiedere alcune ore. Tempo totale di backup per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
1. Dopo che i dati vengono inviati all'insieme di credenziali, lo snapshot viene rimosso e viene creato un punto di ripristino.

Macchine virtuali di Azure è necessario l'accesso a internet per i comandi del controllo. Se si esegue il backup dei carichi di lavoro all'interno della VM (ad esempio, backup di database di SQL Server), i dati di back-end devono anche l'accesso a internet. 

![Backup di macchine virtuali di Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architettura: Eseguire direttamente backup di macchine virtuali Windows Server locali o macchine Virtuali di Azure file o cartelle

1. Per configurare lo scenario, scaricare e installare l'agente MARS nel computer. Quindi possibile selezionare quali eseguire il backup, quando verranno eseguiti i backup e il tempo sarà rimanere in Azure.
1. Viene eseguito il backup iniziale in base alle impostazioni di backup.
1. L'agente MARS utilizza VSS per creare uno snapshot temporizzato dei volumi selezionati per il backup.
    - L'agente MARS Usa solo l'operazione di scrittura del sistema Windows per acquisire lo snapshot.
    - Perché l'agente non usa alcun writer VSS dell'applicazione, non acquisisce snapshot coerenti con l'app.
1. Dopo la creazione dello snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache è specificato quando è stato configurato il backup. L'agente archivia anche i checksum per ogni blocco di dati.
1. I backup incrementali eseguiti in base alla pianificazione specificata, a meno che non si esegue un backup ad hoc.
1. Nei backup incrementali, i file modificati vengono identificati e viene creato un nuovo disco rigido virtuale, Il disco rigido virtuale verrà compressi e crittografato e quindi inviarlo per l'insieme di credenziali.
1. Al termine dell'esecuzione del backup incrementale, il nuovo disco rigido virtuale viene unito con il disco rigido virtuale creato dopo la replica iniziale. Questo disco rigido virtuale unito fornisce lo stato più recente da utilizzare per il confronto per il backup in corso.

![Backup delle macchine Windows Server in locale con l'agente di MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architettura: backup in DPM/MABS

1. Installare l'agente protezione DPM e MABS nei computer da proteggere. È quindi possibile aggiungere le macchine a un gruppo protezione dati DPM.
    - Per proteggere i computer locali, il server DPM o MABS deve essere in locale.
    - Per proteggere le macchine virtuali di Azure, il server MABS deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.
    - Con DPM/MABS, è possibile proteggere le cartelle, condivisioni, i file e volumi di backup. È anche possibile proteggere lo stato del sistema del computer (bare metal) ed è possibile proteggere le app specifiche con le impostazioni di backup compatibile con app.
1. Quando si imposta la protezione per un computer o un'app in DPM/MABS, si seleziona per eseguire il backup nel disco locale di DPM/MABS per l'archiviazione a breve termine e in Azure per la protezione online. È inoltre possibile specificare quando deve essere eseguito il backup nell'archiviazione locale di DPM/MABS e quando eseguire il backup online in Azure.
1. Il disco del carico di lavoro protetto viene eseguito il backup dei dischi DPM/MABS locali, in base alla pianificazione specificata.
4. I dischi DPM/MABS vengono eseguito il backup nell'insieme di credenziali dall'agente MARS è in esecuzione nel server DPM/MABS.

![Backup dei computer e carichi di lavoro protetti da DPM o il backup di Microsoft AZURE](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Risorse di archiviazione delle macchine virtuali di Azure

Le macchine virtuali di Azure usano dischi per archiviare il sistema operativo, le app e i dati. Ogni macchina virtuale di Azure ha almeno due dischi: un disco per il sistema operativo e un disco temporaneo. Macchine virtuali di Azure possono anche avere dischi dati per i dati dell'app. I dischi vengono archiviati come dischi rigidi virtuali.

- I VHD sono archiviati come BLOB di pagine negli account di archiviazione standard o premium in Azure:
    - **Archiviazione standard:** supporto di dischi affidabili e a basso costo per le macchine virtuali che eseguono carichi di lavoro non sensibili alla latenza. Archiviazione standard è possibile usare dischi standard (unità SSD) di unità SSD o unità disco rigido standard (HDD).
    - **Archiviazione Premium:** supporto per dischi ad alte prestazioni. Usa dischi SSD Premium.
- Sono disponibili tre diversi livelli di prestazioni per i dischi:
    - **Disco del disco rigido standard:** supportato da unità HDD e usato per un'archiviazione conveniente.
    - **Disco SSD standard:** Combina gli elementi dei dischi SSD premium e standard dei dischi HDD. Offre più coerente sulle prestazioni e affidabilità rispetto a unità disco rigido, ma comunque conveniente.
    - **Disco SSD Premium:** Supportata da unità SSD e offre prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro dei / O intensivo.
- I dischi possono essere gestiti o non gestiti:
    - **Dischi non gestiti:** Tipo tradizionale di dischi usati dalle macchine virtuali. Per questi dischi è necessario creare un account di archiviazione personale e specificarlo durante la creazione del disco. È quindi necessario determinare come ottimizzare le risorse di archiviazione per le macchine virtuali.
    - **Dischi gestiti**: Azure crea e gestisce gli account di archiviazione per l'utente. Si specifica il livello di prestazioni e le dimensioni del disco e Azure Crea dischi gestiti per l'utente. Quando si aggiungono dischi e scalabilità di macchine virtuali, Azure gestisce gli account di archiviazione.

Per altre informazioni sull'archiviazione su disco e i tipi di disco disponibili per le macchine virtuali, vedere questi articoli:

- [Azure managed disks per le macchine virtuali Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Azure managed disks per le macchine virtuali Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipi di dischi disponibili per le macchine virtuali](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Eseguire il backup e ripristino di macchine virtuali di Azure con archiviazione premium 

È possibile eseguire il backup di macchine virtuali di Azure usando archiviazione premium con Backup di Azure:

- Durante il processo di backup di macchine virtuali con archiviazione premium, il servizio Backup crea un percorso di gestione temporanea, denominato *AzureBackup -*, nell'account di archiviazione. La dimensione del percorso di gestione temporanea corrisponde alle dimensioni dello snapshot di punto di ripristino.
- Assicurarsi che sia presente spazio libero sufficiente nell'account di archiviazione Premium per il percorso di gestione temporanea. [Altre informazioni](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits) Non modificare il percorso di gestione temporanea.
- Al termine del processo di backup, il percorso di gestione temporanea viene eliminato.
- Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con i [prezzi dell'archiviazione Premium](../virtual-machines/windows/disks-types.md#billing).

Quando si ripristinano le macchine virtuali di Azure usando archiviazione premium, è possibile ripristinarli archiviazione standard o premium. In genere, si sarebbe ripristinarli ad archiviazione premium. Ma se è necessario solo un subset di file dalla macchina virtuale, potrebbe risultare più economico ripristinarli in archiviazione standard.

### <a name="back-up-and-restore-managed-disks"></a>Eseguire il backup e ripristinare i dischi gestiti

È possibile eseguire il backup di macchine virtuali di Azure con dischi gestiti:

- Il backup di macchine virtuali con dischi gestiti funziona come per qualsiasi altra macchina virtuale di Azure. È possibile eseguire il backup della macchina virtuale direttamente dalle impostazioni della macchina virtuale oppure è possibile abilitare il backup per le macchine virtuali nell'insieme di credenziali di Servizi di ripristino.
- È possibile eseguire il backup delle macchine virtuali nei dischi gestiti tramite raccolte RestorePoint basate su dischi gestiti.
- Backup di Azure supporta anche i backup delle macchine virtuali con dischi gestiti che sono stati crittografati tramite crittografia dischi di Azure.

Quando si ripristinano le macchine virtuali con dischi gestiti, è possibile ripristinare per una macchina virtuale completa con dischi gestiti o per un account di archiviazione:

- Durante il processo di ripristino, Azure gestisce i dischi gestiti. Se si usa l'opzione di account di archiviazione, è gestire l'account di archiviazione creato durante il processo di ripristino.
- Se si ripristina una macchina virtuale gestita che viene crittografata, assicurarsi che le chiavi della macchina virtuale e i segreti esistono nell'insieme di credenziali chiave prima di iniziare il processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la matrice di supporto per [informazioni sulle funzionalità supportate e sulle limitazioni per gli scenari di backup](backup-support-matrix.md).
- Configurare il backup per uno di questi scenari:
    - [Eseguire il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
    - [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
    - [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
    - [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

