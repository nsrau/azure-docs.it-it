---
title: Matrice di supporto di Backup di Azure
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273267"
---
# <a name="support-matrix-for-azure-backup"></a>Matrice di supporto per Backup di AzureSupport matrix for Azure Backup

È possibile usare Backup di Azure per eseguire il backup dei dati nella piattaforma cloud di Microsoft Azure.You can use [Azure Backup](backup-overview.md) to back up data to the Microsoft Azure cloud platform. Questo articolo riepiloga le impostazioni e le limitazioni generali del supporto per gli scenari e le distribuzioni di Backup di Azure.This article summarizes the general support settings and limitations for Azure Backup scenarios and deployments.

Sono disponibili altre matrici di supporto:

- Matrice di supporto per il backup di [macchine virtuali (VM) di AzureSupport matrix for Azure virtual machine (VM) backup](backup-support-matrix-iaas.md)
- Matrice di supporto per il backup tramite [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matrice di supporto per il backup tramite [l'agente di Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure usa gli insieme di credenziali di Servizi di ripristino per orchestrare e gestire i backup. Utilizza anche gli insiemi di credenziali per archiviare i dati di cui è stato eseguito il backup.

Nella tabella seguente vengono descritte le funzionalità degli insiemi di credenziali di Servizi di ripristino:

**Funzionalità** | **Dettagli**
--- | ---
**Insiemi di credenziali nella sottoscrizione** | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
**Computer in un insieme di credenziali** | Fino a 1.000 macchine virtuali di Azure in un singolo insieme di credenziali.<br/><br/> In un singolo insieme di credenziali possono essere registrati fino a 50 server MABS.
**Origini dati** | La dimensione massima di una singola [origine dati](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) è 54.400 GB. Questo limite non si applica ai backup delle macchine virtuali di Azure.This limit does not apply to Azure VM backups. Non si applicano limiti alla quantità totale di dati di cui è possibile eseguire il backup nel vault.
**Backup in un insieme di credenziali** | **Macchine virtuali di Azure:Azure VMs:** Una volta al giorno.<br/><br/>**Macchine protette da DPM/MABS:** Due volte al giorno.<br/><br/> **Macchine di cui è stato eseguito il backup direttamente utilizzando l'agente MARS:** Tre volte al giorno.
**Backup tra insiemi di credenziali** | Il backup avviene all'interno di un'area.<br/><br/> È necessario un insieme di credenziali in ogni area di Azure che contiene macchine virtuali di cui si vuole eseguire il backup. Non è possibile eseguire il backup in un'altra area.
**Spostare i vault** | È possibile [spostare gli insiemi](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) di credenziali tra sottoscrizioni o tra gruppi di risorse nella stessa sottoscrizione. Tuttavia, lo spostamento di vault tra aree non è supportato.
**Spostamento dei dati tra insiemi di credenziali** | Lo spostamento dei dati di cui è stato eseguito il backup tra i vault non è supportato.
**Modifica del tipo di archiviazione dell'insieme di credenziali** | È possibile modificare il tipo di replica di archiviazione (archiviazione con ridondanza geografica o archiviazione con ridondanza locale) per un insieme di credenziali prima dell'archiviazione dei backup. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.

## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Ecco cosa è supportato se si vuole eseguire il backup dei computer locali:Here's what's supported if you want to back up on-premises machines:

**Computer** | **Elementi di cui è stato eseguito il backup** | **Percorso** | **Caratteristiche**
--- | --- | --- | ---
**Backup diretto di computer Windows con l'agente MARS** | File, cartelle, stato del sistema | Eseguire il backup nell'insieme di credenziali dei servizi di ripristino. | Eseguire il backup tre volte al giorno<br/><br/> Nessun backup in grado di riconoscere le app<br/><br/> Ripristinare file, cartella, volume
**Backup diretto di computer Linux con l'agente MARS** | Backup non supportato
**Eseguire il backup in DPM** | File, cartelle, volumi, stato del sistema, dati dell'app | Backup in un archivio DPM locale. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e il ripristino<br/><br/> Linux supportato per le macchine virtuali (Hyper-V/VMware)<br/><br/> Oracle non supportato
**Eseguire il backup in MABS** | File, cartelle, volumi, stato del sistema, dati dell'app | Backup in un archivio MABS locale. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e il ripristino<br/><br/> Linux supportato per le macchine virtuali (Hyper-V/VMware)<br/><br/> Oracle non supportato

## <a name="azure-vm-backup-support"></a>Supporto per il backup di macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
**Dischi di dati delle VM di Azure** | Limite di 16 <br> Per iscriversi all'anteprima privata delle macchine virtuali con più di 16 dischi (massimo 32), scrivere all'indirizzo AskAzureBackupTeam@microsoft.com
**Dimensioni dei dischi di dati delle VM di Azure** | Le dimensioni dei singoli dischi possono essere fino a 32 TB e un massimo di 256 TB combinate per tutti i dischi in una macchina virtuale.

### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Ecco cosa è supportato se si vuole eseguire il backup delle macchine virtuali di Azure:Here's what's supported if you want to back up Azure VMs:

**Computer** | **Elementi di cui è stato eseguito il backup** | **Percorso** | **Caratteristiche**
--- | --- | --- | ---
**Backup della macchina virtuale di Azure tramite l'estensione della macchina virtualeAzure VM backup by using VM extension** | Intera macchina virtuale | Torna all'insieme di credenziali. | Estensione installata quando si abilita il backup per una macchina virtuale.<br/><br/> Indietro una volta al giorno.<br/><br/> Backup in grado di riconoscere le app per le macchine virtuali Windows; backup coerente con i file per le macchine virtuali Linux. È possibile configurare la coerenza delle app per i computer Linux usando script personalizzati.<br/><br/> Ripristinare la macchina virtuale o il disco.<br/><br/> Impossibile eseguire il backup di una macchina virtuale di Azure in un percorso locale.
**Backup della macchina virtuale di Azure tramite l'agente MARSAzure VM backup by using MARS agent** | File, cartelle, stato del sistema | Torna all'insieme di credenziali. | Indietro tre volte al giorno.<br/><br/> Se si desidera eseguire il backup di file o cartelle specifici anziché dell'intera macchina virtuale, l'agente MARS può essere eseguito insieme all'estensione della macchina virtuale.
**VM di Azure con DPM** | File, cartelle, volumi, stato del sistema, dati dell'app | Eseguire il backup nell'archiviazione locale della macchina virtuale di Azure che esegue DPM. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot in grado di riconoscere le app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, stato del sistema, dati dell'app | Eseguire il backup nell'archiviazione locale della macchina virtuale di Azure che esegue MABS. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot in grado di riconoscere le app.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.

## <a name="linux-backup-support"></a>Supporto di backup di Linux

Ecco cosa è supportato se si desidera eseguire il backup di macchine Linux:

**Tipo di backup** | **Linux (approvato per Azure)**
--- | ---
**Backup diretto del computer locale che esegue Linux** | Non supportato. L'agente MARS può essere installato solo su computer Windows.
**Uso dell'estensione dell'agente per eseguire il backup della macchina virtuale di Azure che esegue LinuxUsing agent extension to back up Azure VM that's running Linux** | Backup coerente con le app tramite [script personalizzati.](backup-azure-linux-app-consistent.md)<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Utilizzo di DPM per eseguire il backup di computer locali che eseguono Linux** | Backup coerente dei file delle macchine virtuali guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino delle macchine virtuali delle macchine virtuali guest Hyper-V e VMWare Linux.
**Utilizzo di MABS per eseguire il backup di computer locali che eseguono Linux** | Backup coerente dei file delle macchine virtuali guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino delle macchine virtuali delle macchine virtuali guest Hyper-V e VMWare Linux.
**Utilizzo di MABS o DPM per eseguire il backup delle macchine virtuali di Azure LinuxUsing MABS or DPM to back up Linux Azure VMs** | Non supportato.

## <a name="daylight-saving-time-support"></a>Supporto per l'ora legale

Backup di Azure non supporta la regolazione automatica dell'orologio per l'ora legale per i backup delle macchine virtuali di Azure.Azure Backup doesn't support automatic clock adjustment for daylight time time for Azure VM backups. Non sposta l'ora del backup in avanti o all'indietro. Per assicurarsi che il backup venga eseguito all'ora desiderata, modificare manualmente i criteri di backup in base alle esigenze.

## <a name="disk-deduplication-support"></a>Supporto per la deduplicazione dei dischi

La deduplicazione dei dischi è supportata come segue:

- La deduplicazione del disco è supportata in locale quando si utilizza DPM o MABs per eseguire il backup delle macchine virtuali Hyper-V che eseguono Windows. Windows Server esegue la deduplicazione dei dati (a livello di host) nei dischi rigidi virtuali (VHD) collegati alla macchina virtuale come archivio di backup.
- La deduplicazione non è supportata in Azure per nessun componente di Backup. Quando DPM e MABS vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.

## <a name="security-and-encryption-support"></a>Supporto per la sicurezza e la crittografia

Backup di Azure supporta la crittografia per i dati in transito e inattivi.

### <a name="network-traffic-to-azure"></a>Traffico di rete verso AzureNetwork traffic to Azure

- Il traffico di backup dai server all'insieme di credenziali di Servizi di ripristino viene crittografato utilizzando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in forma crittografata.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

    > [!WARNING]
    > Dopo aver configurato l'insieme di credenziali, solo il cliente ha accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

### <a name="data-security"></a>Sicurezza dei dati

- Quando si esegue il backup delle macchine virtuali di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker su macchine virtuali Windows e **dm-crypt** su macchine virtuali Linux.
- Nel back-end, Backup di Azure usa [Crittografia del servizio di archiviazione](../storage/common/storage-service-encryption.md)di Azure , che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
**Computer Windows locali senza DPM/MABS** | ![Sì][green] | ![Sì][green]
**Macchine virtuali di AzureAzure VMs** | ![Sì][green] | ![Sì][green]
**Macchine Windows locali o macchine virtuali di Azure con DPM** | ![Sì][green] | ![Sì][green]
**Macchine Windows locali o macchine virtuali di Azure con MABS** | ![Sì][green] | ![Sì][green]

## <a name="compression-support"></a>Supporto della compressione

Il backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente.

- Per le macchine virtuali di Azure, l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure tramite la rete di archiviazione, pertanto non è necessario comprimere il traffico.
- Se si utilizza DPM o MABS, è possibile risparmiare larghezza di banda comprimendo i dati prima del backup.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimi nel vault (HTTPS)**
--- | --- | ---
**backup diretto di computer Windows locali** | ND | ![Sì][green]
**Backup delle macchine virtuali di Azure tramite l'estensione della macchina virtualeBackup of Azure VMs by using VM extension** | ND | ND
**Eseguire il backup su computer locali/di Azure tramite MABS/DPM** | ![Sì][green] | ![Sì][green]

## <a name="retention-limits"></a>Limiti di conservazione

**Impostazione** | **Limiti**
--- | ---
**Numero massimo di punti di ripristino per istanza protetta (computer o carico di lavoro)Max recovery points per protected instance (machine or workload)** | 9,999
**Tempo massimo prima della scadenza di un punto di ripristino** | Nessun limite
**Frequenza massima di backup in DPM/MABS** | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro
**Frequenza massima di backup in insieme di credenziali** | **Macchine Windows locali o macchine virtuali di Azure che eseguono MARS:On-premises Windows machines or Azure VMs running MARS:** Tre al giorno<br/><br/> **DPM/MABS:** Due al giorno<br/><br/> **Backup della macchina virtuale di Azure:Azure VM backup:** Uno al giorno
**Conservazione dei punti di ripristino** | Giornaliera, settimanale, mensile, annuale
**Periodo massimo di conservazione** | Dipende dalla frequenza dei backup
**Punti di ripristino su disco DPM/MABS** | 64 per i file server; 448 per i server app <br/><br/>Punti di ripristino su nastro illimitati per DPM locale

## <a name="cross-region-restore"></a>Ripristino tra aree

Backup di Azure ha aggiunto la funzionalità di ripristino tra aree per rafforzare la disponibilità e la resilienza dei dati, offrendo ai clienti il controllo completo per ripristinare i dati in un'area secondaria. Per configurare questa funzionalità, vedere [l'articolo Impostare il ripristino tra aree.](backup-create-rs-vault.md#set-cross-region-restore). . Questa funzionalità è supportata per i seguenti tipi di gestione:

| Tipo di gestione backup | Supportato                                                    | Aree supportate |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Macchina virtuale di Azure               | Sì. Anteprima limitata pubblica Supportato per macchine virtuali crittografate e macchine virtuali con dischi inferiori a 4 TBPublic limited Preview Supported for encrypted VMs and VMs with minor than 4-TB disks | Stati Uniti centro-occidentali   |
| Agente MARS/On locali | No                                                           | N/D               |
| SQL /SAP HANA          | No                                                           | N/D               |
| Afs                    | No                                                           | N/D               |

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
