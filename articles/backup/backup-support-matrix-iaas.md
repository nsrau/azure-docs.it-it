---
title: Matrice di supporto del servizio Backup di Azure per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431055"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure
È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per l'esecuzione del backup di macchine virtuali (VM) di Azure con il servizio Backup di Azure.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per il servizio Backup di Azure
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il server di Backup di Microsoft Azure/backup DOM
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di Servizi di ripristino di Microsoft Azure



## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.


**Scenario** | **Backup** | **Agente** |**Ripristino** 
--- | --- | --- | ---
**Backup diretto di macchine virtuali di Azure** | Backup dell'intera macchina virtuale  | Non è necessario alcun agente nella macchina virtuale di Azure. Backup di Azure installa e usa un'estensione dell'[agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino come descritto di seguito:<br/><br/> - **Creare una macchina virtuale di base**. Questa operazione è utile se la macchina virtuale non ha configurazioni speciali, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco e quindi collegarlo a una macchina virtuale esistente oppure creare una nuova macchina virtuale dal disco con PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare cartelle e file specifici di una macchina virtuale, anziché l'intera macchina virtuale.  
**Backup diretto di macchine virtuali di Azure (solo Windows)** | Backup di file, cartelle e volumi specifici | Installare l'[agente di Servizi di ripristino di Microsoft Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
**Backup di una macchina virtuale di Azure nel server di backup** |  Backup di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app in System Center DPM o nel server di Backup di Microsoft Azure.<br/><br/> DPM o il server di Backup di Microsoft Azure esegue quindi il backup nell'insieme di credenziali di backup | Installare l'agente di protezione di DPM o del server di Backup di Microsoft Azure nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app. 

Vedere altre informazioni sul backup con un server di backup(backup-architecture.md#architecture-back-up-to-dpmmabs) e sui [requisiti per il supporto](backup-support-matrix-mabs-dpm.md).


## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto**
--- | ---
Abilitazione del backup quando si crea una macchina virtuale di Azure per Windows | Supportata per:  Windows Server 2016 (Datacenter/Datacenter Core); Windows Server 2012 R2 DataCenter; Windows Server 2008 R2 SP1
Abilitazione del backup quando si crea una macchina virtuale Linux | Supportata per:<br/><br/> - Server Ubuntu: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Backup di una macchina virtuale arrestata/offline/in fase di ricerca | Supportata.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Backup dei dischi dopo la migrazione in dischi gestiti. | Supportata.<br/><br/> Il backup continuerà a funzionare. Non è necessaria alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportata.<br/><br/> Il backup non può eliminare i punti di ripristino meno recenti e i backup inizieranno ad avere esito negativo quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportata.<br/><br/> Il backup della macchina virtuale verrà eseguito usando le impostazioni di pianificazione e conservazione definite nei nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se vengono ridotte, i punti di ripristino esistenti verranno rimossi nel successivo processo di pulizia e infine eliminati.
Annullamento di un processo di backup | Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali. 
Backup della macchina virtuale in un'area o una sottoscrizione diversa |  Non supportata.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/> È possibile eseguire fino a quattro backup su richiesta al giorno.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno. 
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale   | Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportata.<br/><br/> Backup di Azure non tiene conto della regolazione automatica dell'orologio per le modifiche correlate all'ora legale quando si esegue il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  La disabilitazione delle funzionalità non è supportata.



## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

La tabella riepiloga i sistemi operativi supportati quando si esegue il backup di macchine virtuali di Azure per Windows.

**Scenario** | **Supporto del sistema operativo** 
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | Client Windows: non supportato<br/><br/> Server Windows: Windows Server 2008 R2 o versioni successive 
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Backup tramite DPM o il server di Backup di Microsoft Azure | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto**
--- | --- 
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerente con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino è possibile creare una nuova macchina virtuale, ripristinare un disco e usarlo per creare una macchina virtuale oppure ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportata.<br/><br/> L'agente di Servizi di ripristino di Microsoft Azure può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportata.

## <a name="operating-system-support-linux"></a>Supporto dei sistemi operativi (Linux)

Per i backup Linux delle macchine virtuali di Azure, il servizio Backup di Azure supporta l'[elenco di distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Si noti che:

- Backup di Azure non supporta Core OS Linux.
- Backup di Azure non supporta i sistemi operativi a 32 bit.
- Altre distribuzioni Bring Your Own Linux potrebbero funzionare, a condizione che l'[agente di macchine virtuali di Azure per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sia disponibile nella macchina virtuale e che Python sia supportato.



## <a name="backup-frequency-and-retention"></a>Frequenza e conservazione dei backup

**Impostazione** | **Limiti** 
--- | --- 
Numero massimo di punti di ripristino per ogni istanza protetta (computer/carico di lavoro) | 9999
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite
Frequenza massima di backup nell'insieme di credenziali (estensione della macchina virtuale di Azure) | Una volta al giorno
Frequenza massima di backup nell'insieme di credenziali (agente di Servizi di ripristino di Microsoft Azure) | Tre backup al giorno.
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server<br/><br/> Una volta all'ora per altri carichi di lavoro.
Conservazione dei punti di ripristino | Giornaliera, settimanale, mensile, annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per i file server, 448 per i server applicazioni.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.


## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

**Metodo di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | È possibile creare una macchina virtuale durante il processo di ripristino. <br/><br/> Questa opzione consente di avere in esecuzione una macchina virtuale di base. È possibile specificare il nome, il gruppo di risorse, la rete virtuale, la subnet e l'archiviazione della macchina virtuale.  
**Ripristino di un disco** | È possibile ripristinare un disco e usarlo per creare una macchina virtuale<br/><br/> Quando si seleziona questa opzione, il servizio Backup di Azure copia i dati dall'insieme di credenziali in un account di archiviazione selezionato dall'utente. Il processo di ripristino genera un modello. È possibile scaricare tale modello e usarlo per specificare impostazioni personalizzate per le macchine virtuali, nonché creare una macchina virtuale.<br/><br/> Questa opzione consente di specificare un numero di impostazioni maggiore rispetto all'opzione precedente per la creazione di una macchina virtuale.<br/><br/>
**Sostituzione di un disco esistente** | È possibile ripristinare un disco e quindi usare il disco ripristinato per sostituire un disco attualmente presente in una macchina virtuale.
**Ripristino dei file** | È possibile ripristinare i file da un punto di ripristino selezionato. A tale scopo, è necessario scaricare uno script per montare il disco della macchina virtuale dal punto di ripristino. È quindi necessario esplorare i volumi del disco per trovare i file e le cartelle che si intende ripristinare e al termine smontare il disco.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Ripristino** | **Supportato**
--- | --- 
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere la [tabella dei sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file nelle macchine virtuali classiche | Non supportato. 
Ripristino dei file da macchine virtuali crittografate | Non supportato.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportato.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

La tabella riepiloga il supporto per il backup durante le attività di gestione delle macchine virtuali, ad esempio l'aggiunta o la sostituzione di dischi della macchina virtuale.

**Ripristino** | **Supportato** 
--- | --- 
Ripristino a livello di sottoscrizione/area/zona. | Non supportato 
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportato.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti | Non supportato.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti. 
Ripristino in un account con archiviazione con ridondanza della zona | Non supportato
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per i dischi gestiti, è possibile ripristinare il disco e usare l'opzione relativa al set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristino del backup di macchine virtuali non gestite dopo l'aggiornamento a macchine virtuali gestite | Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e si crea una macchina virtuale con PowerShell.
Ripristino di una macchina virtuale in una rete virtuale diversa |  Supportato.<br/><br/> La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area.




## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Calcolo** | **Supporto** 
--- | --- 
Dimensioni macchina virtuale |   Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
Backup di macchine virtuali in [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Supportato.<br/><br/> Non è possibile ripristinare una macchina virtuale in un set di disponibilità usando l'opzione per creare rapidamente una macchina virtuale. Al contrario, quando si ripristina la macchina virtuale, è necessario ripristinare il disco e usarlo per distribuire una macchina virtuale oppure ripristinare un disco e usarlo per sostituire un disco esistente. 
Backup di macchine virtuali in [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Non supportato.  
Backup di macchine virtuali distribuite con il [Vantaggio Hybrid Use (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Supportato.
Backup di macchine virtuali distribuite in un [set di scalabilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |   Non supportato   
Backup di macchine virtuali distribuite da [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (pubblicazione da parte di Microsoft, terze parti) |   Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Backup di macchine virtuali distribuite da un'immagine personalizzata (terze parti) |    Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Backup di macchine virtuali di cui è stata eseguita la migrazione in Azure   | Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione. 



## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Dischi dati delle macchine virtuali di Azure | Backup di una macchina virtuale con un massimo di 16 dischi dati.
Dimensioni del disco dati | Ogni singolo disco può avere dimensioni fino a 4095 GB.<br/><br/> Se si esegue la versione più recente del backup delle macchine virtuali di Azure (procedura nota come ripristino istantaneo), sono supportati dischi di dimensioni fino a 4 TB. [Altre informazioni](backup-instant-restore-capability.md)
Tipo di archiviazione | HDD Standard, SDD Standard, SDD Premium <br/><br/> SDD Standard è supportato se si esegue la versione più recente del backup delle macchine virtuali di Azure (procedura nota come ripristino istantaneo). [Altre informazioni](backup-instant-restore-capability.md)
Dischi gestiti | Supportato
Dischi crittografati | Supportato.<br/><br/> Le macchine virtuali di Azure abilitate con Crittografia dischi di Azure possono essere sottoposte a backup (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportato.<br/><br/> Se si esegue la versione più recente del backup delle macchine virtuali di Azure (procedura nota come [ripristino istantaneo](backup-instant-restore-capability.md)), è possibile escludere dal backup i dischi con l'acceleratore di scrittura abilitato.
Backup di dischi deduplicati | Non supportato.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.

## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali


**Componente** | **Supporto**
--- | ---
Numero di schede di interfaccia di rete | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. La rimozione di schede di interfaccia di rete dopo l'abilitazione non incide sul conteggio.
Bilanciamento del carico interno/esterno |   Supportato. <br/></br> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |  Supportato. <br/></br> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete  | Supportato. <br/></br> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici    | Supportato.<br/><br/> È necessario associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete oppure creare un indirizzo e associarlo a tale scheda dopo l'esecuzione del ripristino.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |   Supportato.
Indirizzo IP riservato (statico) | Non supportato.<br/><br/> Non è possibile eseguire il backup di una macchina virtuale con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |    Supportato.<br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine usa indirizzi IP dinamici, per impostazione predefinita farà altrettanto anche la scheda di interfaccia di rete nella macchina virtuale ripristinata.
Gestione traffico | Supportato<br/><br/>. Se la macchina virtuale sottoposta a backup è in Gestione traffico, sarà necessario aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di Gestione traffico. 
DNS di Azure | Supportato.
DNS personalizzato |    Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato. 
Endpoint del servizio di rete virtuale  | Supportato.<br/><br/> Le impostazioni degli account di archiviazione di firewall e rete virtuale devono consentire l'accesso da tutte le reti. 



## <a name="vm-securityencryption-support"></a>Supporto della sicurezza/crittografia delle macchine virtuali

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



## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Si noti che:

- Per le VM di Azure, l'estensione macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, quindi non è necessario comprimere il traffico.
- Se si usa DPM o MABS, è possibile comprimere i dati prima di eseguirne il backup in questi server per risparmiare larghezza di banda. 

**Computer** | **Compressione in MABS/DPM (TCP)** | **Compressione (HTTPS) in insieme di credenziali**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ND | Sì
Macchine virtuali di Azure | ND | ND
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]




## <a name="next-steps"></a>Passaggi successivi

- [Eseguire un backup delle VM di Azure](backup-azure-arm-vms-prepare.md)
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

