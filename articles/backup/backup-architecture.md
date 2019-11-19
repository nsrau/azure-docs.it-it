---
title: Panoramica dell'architettura
description: Panoramica dell'architettura, dei componenti e dei processi usati dal servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: ae7b0c2b81bd3d393b7e749e077a6f5fa0379562
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173511"
---
# <a name="azure-backup-architecture-and-components"></a>Architettura e componenti di backup di Azure

È possibile usare il [servizio backup di Azure](backup-overview.md) per eseguire il backup dei dati nella piattaforma Microsoft Azure cloud. Questo articolo riepiloga l'architettura, i componenti e i processi di Backup di Azure.

## <a name="what-does-azure-backup-do"></a>Che cosa fa Backup di Azure?

Backup di Azure esegue il backup dei dati, lo stato del computer e i carichi di lavoro in esecuzione in computer locali e istanze di macchine virtuali (VM) di Azure. Esistono diversi scenari di Backup di Azure.

## <a name="how-does-azure-backup-work"></a>Come funziona Backup di Azure?

È possibile eseguire il backup di computer e dati utilizzando diversi metodi:

- **Backup di computer locali**:
  - È possibile eseguire il backup di computer Windows locali direttamente in Azure usando l'agente di Servizi di ripristino di Microsoft Azure di backup di Azure (MARS). I computer Linux non sono supportati.
  - È possibile eseguire il backup di computer locali in un server di backup, ovvero System Center Data Protection Manager (DPM) o server di Backup di Microsoft Azure (MAB). È quindi possibile eseguire il backup del server di backup in un insieme di credenziali di servizi di ripristino in Azure.

- **Backup delle macchine virtuali di Azure**:
  - È possibile eseguire il backup di macchine virtuali di Azure direttamente. Backup di Azure installa un'estensione di backup nell'agente di macchine virtuali di Azure in esecuzione nella macchina virtuale. Questa estensione esegue il backup dell'intera macchina virtuale.
  - È possibile eseguire il backup di cartelle e file specifici nella macchina virtuale di Azure eseguendo l'agente MARS.
  - È possibile eseguire il backup di macchine virtuali di Azure nell'oggetto MAB in esecuzione in Azure ed è quindi possibile eseguire il backup di Mab in un insieme di credenziali di servizi di ripristino.

Scopri di più sulle funzionalità di [cui puoi eseguire il](backup-overview.md) backup e sugli [scenari di backup supportati](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Dove viene eseguito il backup dei dati?

Backup di Azure archivia i dati sottoposti a backup in un insieme di credenziali di servizi di ripristino. Un insieme di credenziali è un'entità di archiviazione online in Azure usata per conservare i dati, ad esempio copie di backup, punti di ripristino e criteri di backup.

Gli insiemi di credenziali dei servizi di ripristino includono le funzionalità seguenti:

- Gli insiemi di credenziali semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.
- In ogni sottoscrizione di Azure è possibile creare fino a 500 insiemi di credenziali.
- È possibile monitorare gli elementi di cui è stato eseguito il backup in un insieme di credenziali, incluse le macchine virtuali di Azure e i computer locali.
- È possibile gestire l'accesso dell'insieme di credenziali tramite il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di Azure.
- È necessario specificare come vengono replicati i dati nell'insieme di credenziali per la ridondanza:
  - **Archiviazione con ridondanza locale (con ridondanza locale)** : per evitare errori in un Data Center, è possibile usare con ridondanza locale. L'archiviazione con ridondanza locale replica i dati in un'unità di scala di archiviazione. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Archiviazione con ridondanza geografica**: per proteggersi da interruzioni a livello di area, è possibile usare GRS. Il GRS replica i dati in un'area secondaria. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Per impostazione predefinita, gli insiemi di credenziali dei servizi di ripristino usano GRS.

## <a name="backup-agents"></a>Agenti di backup

Backup di Azure fornisce diversi agenti di backup, a seconda del tipo di computer di cui viene eseguito il backup:

**Agent** | **Dettagli**
--- | ---
**Agente MARS** | <ul><li>Viene eseguito nei singoli computer Windows Server locali per eseguire il backup di file, cartelle e lo stato del sistema.</li> <li>Viene eseguito in macchine virtuali di Azure per eseguire il backup di file, cartelle e lo stato del sistema.</li> <li>Viene eseguito nei server DPM/MAB per eseguire il backup del disco di archiviazione locale di DPM/MAB in Azure.</li></ul>
**Estensione per le macchine virtuali di Azure** | Viene eseguito in macchine virtuali di Azure per eseguirne il backup in un insieme di credenziali.

## <a name="backup-types"></a>Tipi di backup

Nella tabella seguente sono illustrati i diversi tipi di backup e quando vengono utilizzati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Completo** | Un backup completo contiene l'intera origine dati. Richiede una maggiore larghezza di banda di rete rispetto al backup differenziale o incrementale. | Usato per il backup iniziale.
**Differenziale** |  Un backup differenziale archivia i blocchi modificati dopo il backup completo iniziale. Usa una quantità minore di spazio di archiviazione e di rete e non mantiene copie ridondanti di dati non modificati.<br/><br/> Inefficiente poiché i blocchi di dati che non sono stati modificati tra i backup successivi vengono trasferiti e archiviati. | Non è usato da Backup di Azure.
**Incrementale** | Un backup incrementale archivia solo i blocchi di dati modificati rispetto al backup precedente. Efficienza elevata per rete e archiviazione. <br/><br/> Con il backup incrementale, non è necessario integrare i backup completi. | Usato da DPM/MABS per i backup su disco e usato in tutti i backup in Azure. Non usato per il backup SQL Server.

## <a name="sql-server-backup-types"></a>Tipi di backup di SQL Server

La tabella seguente illustra i diversi tipi di backup usati per SQL Server database e la frequenza con cui vengono usati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Backup completo** | un backup completo è un backup eseguito per l'intero database. Contiene tutti i dati in un database specifico o in un set di filegroup o file. Un backup completo contiene anche un numero sufficiente di log per recuperare i dati. | Al massimo, è possibile attivare un backup completo al giorno.<br/><br/> È possibile scegliere di eseguire un backup completo su un intervallo giornaliero o settimanale.
**Backup differenziale** | Un backup differenziale si basa sul backup completo dei dati più recente e precedente.<br/><br/> Acquisisce solo i dati che sono stati modificati dopo il backup completo. |  Al massimo, è possibile attivare un backup differenziale al giorno.<br/><br/> Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
**Backup del log delle transazioni** | un backup del log consente il ripristino temporizzato fino a uno specifico secondo. | Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

### <a name="comparison-of-backup-types"></a>Confronto tra tipi di backup

L'utilizzo dell'archiviazione, l'obiettivo del tempo di ripristino (RTO) e l'utilizzo della rete variano per ogni tipo di backup. Nell'immagine seguente viene illustrato un confronto tra i tipi di backup:

- L'origine dati A è costituita da 10 blocchi di archiviazione, A1-A10, di cui viene eseguito il backup mensile.
- I blocchi A2, A3, A4 e A9 cambiano nel primo mese, mentre il blocco A5 cambia il mese successivo.
- Per i backup differenziali, nel secondo mese viene eseguito il backup dei blocchi modificati A2, A3, A4 e A9. Nel terzo mese, viene eseguito nuovamente il backup di questi stessi blocchi, insieme al blocco A5 modificato. Il backup continua a essere eseguito per i blocchi modificati fino al backup completo successivo.
- Per i backup incrementali, nel secondo mese i blocchi a2, a3, A4 e A9 sono contrassegnati come modificati e trasferiti. Nel terzo mese, viene contrassegnato e trasferito solo il blocco A5 modificato.

![Immagine che mostra i confronti dei metodi di backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funzionalità di backup

Nella tabella seguente sono riepilogate le funzionalità supportate per i diversi tipi di backup:

**Funzionalità** | **Backup diretto di file e cartelle (tramite l'agente MARS)** | **Backup di macchine virtuali di Azure** | **Computer o app con DPM/MAB**
--- | --- | --- | ---
Backup nell'insieme di credenziali | ![Sì][green] | ![Sì][green] | ![Sì][green]
Esegui il backup nel disco di DPM/MAB, quindi in Azure | | | ![Sì][green]
Compressione dei dati inviati per il backup | ![Sì][green] | Durante il trasferimento dei dati non viene usata alcuna compressione. Leggero aumento dello spazio di archiviazione richiesto, ma ripristino più veloce.  | ![Sì][green]
Backup incrementale |![Sì][green] |![Sì][green] |![Sì][green]
Backup di dischi deduplicati | | | ![Parzialmente][yellow]<br/><br/> Solo per i server DPM/MABS distribuiti in locale.

![Chiave tabella](./media/backup-architecture/table-key.png)

## <a name="architecture-built-in-azure-vm-backup"></a>Architettura: backup di macchine virtuali di Azure predefinito

1. Quando si Abilita il backup per una macchina virtuale di Azure, viene eseguito un backup in base alla pianificazione specificata.
1. Durante il primo backup, un'estensione di backup viene installata nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, viene installata l'estensione VMSnapshot.
    - Per le macchine virtuali Linux, è installata l'estensione VMSnapshot per Linux.
1. L'estensione accetta uno snapshot a livello di archiviazione.
    - Per le macchine virtuali Windows in esecuzione, le coordinate di backup con Windows Servizio Copia Shadow del volume (VSS) per creare uno snapshot coerente con l'app della macchina virtuale. Per impostazione predefinita, il backup esegue backup VSS completi. Se Backup di Azure non riesce a creare uno snapshot coerente con l'app, acquisisce uno snapshot coerente con i file.
    - Per le macchine virtuali Linux, il backup esegue uno snapshot coerente con i file. Per gli snapshot coerenti con l'app, è necessario personalizzare manualmente gli script pre/post.
    - Il backup viene ottimizzato eseguendo in parallelo il backup di ogni disco di macchina virtuale. Per ogni disco di cui si esegue il backup, Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati.
1. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali.
    - Vengono copiati solo i blocchi di dati modificati dopo l'ultimo backup.
    - I dati non vengono crittografati. Backup di Azure può eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. In momenti di picco, il backup potrebbe richiedere alcune ore. Il tempo totale di backup per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
1. Dopo che i dati sono stati inviati all'insieme di credenziali, viene creato un punto di ripristino. Per impostazione predefinita, gli snapshot vengono conservati per due giorni prima di essere eliminati. Questa funzionalità consente l'operazione di ripristino da questi snapshot, riducendo così i tempi di ripristino. Riduce il tempo necessario per la trasformazione e la copia dei dati dall'insieme di credenziali. Vedere [funzionalità di ripristino immediato di backup di Azure](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Le macchine virtuali di Azure richiedono l'accesso a Internet per i comandi di controllo. Se si esegue il backup dei carichi di lavoro all'interno della macchina virtuale, ad esempio SQL Server backup del database, anche i dati back-end necessitano di accesso a Internet.

![Backup di macchine virtuali di Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architettura: backup diretto di computer Windows Server locali o di cartelle o file di VM di Azure

1. Per configurare lo scenario, scaricare e installare l'agente MARS nel computer. È quindi possibile selezionare gli elementi di cui eseguire il backup, quando verranno eseguiti i backup e per quanto tempo verranno conservati in Azure.
1. Il backup iniziale viene eseguito in base alle impostazioni di backup.
1. L'agente MARS utilizza VSS per eseguire uno snapshot temporizzato dei volumi selezionati per il backup.
    - L'agente MARS usa solo l'operazione di scrittura del sistema Windows per acquisire lo snapshot.
    - Poiché l'agente non usa alcun writer VSS dell'applicazione, non acquisisce snapshot coerenti con l'app.
1. Dopo l'acquisizione dello snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache specificata al momento della configurazione del backup. L'agente archivia inoltre i checksum per ogni blocco di dati.
1. I backup incrementali vengono eseguiti in base alla pianificazione specificata, a meno che non si esegua un backup su richiesta.
1. Nei backup incrementali, i file modificati vengono identificati e viene creato un nuovo disco rigido virtuale, Il disco rigido virtuale viene compresso e crittografato, quindi viene inviato all'insieme di credenziali.
1. Al termine del backup incrementale, il nuovo disco rigido virtuale viene unito al VHD creato dopo la replica iniziale. Questo VHD Unito fornisce lo stato più recente da usare per il confronto per il backup in corso.

![Backup di computer Windows Server locali con l'agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architettura: backup in DPM/MAB

1. Si installa l'agente protezione DPM o MAB nei computer che si desidera proteggere. È quindi possibile aggiungere i computer a un gruppo protezione dati DPM.
    - Per proteggere i computer locali, il server DPM o MABS deve essere in locale.
    - Per proteggere le macchine virtuali di Azure, il server MABS deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.
    - Con DPM/MAB è possibile proteggere i volumi di backup, le condivisioni, i file e le cartelle. È anche possibile proteggere lo stato del sistema di un computer (Bare Metal) ed è possibile proteggere app specifiche con impostazioni di backup compatibili con le app.
1. Quando si configura la protezione per un computer o un'app in DPM/MAB, si sceglie di eseguire il backup nel disco locale di MAB/DPM per l'archiviazione a breve termine e in Azure per la protezione dati online. Si specifica anche quando deve essere eseguito il backup sull'archiviazione DPM/MAB locale e quando deve essere eseguito il backup online in Azure.
1. Viene eseguito il backup del disco del carico di lavoro protetto nei dischi MAB/DPM locali, in base alla pianificazione specificata.
1. Viene eseguito il backup dei dischi DPM/MAB nell'insieme di credenziali da parte dell'agente MARS in esecuzione nel server DPM/MAB.

![Backup di computer e carichi di lavoro protetti da DPM o MAB](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Risorse di archiviazione delle macchine virtuali di Azure

Le macchine virtuali di Azure usano dischi per archiviare il sistema operativo, le app e i dati. Ogni macchina virtuale di Azure dispone di almeno due dischi: un disco per il sistema operativo e un disco temporaneo. Le macchine virtuali di Azure possono anche avere dischi dati per i dati delle app. I dischi vengono archiviati come dischi rigidi virtuali.

- I dischi rigidi virtuali vengono archiviati come BLOB di pagine negli account di archiviazione standard o Premium in Azure:
  - **Archiviazione standard:** Supporto del disco affidabile e a basso costo per le macchine virtuali che eseguono carichi di lavoro che non sono sensibili alla latenza. L'archiviazione standard può usare dischi SSD (Solid-State Drive) standard o dischi disco rigido (HDD) standard.
  - **Archiviazione Premium:** Supporto per dischi ad alte prestazioni. Usa dischi SSD Premium.
- Sono disponibili tre diversi livelli di prestazioni per i dischi:
  - **Disco HDD standard:** Supportato da HDD e usato per l'archiviazione conveniente.
  - **Disco SDD standard:** Combina gli elementi di dischi SSD Premium e dischi HDD standard. Offre prestazioni e affidabilità più coerenti rispetto al disco rigido, ma è ancora conveniente.
  - **Disco SSD Premium:** Supportato da SSD e offre prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con attività di I/O intensive.
- I dischi possono essere gestiti o non gestiti:
  - **Dischi non gestiti:** Tipo tradizionale di dischi usati dalle macchine virtuali. Per questi dischi è necessario creare un account di archiviazione personale e specificarlo durante la creazione del disco. È quindi necessario scoprire come ottimizzare le risorse di archiviazione per le macchine virtuali.
  - **Dischi gestiti:** Azure crea e gestisce automaticamente gli account di archiviazione. Si specificano le dimensioni del disco e il livello di prestazioni e Azure crea automaticamente i dischi gestiti. Quando si aggiungono dischi e si ridimensionano le macchine virtuali, Azure gestisce gli account di archiviazione.

Per altre informazioni sull'archiviazione su disco e sui tipi di dischi disponibili per le macchine virtuali, vedere questi articoli:

- [Azure Managed disks per macchine virtuali Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Azure Managed disks per macchine virtuali Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipi di dischi disponibili per le macchine virtuali](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Eseguire il backup e il ripristino di macchine virtuali di Azure con archiviazione Premium

È possibile eseguire il backup di macchine virtuali di Azure usando archiviazione Premium con backup di Azure:

- Durante il processo di backup di macchine virtuali con archiviazione Premium, il servizio di backup crea un percorso di gestione temporanea, denominato *AzureBackup*, nell'account di archiviazione. Le dimensioni del percorso di gestione temporanea equivalgono alla dimensione dello snapshot del punto di ripristino.
- Assicurarsi che sia presente spazio libero sufficiente nell'account di archiviazione Premium per il percorso di gestione temporanea. [Altre informazioni](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Non modificare il percorso di gestione temporanea.
- Al termine del processo di backup, il percorso di gestione temporanea viene eliminato.
- Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con i [prezzi dell'archiviazione Premium](../virtual-machines/windows/disks-types.md#billing).

Quando si ripristinano le macchine virtuali di Azure usando archiviazione Premium, è possibile ripristinarle nell'archiviazione Premium o standard. In genere, è necessario ripristinarli in archiviazione Premium. Tuttavia, se è necessario solo un subset di file dalla macchina virtuale, potrebbe essere conveniente ripristinarli nell'archiviazione standard.

### <a name="back-up-and-restore-managed-disks"></a>Eseguire il backup e il ripristino dei dischi gestiti

È possibile eseguire il backup di macchine virtuali di Azure con Managed disks:

- Il backup di macchine virtuali con dischi gestiti funziona come per qualsiasi altra macchina virtuale di Azure. È possibile eseguire il backup della macchina virtuale direttamente dalle impostazioni della macchina virtuale oppure è possibile abilitare il backup per le macchine virtuali nell'insieme di credenziali di Servizi di ripristino.
- È possibile eseguire il backup delle macchine virtuali nei dischi gestiti tramite raccolte RestorePoint basate su dischi gestiti.
- Backup di Azure supporta anche il backup di macchine virtuali con dischi gestiti crittografati tramite crittografia dischi di Azure.

Quando si ripristinano le VM con Managed disks, è possibile eseguire il ripristino in una macchina virtuale completa con dischi gestiti o in un account di archiviazione:

- Durante il processo di ripristino, Azure gestisce i dischi gestiti. Se si usa l'opzione account di archiviazione, è possibile gestire l'account di archiviazione creato durante il processo di ripristino.
- Se si ripristina una macchina virtuale gestita crittografata, assicurarsi che le chiavi e i segreti della macchina virtuale esistano nell'insieme di credenziali delle chiavi prima di avviare il processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la matrice di supporto per informazioni [sulle funzionalità supportate e sulle limitazioni per gli scenari di backup](backup-support-matrix.md).
- Configurare il backup per uno di questi scenari:
  - [Eseguire il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
  - [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
  - [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
  - [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
