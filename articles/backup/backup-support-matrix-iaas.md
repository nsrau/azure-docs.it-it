---
title: Matrice di supporto del servizio Backup di Azure per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: 3823bca0601f825323a44773f8c70be371ec8781
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311655"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure
È possibile usare il [servizio backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali e di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si esegue il backup di macchine virtuali di Azure con backup di Azure.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per backup di Azure
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il server di backup di Azure/System Center Data Protection Manager (DPM) backup
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di servizi di ripristino di Microsoft Azure (Mars)

## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.

**Scenario** | **Backup** | **Agent** |**Restore**
--- | --- | --- | ---
backup diretto di macchine virtuali di Azure  | Eseguire il backup dell'intera VM.  | Non è necessario alcun agente nella macchina virtuale di Azure. Backup di Azure installa e usa un'estensione per l' [agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino nel modo seguente:<br/><br/> - **Creare una macchina virtuale di base**. Questa operazione è utile se la macchina virtuale non ha una configurazione speciale, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco Quindi collegarlo a una macchina virtuale esistente o creare una nuova macchina virtuale dal disco usando PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare file e cartelle da una macchina virtuale anziché dall'intera VM.
Backup diretto di macchine virtuali di Azure (solo Windows)  | Eseguire il backup di file/cartelle/volumi specifici. | Installare l' [agente di servizi di ripristino di Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
Eseguire il backup della macchina virtuale di Azure nel server di backup  | Eseguire il backup di file/cartelle/volumi; file bare metal e dello stato del sistema; dati delle app in System Center DPM o in Backup di Microsoft Azure Server (MAB).<br/><br/> DPM/MAB esegue quindi il backup nell'insieme di credenziali per il backup. | Installare l'agente protezione DPM/MAB nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app.

Altre informazioni sul backup [con un server di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sui [requisiti di supporto](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto**
--- | ---
Abilitazione del backup quando si crea una macchina virtuale di Azure per Windows | Supportata per: <br/><br/> -Windows Server 2019 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2016 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Abilitazione del backup quando si crea una macchina virtuale Linux | Supportata per:<br/><br/> - Server Ubuntu: 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Eseguire il backup di una macchina virtuale arrestata/offline | Supportato.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Eseguire il backup dei dischi dopo la migrazione a Managed Disks | Supportato.<br/><br/> Il backup continuerà a funzionare. non è necessaria alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportati.<br/><br/> Backup di Azure non è in grado di eliminare i punti di ripristino precedenti e i backup inizieranno ad avere esito negativo quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportato.<br/><br/> Verrà eseguito il backup della macchina virtuale utilizzando le impostazioni pianificazione e conservazione in nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se sono ridotti, i punti di ripristino esistenti verranno eliminati nel processo di pulizia successivo e infine eliminati.
Annullamento di un processo di backup | Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali.
Backup della macchina virtuale in un'area o una sottoscrizione diversa |  Non supportati.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/> Puoi creare fino a quattro backup su richiesta al giorno.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno.
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale   | Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportati.<br/><br/> Backup di Azure non viene regolato automaticamente per le modifiche all'ora legale quando si esegue il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  La disabilitazione delle funzionalità di sicurezza non è supportata.
Eseguire il backup della VM di cui è stata modificata l'ora del computer | Non supportati.<br/><br/> Se l'ora del computer viene modificata in una data e ora successiva dopo l'abilitazione del backup per la VM. Tuttavia, anche se la modifica dell'ora viene ripristinata, il backup non è garantito.  


## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

Nella tabella seguente sono riepilogati i sistemi operativi supportati per il backup di macchine virtuali di Windows Azure.

**Scenario** | **Supporto del sistema operativo**
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | Client Windows: Non supportate<br/><br/>-Windows Server 2019 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2016 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Backup con DPM/MAB | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto**
--- | ---
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino, è possibile creare una nuova macchina virtuale, ripristinare un disco e usarla per creare una VM oppure ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportati.<br/><br/> L'agente MARS può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportati.

## <a name="operating-system-support-linux"></a>Supporto dei sistemi operativi (Linux)

Per i backup Linux delle macchine virtuali di Azure, il servizio Backup di Azure supporta l'[elenco di distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenere presente quanto segue:

- Backup di Azure non supporta Core OS Linux.
- Backup di Azure non supporta i sistemi operativi a 32 bit.
- Altre distribuzioni di Linux Bring your own possono funzionare purché l' [agente di macchine virtuali di Azure per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sia disponibile nella macchina virtuale e, a condizione che Python sia supportato.
- Backup di Azure non supporta una VM Linux configurata tramite proxy se non è installata la versione 2,7 di Python.


## <a name="backup-frequency-and-retention"></a>Frequenza e conservazione dei backup

**Impostazione** | **Limiti**
--- | ---
Punti di ripristino massimi per istanza protetta (computer/carico di lavoro) | 9999.
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite.
Frequenza massima di backup nell'insieme di credenziali (estensione della macchina virtuale di Azure) | Una volta al giorno.
Frequenza massima di backup nell'insieme di credenziali (agente di Servizi di ripristino di Microsoft Azure) | Tre backup al giorno.
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server.<br/><br/> Una volta all'ora per altri carichi di lavoro.
Conservazione dei punti di ripristino | Giornaliera, settimanale, mensile e annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per i file server e 448 per i server app.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

**Metodo di ripristino** | **Dettagli**
--- | ---
Creare una nuova VM | È possibile creare una macchina virtuale durante il processo di ripristino. <br/><br/> Questa opzione consente di avere in esecuzione una macchina virtuale di base. È possibile specificare il nome, il gruppo di risorse, la rete virtuale, la subnet e l'archiviazione della macchina virtuale.  
Ripristinare un disco | È possibile ripristinare un disco e usarlo per creare una macchina virtuale.<br/><br/> Quando si seleziona questa opzione, il servizio Backup di Azure copia i dati dall'insieme di credenziali in un account di archiviazione selezionato dall'utente. Il processo di ripristino genera un modello. È possibile scaricare questo modello, usarlo per specificare le impostazioni della macchina virtuale personalizzata e creare una VM.<br/><br/> Questa opzione consente di specificare un numero di impostazioni maggiore rispetto all'opzione precedente per la creazione di una macchina virtuale.<br/><br/>
Sostituire un disco esistente | È possibile ripristinare un disco e quindi usare il disco ripristinato per sostituire un disco attualmente presente in una macchina virtuale.
Ripristinare i file | È possibile ripristinare i file da un punto di ripristino selezionato. A tale scopo, è necessario scaricare uno script per montare il disco della macchina virtuale dal punto di ripristino. È quindi possibile esplorare i volumi del disco per trovare i file e le cartelle che si desidera ripristinare e smontare il disco al termine dell'operazione.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Restore** | **Supportato**
--- | ---
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere la [tabella del sistema operativo compatibile](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file nelle macchine virtuali classiche | Non supportati.
Ripristino dei file da macchine virtuali crittografate | Non supportati.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportati.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

La tabella seguente riepiloga il supporto per il backup durante le attività di gestione delle macchine virtuali, ad esempio l'aggiunta o la sostituzione dei dischi delle macchine virtuali.

**Restore** | **Supportato**
--- | ---
Ripristino a livello di sottoscrizione/area/zona. | Non supportati.
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportati.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti | Non supportati.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti.
Ripristinare l'account di archiviazione usando l'archiviazione con ridondanza della zona (ZRS) | Supportato (per la macchina virtuale di cui viene eseguito il backup dopo Jan 2019 e dove sono disponibili [zone di disponibilità](https://azure.microsoft.com/global-infrastructure/availability-zones/) )
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per Managed disks, è possibile ripristinare il disco e usare l'opzione set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristinare il backup di macchine virtuali non gestite dopo l'aggiornamento alla macchina virtuale gestita| Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e si crea una VM tramite PowerShell.
Ripristinare una macchina virtuale in una rete virtuale diversa |   Supportato.<br/><br/> La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area.

## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Calcolo** | **Supporto**
--- | ---
Dimensioni macchina virtuale |   Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Altre informazioni.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup di macchine virtuali in [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Supportato.<br/><br/> Non è possibile ripristinare una macchina virtuale in un set disponibile usando l'opzione per creare rapidamente una macchina virtuale. Al contrario, quando si ripristina la macchina virtuale, ripristinare il disco e usarlo per distribuire una VM oppure ripristinare un disco e usarlo per sostituire un disco esistente.
Backup di macchine virtuali in [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Non supportati.
Eseguire il backup di macchine virtuali distribuite con il [vantaggio Hybrid use (hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Supportato.
Eseguire il backup di macchine virtuali distribuite in un [set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) di scalabilità |  Non supportati.
Eseguire il backup di macchine virtuali distribuite da [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Pubblicato da Microsoft, terze parti) |  Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo). Non vengono ripristinate le macchine virtuali di Azure Marketplace supportate come macchine virtuali, in quanto queste necessitano di informazioni di acquisto ma solo come dischi.
Eseguire il backup di macchine virtuali distribuite da un'immagine personalizzata (di terze parti) |   Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Eseguire il backup di macchine virtuali di cui viene eseguita la migrazione in Azure  | Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione.
Backup della coerenza tra più macchine virtuali | Backup di Azure non garantisce la coerenza dei dati e delle applicazioni tra più macchine virtuali.
Backup con [impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Non supportato. <br/><br/> Se il ripristino della macchina virtuale di Azure con le impostazioni di diagnostica viene attivato usando [Crea nuova](backup-azure-arm-restore-vms.md#create-a-vm) opzione, il ripristino ha esito negativo.


## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Dischi di dati delle VM di Azure | Backup di una macchina virtuale con un massimo di 16 dischi dati. <br/><br/> Supporto di dischi con dimensioni fino a 4 TB.
Dimensioni del disco dati | Ogni singolo disco può avere dimensioni fino a 4095 GB.<br/><br/> Se gli insiemi di credenziali eseguono la versione più recente di backup di Azure (noto come ripristino istantaneo), sono supportate dimensioni del disco fino a 4 TB. [Altre informazioni](backup-instant-restore-capability.md)  
Tipo di archiviazione | HDD Standard, unità SSD standard, unità SSD Premium. <br/><br/> SDD Standard è supportato se gli insiemi di credenziali vengono aggiornati alla versione più recente del backup delle macchine virtuali di Azure (noto come ripristino immediato). [Altre informazioni](backup-instant-restore-capability.md)
Dischi gestiti | Supportato.
Dischi crittografati | Supportato.<br/><br/> È possibile eseguire il backup delle macchine virtuali di Azure abilitate con crittografia dischi di Azure (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportati.<br/><br/> Backup di Azure esclude automaticamente i dischi con acceleratore di scrittura abilitati durante il backup. Poiché non viene eseguito il backup, non sarà possibile ripristinare questi dischi dal punto di ripristino della macchina virtuale.
Backup di dischi deduplicati | Non supportati.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.
Archiviazione condivisa| Non è consigliabile eseguire il backup di macchine virtuali con Volume condiviso cluster (CSV) o file server di scalabilità orizzontale. Probabilmente i writer CSV avranno esito negativo durante il backup. Durante il ripristino, i dischi contenenti volumi CSV potrebbero non essere disponibili.



## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Numero di interfacce di rete (NIC) | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. La rimozione delle schede di rete dopo l'abilitazione della protezione non influisce sul conteggio.
Bilanciamento del carico interno/esterno |   Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |    Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete  | Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici    | Supportato.<br/><br/> Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete oppure creare un indirizzo e associarlo alla scheda di interfaccia di rete dopo aver eseguito il ripristino.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |   Supportato.
Indirizzo IP riservato (statico) | Non supportati.<br/><br/> Non è possibile eseguire il backup di una macchina virtuale con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |    Supportato.<br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine USA indirizzi IP dinamici, per impostazione predefinita la scheda di interfaccia di rete nella macchina virtuale ripristinata lo userà anche.
Gestione traffico di Azure   | Supportato.<br/><br/>Se la macchina virtuale di cui è stato eseguito il backup si trova in gestione traffico, aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di gestione traffico.
DNS di Azure | Supportato.
DNS personalizzato |    Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato.
Endpoint servizio di rete virtuale   | Supportato.<br/><br/> Le impostazioni dell'account di archiviazione di rete virtuale e del firewall devono consentire l'accesso da tutte le reti.



## <a name="vm-security-and-encryption-support"></a>Supporto della crittografia e della sicurezza delle VM

Backup di Azure supporta la crittografia per i dati in movimento e inattivi:

Traffico di rete verso Azure:

- Il traffico di backup dai server all'insieme di credenziali dei servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

  > [!WARNING]
  > Dopo aver configurato l'insieme di credenziali, è possibile accedere solo alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Sicurezza dei dati:

- Quando si esegue il backup di macchine virtuali di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta crittografia dischi di Azure, che usa BitLocker in macchine virtuali Windows e US **dm-crypt** in macchine virtuali Linux.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.


**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Yes][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Yes][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Yes][green]



## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Tenere presente quanto segue:

- Per le macchine virtuali di Azure, l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure tramite la rete di archiviazione. Non è necessario comprimere il traffico.
- Se si usa DPM o MAB, è possibile risparmiare larghezza di banda comprimendo i dati prima di eseguirne il backup in DPM/MAB.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimi nell'insieme di credenziali (HTTPS)**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ND | ![Sì][green]
Macchine virtuali di Azure | ND | ND
Computer locali/VM di Azure con DPM | ![Yes][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
