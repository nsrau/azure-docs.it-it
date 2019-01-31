---
title: Matrice di supporto di Backup di Azure
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883705"
---
# <a name="azure-backup-support-matrix"></a>Matrice di supporto di Backup di Azure

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup dei dati nel cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni e i limiti del supporto per gli scenari e le distribuzioni di Backup di Azure.

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure usa l'insieme di credenziali di Servizi di ripristino per orchestrare e gestire i backup, nonché per archiviare i dati sottoposti a backup.

**Impostazione** | **Dettagli**
--- | ---
Numero di insiemi di credenziali | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
Computer in un insieme di credenziali | Fino a 1000 VM di Azure in un singolo insieme di credenziali.<br/><br/> È possibile registrare in un singolo insieme di credenziali fino a 50 computer locali che eseguono l'agente di Backup di Azure (agente di Servizi di ripristino di Microsoft Azure).
Origine dati nella risorsa di archiviazione dell'insieme di credenziali | Massimo 54400 GB. Non esistono limiti per i backup delle macchine virtuali di Azure.
Backup in un insieme di credenziali | Macchine virtuali di Azure: una volta al giorno; computer protetti da DPM/MABS: due volte al giorno; computer sottoposti a backup direttamente tramite l'agente MARS: tre volte al giorno.  
Spostamento degli insiemi di credenziali | Per spostare un insieme di credenziali di Servizi di ripristino, è necessario registrarsi in un'anteprima privata. Per provare, scrivere a AskAzureBackupTeam@microsoft.com.
Spostamento dei dati tra insiemi di credenziali | Lo spostamento dei dati sottoposti a backup tra insiemi di credenziali non è supportato.
Tipo di replica di archiviazione | È possibile modificare il tipo di replica di archiviazione (archiviazione con ridondanza geografica/locale) per un insieme di credenziali prima dell'archiviazione dei backup. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.



## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Se si vuole eseguire il backup dei computer locali, sono supportati gli scenari seguenti.

**Computer** | **Posizione** | **Eseguire il backup** | **Funzionalità**
--- | --- | --- | ---
**Sistemi Windows fisici/virtuali (nessun server di backup)** | File, cartelle, stato del sistema | Backup eseguito in un insieme di credenziali di Servizi di ripristino | Backup eseguito tre volte al giorno.<br/><br/> Backup senza riconoscimento delle app.<br/><br/> Ripristino di file, cartelle, volumi.
**Sistemi Linux fisici/virtuali (nessun server di backup)** | Backup non supportato.
**Sistemi fisici/virtuali con DPM** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup eseguito in DPM (in un disco collegato in locale al server DPM o su nastro).<br/><br/> DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.
**Sistemi fisici/virtuali con MABS** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup eseguito in MABS (in un disco collegato in locale al server MABS). Nastro non supportato<br/><br/> Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.


## <a name="azure-vms"></a>Macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
Dischi di dati delle VM di Azure | Limite di 16.
Dimensioni dei dischi di dati delle VM di Azure | Ogni singolo disco può avere dimensioni fino a 4095 GB.


### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Se si vuole eseguire il backup delle VM di Azure, sono supportati gli scenari seguenti.

**Computer** | **Posizione** | **Eseguire il backup** | **Funzionalità**
--- | --- | --- | ---
**VM di Azure (nessun server di backup)** | File, cartelle, stato del sistema | Backup eseguito nell'insieme di credenziali. | Backup eseguito una volta al giorno.<br/><br/> Backup con riconoscimento delle app per le VM Windows, backup coerenti con i file per le VM Linux. È possibile configurare la coerenza con le app per i computer Linux tramite script personalizzati.<br/><br/> VM/disco di ripristino.<br/><br/> Non è possibile eseguire il backup di una VM di Azure in una posizione locale.
**VM di Azure con DPM** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup eseguito in DPM in esecuzione in Azure (in un disco collegato in locale al server DPM). Nastro non supportato.<br/><br/> DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, stato del sistema, dati delle app. | Backup eseguito su MABS in esecuzione in Azure (in un disco collegato in locale al server MABS). Nastro non supportato<br/><br/> Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/>. Oracle non supportato.





## <a name="linux-backup-support"></a>Supporto di backup di Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Backup** | **Linux (approvato per Azure)**
--- | ---
**Computer Linux locale (senza DPM o MABS)**. |  No. L'agente MARS può essere installato solo in computer Windows.
**VM di Azure (senza DPM o MABS)** | Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Computer locale/VM di Azure con DPM** | Backup coerenti con i file di macchine virtuali guest Linux in Hyper-V e VMWare<br/><br/> Ripristino di macchine virtuali guest Linux in Hyper-V e VMWare</br></br> Backup coerente con i file non disponibile per le macchine virtuali di Azure
**Computer locale/VM di Azure con MABS** | Backup coerenti con i file di macchine virtuali guest Linux in Hyper-V e VMWare<br/><br/> Ripristino di macchine virtuali guest Linux Hyper-V e VMWare</br></br> Backup coerente con i file non disponibile per le VM di Azure.

## <a name="disk-support"></a>Supporto dei dischi

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
Computer Windows locali senza DPM/MABS | ![Yes][green] | ![Yes][green]
Macchine virtuali di Azure | ![Yes][green] | ![Yes][green]
Computer locali/VM di Azure con DPM | ![Yes][green] | ![Yes][green]
Computer locali/VM di Azure con MABS | ![Yes][green] | ![Yes][green]



## <a name="compression-support"></a>Supporto della compressione

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Si noti che:

- Per le VM di Azure, l'estensione macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, quindi non è necessario comprimere il traffico.
- Se si usa DPM o MABS, è possibile comprimere i dati prima di eseguirne il backup in questi server per risparmiare larghezza di banda.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Compressione (HTTPS) in insieme di credenziali**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ND | Yes
Macchine virtuali di Azure | ND | ND
Computer locali/VM di Azure con DPM | ![Yes][green] | ![Yes][green]
Computer locali/VM di Azure con MABS | ![Yes][green] | ![Yes][green]



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

- [Eseguire un backup delle VM di Azure](backup-azure-arm-vms-prepare.md)
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
