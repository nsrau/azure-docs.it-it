---
title: Matrice di supporto del servizio Backup di Azure per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: raynew
ms.openlocfilehash: 2267a4e836fe1aff214f40e34afa830de50fa2d5
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471640"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure
È possibile usare la [servizio Backup di Azure](backup-overview.md) per eseguire il backup di macchine virtuali locali e i carichi di lavoro e macchine virtuali di Azure (VM). Questo articolo riepiloga le impostazioni del supporto e le limitazioni quando si esegue il backup di macchine virtuali di Azure con Backup di Azure.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per Backup di Azure
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per server di Backup di Azure/System Center Data Protection Manager (DPM) eseguire il backup
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.

**Scenario** | **Backup** | **Agent** |**Restore**
--- | --- | --- | ---
backup diretto di macchine virtuali di Azure  | Eseguire il backup dell'intera macchina virtuale.  | Non è necessario alcun agente nella macchina virtuale di Azure. Backup di Azure installa e usa un'estensione per il [agente di macchine Virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) che è in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino nel modo seguente:<br/><br/> - **Creare una macchina virtuale di base**. Ciò è utile se la macchina virtuale non dispone di alcuna configurazione speciale, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco Quindi collegarlo a una VM esistente o creare una nuova macchina virtuale dal disco tramite PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare file e cartelle da una macchina virtuale anziché da dell'intera macchina virtuale.
Eseguire direttamente backup di macchine virtuali di Azure (solo Windows)  | Eseguire il backup di file/cartelle/volumi specifico. | Installare il [agente di servizi di ripristino di Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
Eseguire il backup di macchine Virtuali di Azure al server di backup  | Eseguire il backup di file/cartelle/volumi. stato/bare metal file di sistema; dati delle App a System Center DPM o al Server di Backup di Microsoft Azure (MABS).<br/><br/> DPM/MABS quindi eseguito il backup nell'archivio di backup. | Installare l'agente protezione DPM/MABS nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app.

Altre informazioni sul backup [usando un server di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e circa [requisiti di supporto](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto**
--- | ---
Abilitazione del backup quando si crea una macchina virtuale di Azure per Windows | Supportata per:  Windows Server 2019 (Datacenter/Datacenter Core), Windows Server 2016 (Core Data Center o di Data Center); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (versione RTM e SP1)
Abilitazione del backup quando si crea una macchina virtuale Linux | Supportata per:<br/><br/> - Server Ubuntu: 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Eseguire il backup di una macchina virtuale non in linea/arresto della macchina virtuale | Supportato.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Eseguire il backup di dischi, dopo la migrazione a managed disks | Supportato.<br/><br/> Il backup continuerà a funzionare. Non è necessaria alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportati.<br/><br/> Backup di Azure non è possibile eliminare i punti di risorse precedenti e i backup inizieranno a non riuscire quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportato.<br/><br/> La macchina virtuale verrà sottoposti a usando le impostazioni di pianificazione e la conservazione in nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se vengono ridotte, punti di ripristino esistenti verranno eliminati nel processo di pulizia successivo e infine eliminati.
Annullamento di un processo di backup | Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali.
Backup della macchina virtuale in un'area o una sottoscrizione diversa |  Non supportati.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/> È possibile apportare fino a quattro backup su richiesta al giorno.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno.
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale   | Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportati.<br/><br/> Backup di Azure non viene regolata automaticamente per l'ora legale durante il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  La disabilitazione della funzionalità di sicurezza non è supportata.

## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

La tabella seguente riepiloga i sistemi operativi supportati per eseguire il backup di VM Windows di Azure.

**Scenario** | **Supporto del sistema operativo**
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | Client Windows: Non supportate<br/><br/> Windows Server 2019 (Datacenter/Datacenter Core), Windows Server 2016 (Core Data Center o di Data Center); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (versione RTM e SP1)
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Eseguire il backup con DPM/MABS | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto**
--- | ---
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino, è possibile creare una nuova VM, ripristinare un disco e usarlo per creare una macchina virtuale o ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportati.<br/><br/> L'agente MARS può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportati.

## <a name="operating-system-support-linux"></a>Supporto dei sistemi operativi (Linux)

Per i backup Linux delle macchine virtuali di Azure, il servizio Backup di Azure supporta l'[elenco di distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenere presente quanto segue:

- Backup di Azure non supporta Core OS Linux.
- Backup di Azure non supporta i sistemi operativi a 32 bit.
- Altre distribuzioni di Linux bring-your-own potrebbero funzionare, purché la [agente di macchine Virtuali di Azure per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) è disponibile nella macchina virtuale e a condizione che supportano Python.
- Backup di Azure non supporta una VM Linux proxy configurato, se non dispone di Python versione 2.7 installata.


## <a name="backup-frequency-and-retention"></a>Frequenza e conservazione dei backup

**Impostazione** | **Limiti**
--- | ---
Punti di ripristino massimo per ogni istanza protetta (machine/carico di lavoro) | 9999.
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite.
Frequenza massima di backup nell'insieme di credenziali (estensione della macchina virtuale di Azure) | Una volta al giorno.
Frequenza massima di backup nell'insieme di credenziali (agente di Servizi di ripristino di Microsoft Azure) | Tre backup al giorno.
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server.<br/><br/> Una volta all'ora per altri carichi di lavoro.
Conservazione del punto di ripristino | Giornaliera, settimanale, mensile e annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per file server e 448 per i server applicazioni.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

**Metodo di ripristino** | **Dettagli**
--- | ---
Creare una nuova VM | È possibile creare una macchina virtuale durante il processo di ripristino. <br/><br/> Questa opzione consente di avere in esecuzione una macchina virtuale di base. È possibile specificare il nome, il gruppo di risorse, la rete virtuale, la subnet e l'archiviazione della macchina virtuale.  
Ripristinare un disco | È possibile ripristinare un disco e usarlo per creare una macchina virtuale.<br/><br/> Quando si seleziona questa opzione, il servizio Backup di Azure copia i dati dall'insieme di credenziali in un account di archiviazione selezionato dall'utente. Il processo di ripristino genera un modello. È possibile scaricare questo modello, utilizzata per specificare le impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> Questa opzione consente di specificare un numero di impostazioni maggiore rispetto all'opzione precedente per la creazione di una macchina virtuale.<br/><br/>
Sostituire un disco esistente | È possibile ripristinare un disco e quindi usare il disco ripristinato per sostituire un disco attualmente presente in una macchina virtuale.
Ripristinare i file | È possibile ripristinare i file da un punto di ripristino selezionato. A tale scopo, è necessario scaricare uno script per montare il disco della macchina virtuale dal punto di ripristino. Quindi esplorare i volumi di disco per trovare il file e cartelle che si vuole ripristinare e smontare il disco al termine.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Restore** | **Supportato**
--- | ---
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere le [tabella di sistema operativo compatibile](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file nelle macchine virtuali classiche | Non supportati.
Ripristino dei file da macchine virtuali crittografate | Non supportati.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportati.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

Nella tabella seguente riepiloga il supporto per il backup durante le attività di gestione della macchina virtuale, ad esempio aggiunta o sostituzione dei dischi di macchina virtuale.

**Restore** | **Supportato**
--- | ---
Ripristino a livello di sottoscrizione/area/zona. | Non supportati.
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportati.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti | Non supportati.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti.
Ripristinare in account di archiviazione con archiviazione con ridondanza della zona (ZRS) | Supportata (per la macchina virtuale che vengono sottoposti a backup dopo gennaio 2019 e in cui [zona di disponibilità](https://azure.microsoft.com/global-infrastructure/availability-zones/) disponibili)
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per i dischi gestiti, è possibile ripristinare il disco e usare l'opzione set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristinare il backup delle VM non gestita dopo che l'aggiornamento a gestito della macchina virtuale| Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e crea una macchina virtuale usando PowerShell.
Ripristinare una macchina virtuale nella rete virtuale diversa |   Supportato.<br/><br/> La rete virtuale deve essere nella stessa sottoscrizione e area.

## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Calcolo** | **Supporto**
--- | ---
Dimensioni macchina virtuale |   Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Altre informazioni.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup di macchine virtuali in [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Supportato.<br/><br/> È possibile ripristinare una macchina virtuale in un set di disponibilità usando l'opzione per creare rapidamente una macchina virtuale. Al contrario, quando si ripristina la macchina virtuale, ripristinare il disco e usarlo per distribuire una macchina virtuale o ripristinare un disco e usarlo per sostituire un disco esistente.
Backup di macchine virtuali in [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Non supportati.
Eseguire il backup di macchine virtuali distribuite con [vantaggio Hybrid Use (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Supportato.
Eseguire il backup di macchine virtuali distribuite in un [set di scalabilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |  Non supportati.
Eseguire il backup di macchine virtuali distribuite dal [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Pubblicata da Microsoft, terze parti) |  Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Eseguire il backup di macchine virtuali distribuite da un'immagine personalizzata (di terze parti) |   Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Eseguire il backup di macchine virtuali che vengono migrate in Azure  | Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione.
Eseguire il backup della coerenza di più macchine Virtuali | Backup di Azure non offre la coerenza dei dati e dell'applicazione tra più macchine virtuali.
Backup con [le impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Non è supportato. <br/><br/> Se il ripristino della macchina virtuale di Azure con le impostazioni di diagnostica viene attivato utilizzando [Crea nuovo](backup-azure-arm-restore-vms.md#create-a-vm) opzione avrà esito negativo, il ripristino.


## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Dischi di dati delle VM di Azure | Backup di una macchina virtuale con un massimo di 16 dischi dati. <br/><br/> Supporto di dischi con dimensioni fino a 4 TB.
Dimensioni del disco dati | Ogni singolo disco può avere dimensioni fino a 4095 GB.<br/><br/> Se gli insiemi di credenziali sono in esecuzione la versione più recente di Backup di Azure (noto come ripristino istantaneo), dischi di dimensioni fino a 4 TB sono supportati. [Altre informazioni](backup-instant-restore-capability.md)  
Tipo di archiviazione | Premium a standard HDD, SSD standard, SSD. <br/><br/> SSD standard è supportata se gli insiemi di credenziali vengono aggiornati alla versione più recente di backup di macchine Virtuali di Azure (noto come ripristino immediato). [Altre informazioni](backup-instant-restore-capability.md)
Dischi gestiti | Supportato.
Dischi crittografati | Supportato.<br/><br/> Macchine virtuali di Azure abilitate con crittografia dischi di Azure possono eseguire il backup (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportati.<br/><br/> Se si esegue la versione più recente del backup delle macchine virtuali di Azure (procedura nota come [ripristino istantaneo](backup-instant-restore-capability.md)), è possibile escludere dal backup i dischi con l'acceleratore di scrittura abilitato.
Backup di dischi deduplicati | Non supportati.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.
Archiviazione condivisa| Backup di macchine virtuali tramite volumi condivisi Cluster (CSV) o Scale-Out File Server non è consigliato. Writer CSV si verifichino errori durante il backup. Durante il ripristino, i dischi contenenti i volumi condivisi cluster potrebbero non tornare a questa pagina verticale.

> [!NOTE]
> Il ridimensionamento del disco non è consigliato da Backup di Azure.


## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Numero di interfacce di rete (NIC) | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. Rimozione di schede di rete dopo aver abilitato la protezione non influenza il conteggio.
Bilanciamento del carico interno/esterno |   Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |    Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete  | Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici    | Supportato.<br/><br/> Associare l'interfaccia di rete, un indirizzo IP pubblico esistente o creare un indirizzo e associarlo con l'interfaccia di rete dopo il ripristino viene eseguito.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |   Supportato.
Indirizzo IP riservato (statico) | Non supportati.<br/><br/> È possibile eseguire il backup di una VM con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |    Supportato.<br/><br/> Se l'interfaccia di rete nell'origine VM Usa indirizzi IP dinamici, per impostazione predefinita l'interfaccia di rete nella VM ripristinata utilizzerà troppo.
Gestione traffico di Azure   | Supportato.<br/><br/>Se la macchina virtuale sottoposta a backup sia in Traffic Manager, aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di gestione traffico.
DNS di Azure | Supportato.
DNS personalizzato |    Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato.
Endpoint servizio di rete virtuale   | Supportato.<br/><br/> Firewall e impostazioni di account di archiviazione di rete virtuale devono consentire l'accesso da tutte le reti.



## <a name="vm-security-and-encryption-support"></a>Supporto di sicurezza e la crittografia della macchina virtuale

Backup di Azure supporta la crittografia per i dati in movimento e inattivi:

Traffico di rete verso Azure:

- Il traffico di backup dai server all'insieme di credenziali di servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

  > [!WARNING]
  > Dopo aver configurato l'insieme di credenziali, sufficiente l'accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Sicurezza dei dati:

- Per eseguire il backup di macchine virtuali di Azure, è necessario impostare la crittografia *all'interno di* la macchina virtuale.
- Backup di Azure supporta la crittografia dischi di Azure, che utilizza BitLocker nelle macchine virtuali di Windows e Stati Uniti **dm-crypt** nelle macchine virtuali Linux.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.


**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Sì][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]



## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Backup supporta la compressione dei backup del traffico, come riepilogato nella tabella seguente. Tenere presente quanto segue:

- Per le VM di Azure, l'estensione macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione. Non è necessario comprimere il traffico.
- Se si usa DPM e MABS, è possibile risparmiare larghezza di banda con la compressione dei dati prima di cui viene eseguito il backup per DPM/MABS.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimere all'insieme di credenziali (HTTPS)**
--- | --- | ---
Computer Windows locali senza DPM/MABS | NA | ![Sì][green]
Macchine virtuali di Azure | NA | NA
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
