---
title: Matrice di supporto di Backup di Azure
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: f2f3d26f74c6227ad257c188d4088fd41fca7075
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922317"
---
# <a name="support-matrix-for-azure-backup"></a>Matrice di supporto per Backup di Azure

È possibile usare [Backup di Azure](backup-overview.md) per eseguire il backup dei dati nella piattaforma cloud Microsoft Azure. Questo articolo riepiloga i limiti e le impostazioni di supporto generali per gli scenari e le distribuzioni di Backup di Azure.

Sono disponibili altre matrici di supporto:

- Matrice di supporto per il [backup di macchine virtuali (VM) di Azure](backup-support-matrix-iaas.md)
- Matrice di supporto per il backup usando [System Center Data Protection Manager (DPM)/server di Backup di Microsoft Azure (MABS)](backup-support-matrix-mabs-dpm.md)
- Matrice di supporto per il backup usando l'[agente di Servizi di ripristino di Microsoft Azure](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure usa gli insiemi di credenziali di Servizi di ripristino per orchestrare e gestire i backup, nonché per archiviare i dati sottoposti a backup.

Nella tabella seguente vengono descritte le funzioni dell'insieme di credenziali di Servizi di ripristino:

**Funzionalità** | **Dettagli**
--- | ---
**Insiemi di credenziali nella sottoscrizione** | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
**Computer in un insieme di credenziali** | Fino a 2000 origini dati in tutti i carichi di lavoro, ad esempio macchine virtuali di Azure, SQL Server VM, server MAB e così via, possono essere protette in un unico insieme di credenziali.<br><br>Fino a 1.000 VM di Azure in un singolo insieme di credenziali.<br/><br/> In un singolo insieme di credenziali possono essere registrati fino a 50 server MABS.
**Origini dei dati** | La dimensione massima di una singola [origine dati](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) è 54.400 GB. Questo limite non si applica ai backup di VM di Azure. Alla quantità totale di dati di cui è possibile eseguire il backup nell'insieme di credenziali non è applicato alcun limite.
**Backup in un insieme di credenziali** | **VM di Azure:** una volta al giorno.<br/><br/>**Computer protetti da DPM/MABS:** due volte al giorno.<br/><br/> **Computer sottoposti a backup direttamente usando l'agente MARS:** tre volte al giorno.
**Backup tra insiemi di credenziali** | Il backup avviene all'interno di un'area.<br/><br/> È necessario un insieme di credenziali in ogni area di Azure che contiene macchine virtuali di cui si vuole eseguire il backup. Non è possibile eseguire il backup in un'altra area.
**Spostamento di insiemi di credenziali** | È possibile [spostare insiemi di credenziali](./backup-azure-move-recovery-services-vault.md) fra sottoscrizioni o fra gruppi di risorse nella stessa sottoscrizione. Tuttavia, lo spostamento di insiemi di credenziali tra aree non è supportato.
**Spostamento dei dati tra insiemi di credenziali** | Lo spostamento di dati sottoposti a backup tra insiemi di credenziali non è supportato.
**Modifica del tipo di archiviazione dell'insieme di credenziali** | È possibile modificare il tipo di replica di archiviazione (archiviazione con ridondanza geografica o archiviazione con ridondanza locale) per un insieme di credenziali prima che vengano archiviati i backup. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.

## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Se si vuole eseguire il backup dei computer locali, sono supportati gli scenari seguenti:

**Computer** | **Elementi di backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup diretto di computer Windows con l'agente MARS** | File, cartelle, stato del sistema | Backup in un insieme di credenziali di Servizi di ripristino. | Backup eseguito tre volte al giorno<br/><br/> Backup senza riconoscimento delle app<br/><br/> Ripristino di file, cartelle, volumi
**Backup diretto di computer Linux con l'agente MARS** | Backup non supportato
**Back in DPM** | File, cartelle, volumi, stato del sistema, dati delle app | Backup in un archivio DPM locale. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino<br/><br/> Linux supportato per le VM (Hyper-V/VMware)<br/><br/> Oracle non supportato
**Backup in MABS** | File, cartelle, volumi, stato del sistema, dati delle app | Backup in un archivio MABS locale. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino<br/><br/> Linux supportato per le VM (Hyper-V/VMware)<br/><br/> Oracle non supportato

## <a name="azure-vm-backup-support"></a>Supporto per il backup di macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
**Dischi di dati delle VM di Azure** | Vedere la [matrice di supporto per il backup di VM di Azure](./backup-support-matrix-iaas.md#vm-storage-support).
**Dimensioni dei dischi di dati delle VM di Azure** | Le dimensioni possono raggiungere i 32 TB per un disco singolo e un massimo di 256 TB combinati per tutti i dischi in una VM.

### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Se si vuole eseguire il backup delle VM di Azure, sono supportati gli scenari seguenti:

**Computer** | **Elementi di backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup di VM di Azure tramite l'estensione della VM** | Intera macchina virtuale | Backup nell'insieme di credenziali. | Estensione installata quando si abilita il backup per una macchina virtuale.<br/><br/> Backup eseguito una volta al giorno.<br/><br/> Backup con riconoscimento delle app per le VM Windows, backup coerenti con i file per le VM Linux. È possibile configurare la coerenza con le app per i computer Linux tramite script personalizzati.<br/><br/> Ripristino di VM o disco.<br/><br/> Non è possibile eseguire il backup di una VM di Azure in una posizione locale.
**Backup di VM di Azure tramite l'agente MARS** | File, cartelle, stato del sistema | Backup nell'insieme di credenziali. | Backup eseguito tre volte al giorno.<br/><br/> Se si vuole eseguire il backup di cartelle o file specifici invece che dell'intera VM, l'agente MARS può essere eseguito insieme all'estensione della VM.
**VM di Azure con DPM** | File, cartelle, volumi, stato del sistema, dati delle app | Backup nell'archivio locale della VM di Azure che esegue DPM. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, stato del sistema, dati delle app | Backup nell'archivio locale della VM di Azure che esegue MABS. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.

## <a name="linux-backup-support"></a>Supporto di backup di Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti:

**Tipo di backup** | **Linux (approvato per Azure)**
--- | ---
**Backup diretto di un computer locale che esegue Linux** | Non supportato. L'agente MARS può essere installato solo in computer Windows.
**Uso dell'estensione agente per eseguire il backup di una VM di Azure che esegue Linux** | Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Uso di DPM per eseguire il backup su computer locali che eseguono Linux** | Backup coerenti con i file di VM guest Linux in Hyper-V e VMware.<br/><br/> Ripristino di VM guest Linux Hyper-V e VMware.
**Uso di MABS per eseguire il backup su computer locali che eseguono Linux** | Backup coerenti con i file di VM guest Linux in Hyper-V e VMware.<br/><br/> Ripristino di VM guest Linux Hyper-V e VMware.
**Uso di MABS o DPM per eseguire il backup di VM Linux di Azure** | Non supportato.

## <a name="daylight-saving-time-support"></a>Ora legale supportata

Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle VM di Azure. Non sposta l'ora del backup avanti o indietro. Per assicurarsi che il backup venga eseguito all'ora desiderata, modificare manualmente i criteri di backup in base alle proprie necessità.

## <a name="disk-deduplication-support"></a>Supporto per la deduplicazione dei dischi

La deduplicazione dei dischi è supportata come segue:

- La deduplicazione dei dischi è supportata in locale se si usano DPM o MABS per eseguire il backup di VM Hyper-V che eseguono Windows. Windows Server esegue la deduplicazione dei dati a livello di host nei dischi rigidi virtuali (VHD) collegati alla VM come archivio di backup.
- La deduplicazione non è supportata in Azure per nessun componente di Backup. Se DPM e MABS vengono distribuiti in Azure, i dischi di archiviazione collegati alla VM non possono essere deduplicati.

## <a name="security-and-encryption-support"></a>Supporto sicurezza e crittografia

Backup di Azure supporta la crittografia per i dati in movimento e inattivi.

### <a name="network-traffic-to-azure"></a>Traffico di rete verso Azure

- Il traffico di backup dai server all'insieme di credenziali di Servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.

### <a name="data-security"></a>Sicurezza dei dati

- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Quando si esegue il backup dei dati dai server locali con l'agente MARS, i dati vengono crittografati con una passphrase prima del caricamento in backup di Azure e decrittografati solo dopo essere stati scaricati da backup di Azure.
- Per il backup delle VM di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker su macchine virtuali Windows e **dm-crypt** su macchine virtuali Linux.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
**Computer Windows locali senza DPM/MABS** | ![Sì][green] | ![Sì][green]
**Macchine virtuali di Azure** | ![Sì][green] | ![Sì][green]
**Computer Windows locali o VM di Azure con DPM** | ![Sì][green] | ![Sì][green]
**Computer Windows locali o VM di Azure con MAB** | ![Sì][green] | ![Sì][green]

## <a name="compression-support"></a>Supporto della compressione

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente.

- Per le VM di Azure, l'estensione VM legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, quindi non è necessario comprimere il traffico.
- Se si usa DPM o MABS, per risparmiare larghezza di banda è possibile comprimere i dati prima di eseguirne il backup.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Compressione in insieme di credenziali (HTTPS)**
--- | --- | ---
**Backup diretto di computer Windows locali** | ND | ![Sì][green]
**Backup di VM di Azure tramite l'estensione della VM** | ND | ND
**Backup in computer locali/macchine virtuali di Azure usando MABS/DPM** | ![Sì][green] | ![Sì][green]

## <a name="retention-limits"></a>Limiti di conservazione

**Impostazione** | **Limiti**
--- | ---
**Numero massimo di punti di ripristino per ogni istanza protetta (computer o carico di lavoro)** | 9\.999
**Tempo massimo prima della scadenza di un punto di ripristino** | Nessun limite
**Frequenza massima di backup in DPM/MABS** | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro
**Frequenza massima di backup in insieme di credenziali** | **Computer Windows locali o VM di Azure che eseguono MARS:** tre volte al giorno<br/><br/> **DPM/MABS:** due volte al giorno<br/><br/> **Backup di VM di Azure:** una volta al giorno
**Conservazione del punto di ripristino** | Giornaliera, settimanale, mensile, annuale
**Periodo massimo di conservazione** | Dipende dalla frequenza dei backup
**Punti di ripristino su disco DPM/MABS** | 64 per i file server, 448 per i server applicazioni <br/><br/>Punti di ripristino su nastro illimitati per DPM locale

## <a name="cross-region-restore"></a>Ripristino tra aree

Backup di Azure ha aggiunto la funzionalità di ripristino tra aree per rafforzare la disponibilità dei dati e la capacità di resilienza, offrendo ai clienti il controllo completo per ripristinare i dati in un'area secondaria. Per configurare questa funzionalità, vedere l'[articolo sul ripristino tra aree](backup-create-rs-vault.md#set-cross-region-restore). Questa funzionalità è supportata nei seguenti tipi di gestione:

| Tipo di gestione di backup | Supportato                                                    | Aree supportate |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Macchina virtuale di Azure               | Sì.   Supportato per VM e VM crittografate con meno di 4 TB di dischi | Tutte le aree pubbliche di Azure.  |
| Agente MARS/locale | No                                                           | N/D               |
| SQL/SAP HANA          | No                                                           | N/D               |
| AFS                    | No                                                           | N/D               |

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
