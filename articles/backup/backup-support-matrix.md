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
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400171"
---
# <a name="azure-backup-support-matrix"></a>Matrice di supporto di Backup di Azure

È possibile usare [Backup di Azure](backup-overview.md) per eseguire il backup dei dati alla piattaforma cloud di Microsoft Azure. Questo articolo riepiloga le impostazioni di supporto generali e limitazioni per le distribuzioni e gli scenari di Backup di Azure.

Sono disponibili altre matrici di supporto:

- Matrice di supporto per [backup macchina virtuale di Azure (VM)](backup-support-matrix-iaas.md)
- Matrice di supporto per eseguire il backup tramite [System Center Data Protection Manager (DPM) o Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matrice di supporto per il backup usando il [dell'agente di Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Supporto degli insiemi di credenziali

Backup di Azure Usa gli insiemi di credenziali di servizi di ripristino per orchestrare e gestire i backup. Utilizza inoltre gli insiemi di credenziali per archiviare i dati di backup.

Nella tabella seguente vengono descritte le funzionalità di insiemi di credenziali di servizi di ripristino:

**Funzionalità** | **Dettagli**
--- | ---
**Insiemi di credenziali nella sottoscrizione** | Fino a 500 insiemi di credenziali di Servizi di ripristino in una sottoscrizione singola.
**Computer in un insieme di credenziali** | Fino a 1000 macchine virtuali di Azure in un unico insieme di credenziali.<br/><br/> In un singolo insieme di credenziali possono essere registrati fino a 50 server MABS.
**Origini dati nell'archivio dell'insieme di credenziali** | Massimo 54.400 GB. Non esistono limiti per i backup delle macchine virtuali di Azure.
**Backup in un insieme di credenziali** | **Macchine virtuali di Azure:** Una volta al giorno.<br/><br/>**Macchine protette da DPM/MABS:** Due volte al giorno.<br/><br/> **Il backup delle macchine direttamente tramite l'agente MARS:** Tre volte al giorno.
**Backup tra insiemi di credenziali** | Il backup avviene all'interno di un'area.<br/><br/> È necessario un insieme di credenziali in ogni area di Azure che contiene macchine virtuali di cui si vuole eseguire il backup. Non è possibile eseguire il backup in un'altra area.
**Spostare insiemi di credenziali** | È possibile [spostare gli insiemi di credenziali](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) tra le sottoscrizioni o tra gruppi di risorse nella stessa sottoscrizione.
**Spostamento dei dati tra insiemi di credenziali** | Non è supportato lo spostamento di dati di backup tra gli insiemi di credenziali.
**Modifica del tipo di archiviazione dell'insieme di credenziali** | È possibile modificare il tipo di replica archiviazione (archiviazione con ridondanza geografica o archiviazione con ridondanza locale) per un insieme di credenziali prima che i backup vengono archiviati. Dopo l'avvio dei backup nell'insieme di credenziali, il tipo di replica non può essere modificato.

## <a name="on-premises-backup-support"></a>Supporto del backup in locale

Ecco cosa è supportata se si desidera eseguire il backup di macchine virtuali locali:

**Computer** | **Ciò che viene eseguito il backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup diretto di computer Windows con l'agente MARS** | File, cartelle, stato del sistema | Eseguire il backup dell'insieme di credenziali di servizi di ripristino. | Eseguire il backup tre volte al giorno<br/><br/> Nessun backup basato sulle app<br/><br/> Ripristinare file, cartelle, volumi
**Backup diretto di computer Linux con l'agente MARS** | Backup non è supportato
**Eseguire il backup DPM** | File, cartelle, volumi, dello stato del sistema, i dati delle app | Backup in un archivio DPM locale. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e ripristino<br/><br/> Linux è supportato per le macchine virtuali (Hyper-V o VMware)<br/><br/> Oracle non supportata
**Eseguire il backup in backup di Microsoft AZURE** | File, cartelle, volumi, dello stato del sistema, i dati delle app | Backup in un archivio MABS locale. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento delle app<br/><br/> Granularità completa per il backup e ripristino<br/><br/> Linux è supportato per le macchine virtuali (Hyper-V o VMware)<br/><br/> Oracle non supportata

## <a name="azure-vm-backup-support"></a>Supporto per il backup di macchine virtuali di Azure

### <a name="azure-vm-limits"></a>Limiti delle VM di Azure

**Limite** | **Dettagli**
--- | ---
**Dischi di dati della macchina virtuale di Azure** | Limite di 16
**Dimensioni del disco dati della macchina virtuale Azure** | I singoli dischi possono essere fino a 4.095 GB

### <a name="azure-vm-backup-options"></a>Opzioni di backup delle VM di Azure

Ecco cosa è supportata se si desidera eseguire il backup di macchine virtuali di Azure:

**Computer** | **Ciò che viene eseguito il backup** | **Posizione** | **Funzionalità**
--- | --- | --- | ---
**Backup di macchine Virtuali di Azure usando l'estensione della macchina virtuale** | Intera macchina virtuale | Eseguire il backup nell'insieme di credenziali. | Estensione installata quando si abilita il backup per una macchina virtuale.<br/><br/> Eseguire il backup una volta al giorno.<br/><br/> Backup basato sulle App per le macchine virtuali Windows; backup coerenti con i file per le macchine virtuali Linux. È possibile configurare la coerenza delle app per le macchine Linux usando script personalizzati.<br/><br/> Ripristino della macchina virtuale o un disco.<br/><br/> Non è possibile eseguire il backup una VM di Azure in un percorso locale.
**Backup di macchine Virtuali di Azure usando l'agente MARS** | File, cartelle, stato del sistema | Eseguire il backup nell'insieme di credenziali. | Eseguire il backup tre volte al giorno.<br/><br/> Se si desidera eseguire il backup di specifici file o cartelle anziché l'intera macchina virtuale, è possibile eseguire l'agente MARS insieme all'estensione di macchina virtuale.
**VM di Azure con DPM** | File, cartelle, volumi, dello stato del sistema, i dati delle app | Eseguire il backup nell'archivio locale della macchina virtuale di Azure che esegue DPM. DPM esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento dell'applicazione.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.
**VM di Azure con MABS** | File, cartelle, volumi, dello stato del sistema, i dati delle app | Eseguire il backup nell'archivio locale della macchina virtuale di Azure che esegue MABS. Il server MABS esegue quindi il backup nell'insieme di credenziali. | Snapshot con riconoscimento dell'applicazione.<br/><br/> Piena granularità per backup e ripristino.<br/><br/> Linux supportato per le VM (Hyper-V/VMware).<br/><br/> Oracle non supportato.

## <a name="linux-backup-support"></a>Supporto di backup di Linux

Ecco cosa è supportata se si desidera eseguire il backup di macchine Linux:

**Tipo di backup** | **Linux (approvato per Azure)**
--- | ---
**Backup diretto del computer locale che esegue Linux** | Non supportati. L'agente MARS può essere installato solo nei computer Windows.
**Uso di estensione dell'agente per eseguire il backup di macchine Virtuali di Azure che esegue Linux** | Backup coerente con l'App usando [gli script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Ripristino a livello di file.<br/><br/> Ripristino tramite creazione di una VM da un punto di ripristino o da disco.
**Utilizzo di DPM per eseguire il backup in locale o macchina virtuale di Azure che esegue Linux** | Backup coerenti con i file delle macchine virtuali Guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino delle macchine Virtuali di Hyper-V e VMWare le macchine virtuali Guest di Linux.<br/><br/> Backup coerenti con i file non è disponibile per macchine Virtuali di Azure.
**Uso di MABS per eseguire il backup nella macchina locale o macchina virtuale di Azure che esegue Linux** | Backup coerenti con i file delle macchine virtuali Guest Linux in Hyper-V e VMWare.<br/><br/> Ripristino delle macchine Virtuali di Hyper-V e macchine virtuali guest VMWare Linux.<br/><br/> Backup coerente con i file non disponibile per le VM di Azure.

## <a name="daylight-saving-time-support"></a>Supporto dell'ora legale

Backup di Azure non supporta la regolazione automatica dell'orologio per l'ora legale per i backup di macchine Virtuali di Azure. Modificare manualmente i criteri di backup in base alle necessità.

## <a name="disk-deduplication-support"></a>Supporto per la deduplicazione dei dischi

La deduplicazione dei dischi è supportata come segue:

- Deduplicazione dei dischi è supportata in locale quando si usa Data Protection Manager o backup di Microsoft Azure per eseguire il backup di macchine virtuali Hyper-V che eseguono Windows. Windows Server esegue la deduplicazione dei dati (a livello di host) nei dischi rigidi virtuali (VHD) collegati alla macchina virtuale come archivio di backup.
- La deduplicazione non è supportata in Azure per nessun componente di Backup. Quando DPM e MABS vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.

## <a name="security-and-encryption-support"></a>Supporto della protezione e crittografia

Backup di Azure supporta la crittografia per i dati in transito e inattivi.

### <a name="network-traffic-to-azure"></a>Traffico di rete in Azure

- Il traffico di backup dai server all'insieme di credenziali di servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di servizi di ripristino in forma crittografata.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

    > [!WARNING]
    > Dopo aver configurato l'insieme di credenziali, solo il cliente ha accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

### <a name="data-security"></a>Sicurezza dei dati

- Quando esegue il backup di macchine virtuali di Azure, è necessario impostare la crittografia *all'interno di* la macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker su macchine virtuali Windows e **dm-crypt** su macchine virtuali Linux.
- Nel back-end, Usa Backup di Azure [crittografia del servizio di archiviazione Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
**Macchine virtuali Windows locali senza DPM/MABS** | ![Yes][green] | ![Yes][green]
**Macchine virtuali di Azure** | ![Yes][green] | ![Yes][green]
**Macchine virtuali Windows locali o macchine virtuali di Azure con DPM** | ![Yes][green] | ![Yes][green]
**Macchine virtuali Windows locali o macchine virtuali di Azure con backup di Microsoft AZURE** | ![Yes][green] | ![Yes][green]

## <a name="compression-support"></a>Supporto della compressione

Backup supporta la compressione dei backup del traffico, come riepilogato nella tabella seguente.

- Per le VM di Azure, l'estensione macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, in modo che non è necessario comprimere il traffico.
- Se si usa DPM e MABS, è possibile risparmiare larghezza di banda con la compressione dei dati prima di cui viene eseguito il backup.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimere all'insieme di credenziali (HTTPS)**
--- | --- | ---
**Backup diretto di computer Windows locali** | NA | ![Yes][green]
**Backup di macchine virtuali di Azure tramite l'estensione della macchina virtuale** | NA | NA
**Backup con DPM/MABS prevede computer on-premises/Azure** | ![Yes][green] | ![Yes][green]

## <a name="retention-limits"></a>Limiti di conservazione

**Impostazione** | **Limiti**
--- | ---
**Max punti di ripristino per ogni istanza protetta (computer o del carico di lavoro)** | 9,999
**Ora di scadenza massimo per un punto di ripristino** | Nessun limite
**Frequenza massima di backup per DPM/MABS** | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro
**Frequenza massima di backup nell'insieme di credenziali** | **In locale i computer Windows o macchine virtuali di Azure che eseguono MARS:** Tre al giorno<br/><br/> **DPM/MABS:** due volte al giorno<br/><br/> **Backup di macchine Virtuali di Azure:** Uno per ogni giorno
**Conservazione del punto di ripristino** | Giornaliera, settimanale, mensile, annuale
**Periodo di conservazione massimo** | Dipende dalla frequenza dei backup
**Punti di ripristino su disco DPM/MABS** | 64 per i file server; 448 per i server di app <br/><br/>Punti di ripristino di nastri senza limiti per DPM in locale

## <a name="next-steps"></a>Passaggi successivi

- [Vedere la matrice di supporto](backup-support-matrix-iaas.md) per il backup di macchine virtuali di Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
