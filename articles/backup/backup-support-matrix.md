---
title: Matrice di supporto di Azure Backup
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 788da5708178cfa533d4382a4a417cef73f46a5e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254914"
---
# <a name="support-matrix-for-azure-backup"></a>Matrice di supporto per backup di Azure

È possibile usare [backup di Azure](backup-overview.md) per eseguire il backup dei dati nella piattaforma Microsoft Azure cloud. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per gli scenari e le distribuzioni di backup di Azure.

Sono disponibili altre matrici di supporto:

- Matrice di supporto per il [backup di macchine virtuali (VM) di Azure](backup-support-matrix-iaas.md)
- Matrice di supporto per il backup mediante [System Center Data Protection Manager (DPM)/Microsoft Server di backup di Azure (MAB)](backup-support-matrix-mabs-dpm.md)
- Matrice di supporto per il backup tramite l' [agente di servizi di ripristino di Microsoft Azure (Mars)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure usa insiemi di credenziali dei servizi di ripristino per orchestrare e gestire i backup. USA anche gli insiemi di credenziali per archiviare i dati di cui è stato eseguito il backup.

La tabella seguente descrive le funzionalità degli insiemi di credenziali di servizi di ripristino:

**Funzionalità** | **Dettagli**
--- | ---
**Insiemi di credenziali nella sottoscrizione** | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
**Computer in un insieme di credenziali** | Fino a 1.000 VM di Azure in un unico insieme di credenziali.<br/><br/> In un singolo insieme di credenziali possono essere registrati fino a 50 server MABS.
**Origini dei dati** | Le dimensioni massime di una singola [origine dati](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) sono di 54.400 GB. Questo limite non si applica ai backup di macchine virtuali di Azure. Nessun limite si applica alla quantità totale di dati di cui è possibile eseguire il backup nell'insieme di credenziali.
**Backup in un insieme di credenziali** | **Macchine virtuali di Azure:** Una volta al giorno.<br/><br/>**Computer protetti da DPM/MAB:** Due volte al giorno.<br/><br/> **Computer sottoposti a backup direttamente usando l'agente Mars:** Tre volte al giorno.
**Backup tra insiemi di credenziali** | Il backup avviene all'interno di un'area.<br/><br/> È necessario un insieme di credenziali in ogni area di Azure che contiene macchine virtuali di cui si vuole eseguire il backup. Non è possibile eseguire il backup in un'altra area.
**Sposta insiemi di credenziali** | È possibile [spostare](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) gli insiemi di credenziali tra le sottoscrizioni o tra gruppi di risorse nella stessa sottoscrizione. Tuttavia, lo trasferimento di insiemi di credenziali tra aree non è supportato.
**Spostamento dei dati tra insiemi di credenziali** | Lo stato di trasferimento dei dati di backup tra insiemi di credenziali non è supportato.
**Modifica del tipo di archiviazione dell'insieme di credenziali** | È possibile modificare il tipo di replica di archiviazione, ovvero l'archiviazione con ridondanza geografica o l'archiviazione con ridondanza locale, per un insieme di credenziali prima che vengano archiviati i backup. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.

## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Ecco cosa è supportato se si vuole eseguire il backup dei computer locali:

**Computer** | **Elementi di cui è stato eseguito il backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup diretto di computer Windows con l'agente MARS** | File, cartelle, stato del sistema | Eseguire il backup nell'insieme di credenziali di servizi di ripristino. | Backup tre volte al giorno<br/><br/> Nessun backup compatibile con l'app<br/><br/> Ripristino di file, cartelle, volumi
**Backup diretto di computer Linux con l'agente MARS** | Backup non supportato
**Esegui backup in DPM** | File, cartelle, volumi, stato del sistema, dati dell'app | Backup in un archivio DPM locale. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e il ripristino<br/><br/> Linux supportato per le macchine virtuali (Hyper-V/VMware)<br/><br/> Oracle non supportato
**Eseguire il backup in MAB** | File, cartelle, volumi, stato del sistema, dati dell'app | Backup in un archivio MABS locale. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e il ripristino<br/><br/> Linux supportato per le macchine virtuali (Hyper-V/VMware)<br/><br/> Oracle non supportato

## <a name="azure-vm-backup-support"></a>Supporto per il backup di macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
**Dischi dati delle macchine virtuali di Azure** | Limite di 16 <br> Per iscriversi all'anteprima privata delle macchine virtuali con più di 16 dischi (massimo 32), scrivere all'indirizzo AskAzureBackupTeam@microsoft.com
**Dimensioni del disco dati della macchina virtuale di Azure** | Le dimensioni del disco singolo possono essere fino a 32 TB e un massimo di 256 TB combinati per tutti i dischi in una macchina virtuale.

### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Ecco cosa è supportato se si vuole eseguire il backup di macchine virtuali di Azure:

**Computer** | **Elementi di cui è stato eseguito il backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup di macchine virtuali di Azure tramite l'estensione VM** | Intera macchina virtuale | Eseguire il backup nell'insieme di credenziali. | Estensione installata quando si abilita il backup per una macchina virtuale.<br/><br/> Eseguire il backup una volta al giorno.<br/><br/> Backup compatibile con le app per macchine virtuali Windows; backup coerente con i file per le macchine virtuali Linux. È possibile configurare la coerenza delle app per i computer Linux usando script personalizzati.<br/><br/> Ripristinare una macchina virtuale o un disco.<br/><br/> Non è possibile eseguire il backup di una macchina virtuale di Azure in un percorso locale.
**Backup di macchine virtuali di Azure con l'agente MARS** | File, cartelle, stato del sistema | Eseguire il backup nell'insieme di credenziali. | Eseguire il backup tre volte al giorno.<br/><br/> Se si vuole eseguire il backup di file o cartelle specifici anziché dell'intera VM, l'agente MARS può essere eseguito insieme all'estensione della macchina virtuale.
**VM di Azure con DPM** | File, cartelle, volumi, stato del sistema, dati dell'app | Eseguire il backup nell'archiviazione locale della macchina virtuale di Azure in cui è in esecuzione DPM. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot compatibili con le app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, stato del sistema, dati dell'app | Eseguire il backup nell'archiviazione locale della macchina virtuale di Azure che esegue MAB. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot compatibili con le app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.

## <a name="linux-backup-support"></a>Supporto di backup di Linux

Ecco cosa è supportato se si vuole eseguire il backup di computer Linux:

**Tipo di backup** | **Linux (approvato per Azure)**
--- | ---
**Backup diretto del computer locale che esegue Linux** | Non supportato. L'agente MARS può essere installato solo nei computer Windows.
**Uso dell'estensione Agent per eseguire il backup di una macchina virtuale di Azure che esegue Linux** | Backup coerente con l'app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Uso di DPM per eseguire il backup di computer locali che eseguono Linux** | Backup coerente con i file delle macchine virtuali guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino di VM di VM guest Hyper-V e VMWare Linux.
**Uso di MAB per eseguire il backup di computer locali che eseguono Linux** | Backup coerente con i file delle macchine virtuali guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino di VM di VM guest Hyper-V e VMWare Linux.
**Uso di Mab o DPM per eseguire il backup di macchine virtuali Linux di Azure** | Non supportato.

## <a name="daylight-saving-time-support"></a>Supporto per l'ora legale

Backup di Azure non supporta la regolazione automatica dell'orologio per l'ora legale per i backup di VM di Azure. Modificare manualmente i criteri di backup in base alle necessità.

## <a name="disk-deduplication-support"></a>Supporto per la deduplicazione dei dischi

La deduplicazione dei dischi è supportata come segue:

- La deduplicazione del disco è supportata in locale quando si usa DPM o MAB per eseguire il backup di macchine virtuali Hyper-V che eseguono Windows. Windows Server esegue la deduplicazione dei dati (a livello di host) sui dischi rigidi virtuali (VHD) collegati alla macchina virtuale come archivio di backup.
- La deduplicazione non è supportata in Azure per nessun componente di Backup. Quando DPM e MAB vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.

## <a name="security-and-encryption-support"></a>Supporto per sicurezza e crittografia

Backup di Azure supporta la crittografia per i dati in transito e inattivi.

### <a name="network-traffic-to-azure"></a>Traffico di rete in Azure

- Il traffico di backup dai server all'insieme di credenziali dei servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di servizi di ripristino in formato crittografato.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

    > [!WARNING]
    > Dopo aver configurato l'insieme di credenziali, solo il cliente ha accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

### <a name="data-security"></a>Sicurezza dei dati

- Quando si esegue il backup di macchine virtuali di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker su macchine virtuali Windows e **dm-crypt** su macchine virtuali Linux.
- Nel back-end, backup di Azure usa [crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
**Computer Windows locali senza DPM/MAB** | ![Sì][green] | ![Sì][green]
**Macchine virtuali di Azure** | ![Sì][green] | ![Sì][green]
**Computer Windows locali o macchine virtuali di Azure con DPM** | ![Sì][green] | ![Sì][green]
**Computer Windows locali o macchine virtuali di Azure con MAB** | ![Sì][green] | ![Sì][green]

## <a name="compression-support"></a>Supporto della compressione

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente.

- Per le macchine virtuali di Azure, l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure tramite la rete di archiviazione, pertanto non è necessario comprimere il traffico.
- Se si usa DPM o MAB, è possibile risparmiare larghezza di banda comprimendo i dati prima di eseguirne il backup.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimi nell'insieme di credenziali (HTTPS)**
--- | --- | ---
**Backup diretto di computer Windows locali** | ND | ![Sì][green]
**Backup di macchine virtuali di Azure con l'estensione VM** | ND | ND
**Eseguire il backup in computer locali o Azure usando MAB/DPM** | ![Sì][green] | ![Sì][green]

## <a name="retention-limits"></a>Limiti di conservazione

**Impostazione** | **Limiti**
--- | ---
**Numero massimo di punti di ripristino per istanza protetta (computer o carico di lavoro)** | 9\.999
**Tempo di scadenza massimo per un punto di ripristino** | Nessun limite
**Frequenza massima di backup per DPM/MAB** | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro
**Frequenza massima di backup nell'insieme di credenziali** | **Computer Windows locali o macchine virtuali di Azure che eseguono Mars:** Tre al giorno<br/><br/> **DPM/MAB:** Due al giorno<br/><br/> **Backup delle macchine virtuali di Azure:** Uno al giorno
**Conservazione del punto di ripristino** | Giornaliera, settimanale, mensile, annuale
**Periodo di conservazione massimo** | Dipende dalla frequenza dei backup
**Punti di ripristino nel disco DPM/MAB** | 64 per i file server; 448 per i server applicazioni <br/><br/>Punti di ripristino su nastro illimitati per DPM locale

## <a name="cross-region-restore"></a>Ripristino tra aree

Backup di Azure ha aggiunto la funzionalità di ripristino tra aree per rafforzare la disponibilità dei dati e la capacità di resilienza, offrendo ai clienti il controllo completo per ripristinare i dati in un'area secondaria. Per configurare questa funzionalità, vedere [l'articolo set Cross Region Restore.](backup-create-rs-vault.md#set-cross-region-restore) Questa funzionalità è supportata per i tipi di gestione seguenti:

| Tipo di gestione di backup | Supportato                                                    | Aree supportate |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Macchina virtuale di Azure               | Sì. Anteprima pubblica limitata supportata per macchine virtuali e VM crittografate con meno di 4 TB di dischi | Stati Uniti centro-occidentali   |
| Agente MARS/locale | No                                                           | N/D               |
| /SAP CENTRAL SQL HANA          | No                                                           | N/D               |
| AFS                    | No                                                           | N/D               |

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
