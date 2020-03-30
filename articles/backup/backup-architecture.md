---
title: Panoramica dell'architettura
description: Panoramica dell'architettura, dei componenti e dei processi usati dal servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273618"
---
# <a name="azure-backup-architecture-and-components"></a>Architettura e componenti di Backup di AzureAzure Backup architecture and components

È possibile usare il servizio Backup di Azure per eseguire il backup dei dati nella piattaforma cloud di Microsoft Azure.You can use the [Azure Backup service](backup-overview.md) to back up data to the Microsoft Azure cloud platform. Questo articolo riepiloga l'architettura, i componenti e i processi di Backup di Azure.

## <a name="what-does-azure-backup-do"></a>Che cosa fa Backup di Azure?

Backup di Azure esegue il backup dei dati, dello stato della macchina e dei carichi di lavoro in esecuzione nelle macchine locali e nelle istanze di macchine virtuali (VM) di Azure.Azure Backup backs the data, machine state, and workloads running on-premises machines and Azure virtual machine (VM) instances. Esistono diversi scenari di Backup di Azure.

## <a name="how-does-azure-backup-work"></a>Come funziona Backup di Azure?

È possibile eseguire il backup di computer e dati utilizzando diversi metodi:

- **Backup di computer locali**:
  - È possibile eseguire il backup dei computer Windows locali direttamente in Azure usando l'agente Azure Backup di Microsoft Azure Recovery Services (MARS). I computer Linux non sono supportati.
  - È possibile eseguire il backup dei computer locali in un server di backup, ovvero System Center Data Protection Manager (DPM) o Microsoft Azure Backup Server (MABS). È quindi possibile eseguire il backup del server di backup in un insieme di credenziali di Servizi di ripristino in Azure.You can then back up the backup server to a Recovery Services vault in Azure.

- Eseguire il backup delle macchine virtuali di **Azure:Back up Azure VMs:**
  - È possibile eseguire il backup di macchine virtuali di Azure direttamente. Backup di Azure installa un'estensione di backup nell'agente di macchine virtuali di Azure in esecuzione nella macchina virtuale. Questa estensione esegue il backup dell'intera macchina virtuale.
  - È possibile eseguire il backup di cartelle e file specifici nella macchina virtuale di Azure eseguendo l'agente MARS.
  - È possibile eseguire il backup delle macchine virtuali di Azure in MABS in esecuzione in Azure ed è quindi possibile eseguire il backup di MABS in un insieme di credenziali di Servizi di ripristino.

Ulteriori informazioni sugli elementi di [cui è possibile eseguire il backup](backup-overview.md) e sugli scenari di backup [supportati.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Dove viene eseguito il backup dei dati?

Backup di Azure archivia i dati di cui è stato eseguito il backup in un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali è un'entità di archiviazione online in Azure usata per contenere i dati, ad esempio copie di backup, punti di ripristino e criteri di backup.

Gli insiemi di credenziali dei servizi di ripristino presentano le seguenti funzionalità:

- Gli insiemi di credenziali semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.
- In ogni sottoscrizione di Azure è possibile creare fino a 500 insiemi di credenziali.
- È possibile monitorare gli elementi di cui è stato eseguito il backup in un insieme di credenziali, incluse le macchine virtuali di Azure e le macchine locali.
- È possibile gestire l'accesso dell'insieme di credenziali tramite il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di Azure.
- È necessario specificare come vengono replicati i dati nell'insieme di credenziali per la ridondanza:
  - **Archiviazione con ridondanza locale (LRS):** per proteggersi da errori in un data center, è possibile usare LRS. L'archiviazione con ridondanza locale replica i dati in un'unità di scala di archiviazione. [Scopri di più](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Archiviazione con ridondanza geografica (GRS):** per proteggersi da interruzioni a livello di area, è possibile usare GRS. GRS replica i dati in un'area secondaria. [Scopri di più](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino utilizzano GRS.

## <a name="backup-agents"></a>Agenti di backup

Backup di Azure offre diversi agenti di backup, a seconda del tipo di computer di cui viene eseguito il backup:Azure Backup provides different backup agents, depending on what type of machine being being backed up:

**Agente** | **Dettagli**
--- | ---
**Agente MARS** | <ul><li>Viene eseguito su singoli computer Windows Server locali per eseguire il backup di file, cartelle e stato del sistema.</li> <li>Viene eseguito nelle macchine virtuali di Azure per eseguire il backup di file, cartelle e stato del sistema.</li> <li>Viene eseguito sui server DPM/MABS per eseguire il backup del disco di archiviazione locale DPM/MABS in Azure.</li></ul>
**Estensione per le macchine virtuali di Azure** | Viene eseguito in macchine virtuali di Azure per eseguirne il backup in un insieme di credenziali.

## <a name="backup-types"></a>Tipi di backup

Nella tabella seguente vengono illustrati i diversi tipi di backup e quando vengono utilizzati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Completo** | Un backup completo contiene l'intera origine dati. Richiede più larghezza di banda di rete rispetto ai backup differenziali o incrementali. | Usato per il backup iniziale.
**Differenziale** |  Un backup differenziale archivia i blocchi modificati dopo il backup completo iniziale. Utilizza una quantità minore di rete e archiviazione e non conserva copie ridondanti dei dati non modificati.<br/><br/> Inefficiente perché i blocchi di dati che non vengono modificati tra i backup successivi vengono trasferiti e archiviati. | Non è usato da Backup di Azure.
**Incrementale** | Un backup incrementale archivia solo i blocchi di dati modificati dopo il backup precedente. Efficienza elevata per rete e archiviazione. <br/><br/> Con il backup incrementale, non è necessario integrare con backup completi. | Usato da DPM/MABS per i backup su disco e usato in tutti i backup in Azure. Non utilizzato per il backup di SQL Server.

## <a name="sql-server-backup-types"></a>Tipi di backup di SQL Server

Nella tabella seguente vengono illustrati i diversi tipi di backup utilizzati per i database di SQL Server e la frequenza con cui vengono utilizzati:

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Backup completo** | un backup completo è un backup eseguito per l'intero database. Contiene tutti i dati in un database specifico o in un set di filegroup o file. Un backup completo contiene anche un numero sufficiente di registri per il ripristino dei dati. | Al massimo, è possibile attivare un backup completo al giorno.<br/><br/> È possibile scegliere di eseguire un backup completo su base giornaliera o settimanale.
**Backup differenziale** | Un backup differenziale si basa sul backup completo dei dati più recente e precedente.<br/><br/> Acquisisce solo i dati che sono stati modificati dopo il backup completo. |  Al massimo, è possibile attivare un backup differenziale al giorno.<br/><br/> Non è possibile configurare un backup completo e un backup differenziale nella stessa giornata.
**Backup del log delle transazioni** | un backup del log consente il ripristino temporizzato fino a uno specifico secondo. | Al massimo, è possibile configurare backup del log delle transazioni ogni 15 minuti.

### <a name="comparison-of-backup-types"></a>Confronto dei tipi di backup

L'utilizzo dell'archiviazione, l'obiettivo del tempo di ripristino (RTO) e l'utilizzo della rete variano per ogni tipo di backup. L'immagine seguente mostra un confronto dei tipi di backup:

- L'origine dati A è composta da 10 blocchi di archiviazione, A1-A10, di cui viene eseguito il backup mensile.
- I blocchi A2, A3, A4 e A9 cambiano nel primo mese, mentre il blocco A5 cambia il mese successivo.
- Per i backup differenziali, nel secondo mese viene eseguito il backup dei blocchi modificati A2, A3, A4 e A9. Nel terzo mese, viene eseguito nuovamente il backup di questi stessi blocchi, insieme al blocco A5 modificato. Il backup continua a essere eseguito per i blocchi modificati fino al backup completo successivo.
- Per i backup incrementali, nel secondo mese i blocchi A2, A3, A4 e A9 vengono contrassegnati come modificati e trasferiti. Nel terzo mese, viene contrassegnato e trasferito solo il blocco A5 modificato.

![Immagine che mostra confronti di metodi di backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funzionalità di backup

Nella tabella seguente sono riepilogate le funzionalità supportate per i diversi tipi di backup:

**Funzionalità** | **Backup diretto di file e cartelle (tramite l'agente MARS)** | **Backup della macchina virtuale di AzureAzure VM Backup** | **Macchine o app con DPM/MABS**
--- | --- | --- | ---
Backup nell'insieme di credenziali | ![Sì][green] | ![Sì][green] | ![Sì][green]
Eseguire il backup su DPM/MABS, quindi su Azure | | | ![Sì][green]
Compressione dei dati inviati per il backup | ![Sì][green] | Durante il trasferimento dei dati non viene usata alcuna compressione. Leggero aumento dello spazio di archiviazione richiesto, ma ripristino più veloce.  | ![Sì][green]
Backup incrementale |![Sì][green] |![Sì][green] |![Sì][green]
Backup di dischi deduplicati | | | ![Parzialmente][yellow]<br/><br/> Solo per i server DPM/MABS distribuiti in locale.

![Chiave tabella](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Informazioni di base sui criteri di backup

- Un criterio di backup viene creato per ogni insieme di credenziali.
- Un criterio di backup può essere creato per il backup dei carichi di lavoro seguenti
  - Macchina virtuale di Azure
  - SQL in macchine virtuali di Azure
  - Condivisione file di Azure
- Un criterio può essere assegnato a più risorse. Un criterio di backup di macchine virtuali di Azure può essere usato per proteggere più macchine virtuali di Azure.
- Un criterio è costituito da due componenti
  - Pianificazione: quando creare il backup
  - Conservazione: per quanto tempo ogni backup deve essere conservato.
- La pianificazione può essere "giornaliera" o "settimanale" rispetto a uno specifico punto temporale.
- La conservazione può essere definita per punti di backup "giornalieri", "settimanali", "mensili" e "annuali".
- "Settimanale" si riferisce a un backup eseguito in un determinato giorno della settimana, "mensile" indica un backup eseguito in un determinato giorno del mese e "annuale" fa riferimento a un backup eseguito in un determinato giorno dell'anno.
- La conservazione per i punti di backup "mensili" o "annuali" viene definita "LongTermRetention".
- Quando viene creato un insieme di credenziali, viene creato anche un criterio per i backup delle macchine virtuali di Azure denominato "DefaultPolicy" che può essere usato per eseguire il backup delle macchine virtuali di Azure.When a vault is created, a policy for Azure VM backups called "DefaultPolicy" is also created and can be used to back up Azure VMs.

## <a name="architecture-built-in-azure-vm-backup"></a>Architettura: Backup integrato della macchina virtuale di AzureArchitecture: Built-in Azure VM Backup

1. Quando si abilita il backup per una macchina virtuale di Azure, un backup viene eseguito in base alla pianificazione specificata.
1. Durante il primo backup, viene installata un'estensione di backup nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, viene installata l'estensione VMSnapshot.For Windows VMs, the VMSnapshot extension is installed.
    - Per le macchine virtuali Linux, viene installata l'estensione VMSnapshot Linux.For Linux VMs, the VMSnapshot Linux extension is installed.
1. L'estensione accetta uno snapshot a livello di archiviazione.
    - Per le macchine virtuali Windows in esecuzione, Backup si coordina con il servizio Copia Shadow del volume (VSS) di Windows per creare uno snapshot coerente con l'app della macchina virtuale. Per impostazione predefinita, Backup esegue backup VSS completi. Se Backup di Azure non riesce a creare uno snapshot coerente con l'app, acquisisce uno snapshot coerente con i file.
    - Per le macchine virtuali Linux, Backup crea uno snapshot coerente con i file. Per gli snapshot coerenti con l'app, devi personalizzare manualmente gli script pre/post.
    - Il backup viene ottimizzato eseguendo in parallelo il backup di ogni disco di macchina virtuale. Per ogni disco di cui si esegue il backup, Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati.
1. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali.
    - Vengono copiati solo i blocchi di dati modificati dall'ultimo backup.
    - I dati non vengono crittografati. Azure Backup can back up Azure VMs that were encrypted by using Azure Disk Encryption.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Nelle ore di punta, il backup potrebbe richiedere alcune ore. Il tempo di backup totale per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
1. Dopo l'invio dei dati all'insieme di credenziali, viene creato un punto di ripristino. Per impostazione predefinita, gli snapshot vengono mantenuti per due giorni prima di essere eliminati. Questa funzionalità consente di ripristinare l'operazione da queste istantanee, riducendo così i tempi di ripristino. Riduce il tempo necessario per trasformare e copiare i dati dal vault. Vedere Funzionalità di [ripristino immediato di Backup](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)di Azure .

Non è necessario consentire in modo esplicito la connettività Internet per eseguire il backup delle macchine virtuali di Azure.You do not need to explicitly allow internet connectivity to back up your Azure VMs.

![Backup di macchine virtuali di Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architettura: backup diretto di computer Windows Server locali o file o cartelle di macchine virtuali di Azure

1. Per configurare lo scenario, scaricare e installare l'agente MARS nel computer. È quindi possibile selezionare gli elementi di cui eseguire il backup, quando verranno eseguiti i backup e per quanto tempo verranno conservati in Azure.You then select what to back up, when backups will run, and how long they'll be kept in Azure.
1. Il backup iniziale viene eseguito in base alle impostazioni di backup.
1. L'agente MARS utilizza VSS per creare uno snapshot temporizzato dei volumi selezionati per il backup.
    - L'agente MARS utilizza solo l'operazione di scrittura del sistema di Windows per acquisire lo snapshot.
    - Poiché l'agente non utilizza writer VSS dell'applicazione, non acquisisce snapshot coerenti con l'app.
1. Dopo aver creato lo snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache specificata al momento della configurazione del backup. L'agente archivia anche i checksum per ogni blocco di dati.
1. I backup incrementali vengono eseguiti in base alla pianificazione specificata, a meno che non si esedi a meno che non si eseda un backup su richiesta.
1. Nei backup incrementali, i file modificati vengono identificati e viene creato un nuovo disco rigido virtuale, Il disco rigido virtuale viene compresso e crittografato e quindi viene inviato all'insieme di credenziali.
1. Al termine del backup incrementale, il nuovo disco rigido virtuale viene unito al disco rigido virtuale creato dopo la replica iniziale. Questo disco rigido virtuale unito fornisce lo stato più recente da utilizzare per il confronto per il backup in corso.

![Backup di computer Windows Server locali con agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architettura: Backup in DPM/MABS

1. Installare l'agente protezione DPM o MABS nei computer che si desidera proteggere. Aggiungere quindi i computer a un gruppo protezione dati DPM.
    - Per proteggere i computer locali, il server DPM o MABS deve essere in locale.
    - Per proteggere le macchine virtuali di Azure, il server MABS deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.
    - Con DPM/MABS è possibile proteggere volumi, condivisioni, file e cartelle di backup. È inoltre possibile proteggere lo stato del sistema di una macchina (bare metal) ed è possibile proteggere app specifiche con impostazioni di backup in grado di riconoscere le app.
1. Quando si configura la protezione per un computer o un'app in DPM/MABS, si sceglie di eseguire il backup sul disco locale MABS/DPM per l'archiviazione a breve termine e in Azure per la protezione online. È inoltre possibile specificare quando deve essere eseguito il backup nell'archiviazione DPM/MABS locale e quando deve essere eseguito il backup online in Azure.
1. Il backup del disco del carico di lavoro protetto viene eseguito nei dischi MABS/DPM locali, in base alla pianificazione specificata.
1. Il backup dei dischi DPM/MABS viene eseguito nell'insieme di credenziali dall'agente MARS in esecuzione sul server DPM/MABS.

![Backup di macchine e carichi di lavoro protetti da DPM o MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Risorse di archiviazione delle macchine virtuali di Azure

Le macchine virtuali di Azure usano dischi per archiviare il sistema operativo, le app e i dati. Ogni macchina virtuale di Azure ha almeno due dischi: un disco per il sistema operativo e un disco temporaneo. Le macchine virtuali di Azure possono anche avere dischi dati per i dati dell'app. I dischi vengono archiviati come dischi rigidi virtuali.

- I dischi rigidi virtuali vengono archiviati come BLOB di pagine negli account di archiviazione standard o Premium in Azure:VHDs are stored as page blobs in standard or premium storage accounts in Azure:
  - **Archiviazione standard:** Supporto affidabile e a basso costo del disco per le macchine virtuali che eseguono carichi di lavoro che non sono sensibili alla latenza. L'archiviazione standard può utilizzare dischi SSD (Solid-State Drive) standard o dischi HDD (Standard Hard Disk Drive).
  - **Archiviazione Premium:** Supporto di schietto ad alte prestazioni. Usa dischi SSD Premium.
- Sono disponibili tre diversi livelli di prestazioni per i dischi:
  - **Disco HDD standard:** Supportato da HDD e utilizzato per lo storage conveniente.
  - **Disco SSD standard:** Combina elementi di dischi SSD premium e dischi HDD standard. Offre prestazioni e affidabilità più coerenti rispetto all'HDD, ma comunque conveniente.
  - **Disco SSD Premium:** Supportato da SSD e fornisce prestazioni elevate e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con utilizzo intensivo di I/O.Backed by SSDs, and provides high-performance and low-latency for VMs that are running I/O-intensive workloads.
- I dischi possono essere gestiti o non gestiti:
  - **Dischi non gestiti:** Tipo tradizionale di dischi usati dalle macchine virtuali. Per questi dischi è necessario creare un account di archiviazione personale e specificarlo durante la creazione del disco. È quindi necessario capire come ottimizzare le risorse di archiviazione per le macchine virtuali.
  - **Dischi gestiti:** Azure crea e gestisce automaticamente gli account di archiviazione. Specificare le dimensioni del disco e il livello di prestazioni e Azure crea dischi gestiti per l'utente. Quando si aggiungono dischi e si ridimensionano le macchine virtuali, Azure gestisce gli account di archiviazione.

Per altre informazioni sull'archiviazione su disco e sui tipi di disco disponibili per le macchine virtuali, vedere gli articoli seguenti:For more information about disk storage and the available disk types for VMs, see these articles:

- [Azure managed disks for Windows VMs](../virtual-machines/windows/managed-disks-overview.md)
- [Azure managed disks for Linux VMs](../virtual-machines/linux/managed-disks-overview.md)
- [Tipi di disco disponibili per le macchine virtualiAvailable disk types for VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Eseguire il backup e il ripristino delle macchine virtuali di Azure con archiviazione PremiumBack up and restore Azure VMs with premium storage

È possibile eseguire il backup delle macchine virtuali di Azure usando l'archiviazione Premium con Backup di Azure:You can back up Azure VMs by using premium storage with Azure Backup:

- Durante il processo di backup delle macchine virtuali con archiviazione Premium, il servizio Backup crea un percorso di gestione temporanea temporanea, denominato *AzureBackup-*, nell'account di archiviazione. La dimensione del percorso di gestione temporanea è uguale alla dimensione dello snapshot del punto di ripristino.
- Assicurarsi che sia presente spazio libero sufficiente nell'account di archiviazione Premium per il percorso di gestione temporanea. Per altre informazioni, vedere [Obiettivi di scalabilità per gli account](../storage/blobs/scalability-targets-premium-page-blobs.md)di archiviazione BLOB di pagine premium. Non modificare il percorso di gestione temporanea.
- Al termine del processo di backup, il percorso di gestione temporanea viene eliminato.
- Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con i [prezzi dell'archiviazione Premium](../virtual-machines/windows/disks-types.md#billing).

Quando si ripristinano macchine virtuali di Azure usando l'archiviazione Premium, è possibile ripristinarle nell'archiviazione Premium o Standard.When you restore Azure VMs by using premium storage, you can restore them to premium or standard storage. In genere, è necessario ripristinarli in uno spazio di archiviazione Premium. Tuttavia, se è necessario solo un sottoinsieme di file dalla macchina virtuale, potrebbe essere conveniente ripristinarli nell'archiviazione standard.

### <a name="back-up-and-restore-managed-disks"></a>Eseguire il backup e il ripristino dei dischi gestiti

È possibile eseguire il backup delle macchine virtuali di Azure con dischi gestiti:You can back up Azure VMs with managed disks:

- Il backup di macchine virtuali con dischi gestiti funziona come per qualsiasi altra macchina virtuale di Azure. È possibile eseguire il backup della macchina virtuale direttamente dalle impostazioni della macchina virtuale oppure è possibile abilitare il backup per le macchine virtuali nell'insieme di credenziali di Servizi di ripristino.
- È possibile eseguire il backup delle macchine virtuali nei dischi gestiti tramite raccolte RestorePoint basate su dischi gestiti.
- Backup di Azure supporta anche il backup di macchine virtuali con dischi gestiti crittografati tramite Crittografia disco di Azure.Azure Backup also supporting up VMs with managed disks that were encrypted by using Azure Disk Encryption.

Quando si ripristinano macchine virtuali con dischi gestiti, è possibile eseguire il ripristino in una macchina virtuale completa con dischi gestiti o in un account di archiviazione:When you restore VMs with managed disks, you can restore to a complete VM with managed disks or to a storage account:

- Durante il processo di ripristino, Azure gestisce i dischi gestiti. Se si usa l'opzione dell'account di archiviazione, si gestisce l'account di archiviazione creato durante il processo di ripristino.
- Se si ripristina una macchina virtuale gestita crittografata, assicurarsi che le chiavi e i segreti della macchina virtuale esistano nell'insieme di credenziali delle chiavi prima di avviare il processo di ripristino.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la matrice di supporto per [informazioni sulle funzionalità supportate e sulle limitazioni per gli scenari](backup-support-matrix.md)di backup.
- Configurare il backup per uno di questi scenari:
  - [Eseguire il backup delle macchine virtuali](backup-azure-arm-vms-prepare.md)di Azure.
  - [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
  - [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
  - [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
