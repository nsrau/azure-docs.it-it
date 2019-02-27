---
title: Matrice di supporto di Backup di Azure
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429571"
---
# <a name="azure-backup-support-matrix"></a>Matrice di supporto di Backup di Azure

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup dei dati nel cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni e i limiti generali del supporto per gli scenari e le distribuzioni di Backup di Azure.

Sono disponibili altre matrici di supporto:

[Matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il backup con un server System Center DPM/Backup di Microsoft Azure (MABS) [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup mediante l'agente di Servizi di ripristino di Microsoft Azure (MARS)

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure usa l'insieme di credenziali di Servizi di ripristino per orchestrare e gestire i backup, nonché per archiviare i dati sottoposti a backup.

**Impostazione** | **Dettagli**
--- | ---
**Insiemi di credenziali nella sottoscrizione** | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
**Computer in un insieme di credenziali** | Fino a 1000 VM di Azure in un singolo insieme di credenziali.<br/><br/> In un singolo insieme di credenziali possono essere registrati fino a 50 server MABS.
**Origini dati nell'archivio dell'insieme di credenziali** | Massimo 54400 GB. Non esistono limiti per i backup delle macchine virtuali di Azure.
**Backup in un insieme di credenziali** | Macchine virtuali di Azure: una volta al giorno<br/><br/>Computer protetti da DPM/MABS: due volte al giorno<br/><br/> Computer sottoposti a backup direttamente tramite l'agente MARS: tre volte al giorno. 
**Backup tra insiemi di credenziali** | Il backup avviene all'interno di un'area.<br/><br/> È necessario un insieme di credenziali in ogni area di Azure che contiene macchine virtuali di cui si vuole eseguire il backup. Non è possibile eseguire il backup in un'altra area. 
**Spostamento degli insiemi di credenziali** | È possibile [spostare insiemi di credenziali](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) fra sottoscrizioni o fra gruppi di risorse nella stessa sottoscrizione.
**Spostamento dei dati tra insiemi di credenziali** | Lo spostamento dei dati sottoposti a backup tra insiemi di credenziali non è supportato.
**Modifica del tipo di archiviazione dell'insieme di credenziali** | È possibile modificare il tipo di replica di archiviazione (archiviazione con ridondanza geografica/locale) per un insieme di credenziali prima dell'archiviazione dei backup. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.



## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Se si vuole eseguire il backup dei computer locali, sono supportati gli scenari seguenti.

**Computer** | **Backup eseguito** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup diretto di computer Windows con l'agente MARS** | File, cartelle, stato del sistema | Backup in un insieme di credenziali di Servizi di ripristino | Backup eseguito tre volte al giorno.<br/><br/> Backup senza riconoscimento delle app.<br/><br/> Ripristino di file, cartelle, volumi.
**Backup diretto di computer Linux con l'agente MARS** | Backup non supportato.
**Backup in DPM** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup in un archivio DPM locale. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.
**Backup in MABS** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup in un archivio MABS locale. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.


## <a name="azure-vm-backup-support"></a>Supporto per il backup di macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
Dischi di dati delle VM di Azure | Limite di 16.
Dimensioni dei dischi di dati delle VM di Azure | Ogni singolo disco può avere dimensioni fino a 4095 GB.


### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Se si vuole eseguire il backup delle VM di Azure, sono supportati gli scenari seguenti.

**Computer** | **Backup eseguito** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup di macchine virtuali di Azure tramite l'estensione di macchina virtuale** | Intera macchina virtuale | Backup nell'insieme di credenziali | Estensione installata quando si abilita il backup per una macchina virtuale.<br/><br/> Backup eseguito una volta al giorno.<br/><br/> Backup con riconoscimento delle app per le VM Windows, backup coerenti con i file per le VM Linux. È possibile configurare la coerenza con le app per i computer Linux tramite script personalizzati.<br/><br/> VM/disco di ripristino.<br/><br/> Non è possibile eseguire il backup di una VM di Azure in una posizione locale.
**Backup di macchine virtuali di Azure tramite l'agente MARS** | File, cartelle | Backup nell'insieme di credenziali | Backup eseguito tre volte al giorno.<br/><br/> L'agente MARS può essere eseguito insieme all'estensione di macchina virtuale se si vuole eseguire il backup di cartelle/file specifici invece che dell'intera macchina virtuale.
**VM di Azure con DPM** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup nell'archivio locale della macchina virtuale di Azure che esegue DPM. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup nell'archivio locale della macchina virtuale di Azure che esegue MABS. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.





## <a name="linux-backup-support"></a>Supporto di backup di Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Backup** | **Linux (approvato per Azure)**
--- | ---
**Backup diretto di un computer locale che esegue Linux**. |  No. L'agente MARS può essere installato solo in computer Windows.
**Backup di una macchina virtuale di Azure che esegue Linux (mediante l'estensione agente)** | Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Backup locale o di una macchina virtuale di Azure che esegue Linux tramite DPM** | Backup coerenti con i file di macchine virtuali guest Linux in Hyper-V e VMWare<br/><br/> Ripristino di macchine virtuali guest Linux in Hyper-V e VMWare</br></br> Backup coerente con i file non disponibile per le macchine virtuali di Azure
**Backup di un computer locale o una macchina virtuale di Azure che esegue Linux tramite MABS** | Backup coerenti con i file di macchine virtuali guest Linux in Hyper-V e VMWare<br/><br/> Ripristino di macchine virtuali guest Linux Hyper-V e VMWare</br></br> Backup coerente con i file non disponibile per le VM di Azure.

## <a name="daylight-saving-support"></a>Supporto dell'ora legale

Backup di Azure non supporta la regolazione automatica dell'orologio in base all'ora legale per i backup delle macchine virtuali di Azure. Modificare manualmente i criteri di backup in base alle necessità.


## <a name="disk-deduplication-support"></a>Supporto per la deduplicazione dei dischi

La deduplicazione dei dischi è supportata come segue:
- La deduplicazione dei dischi è supportata in locale se si usano DPM o MABS per eseguire il backup di VM Hyper-V che eseguono Windows. Windows Server esegue la deduplicazione dei dati a livello di host nei dischi rigidi virtuali (VHD) collegati alla macchina virtuale come archivio di backup.
- La deduplicazione non è supportata in Azure per nessun componente di Backup. Se System Center DPM e il server di Backup vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.


## <a name="securityencryption-support"></a>Supporto di funzionalità di sicurezza/crittografia

Backup di Azure supporta la crittografia per i dati in movimento e inattivi:

Traffico di rete verso Azure:
- Il traffico di backup dai server all'insieme di credenziali di Servizi di ripristino viene crittografato con Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.
    > [!WARNING]
    > Dopo aver configurato l'insieme di credenziali, solo il cliente ha accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.
Sicurezza dei dati:
- Per il backup delle VM di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker su macchine virtuali Windows e **dm-crypt** su macchine virtuali Linux.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.


**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Sì][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]



## <a name="compression-support"></a>Supporto della compressione

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. 

- Per le VM di Azure, l'estensione macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, quindi non è necessario comprimere il traffico.
- Se si usa DPM o MABS, è possibile comprimere i dati prima di eseguirne il backup in questi server per risparmiare larghezza di banda.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Compressione (HTTPS) in insieme di credenziali**
--- | --- | ---
**Backup diretto di computer Windows locali** | ND | Sì
**Backup di macchine virtuali di Azure con l'estensione macchina virtuale** | ND | ND
**Backup in computer locali/macchine virtuali di Azure con MABS/DPM | ![Sì][green] | ![Sì][green]



## <a name="retention-limits"></a>Limiti di conservazione

**Impostazione** | **Limiti**
--- | ---
Numero massimo di punti di ripristino per ogni istanza protetta (computer/carico di lavoro) | 9999
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro.
Frequenza massima di backup in insieme di credenziali | Computer Windows locali/VM di Azure che eseguono MARS: tre volte al giorno<br/><br/> DPM/MABS: due volte al giorno<br/><br/> Backup di VM di Azure: Una volta al giorno
Conservazione dei punti di ripristino | Giornaliera, settimanale, mensile, annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per i file server, 448 per i server applicazioni.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
