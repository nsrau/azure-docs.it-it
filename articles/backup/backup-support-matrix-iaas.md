---
title: Matrice di supporto per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 66c712eca86b01c5815638c9ea58042580791ea6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685477"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni e le limitazioni di supporto quando si esegue il backup di macchine virtuali di Azure con Backup di Azure.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per Backup di Azure
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il server di backup di Azure/System Center Data Protection Manager (DPM) backup
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di Servizi di ripristino di Microsoft Azure

## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.

**Scenario** | **Backup** | **Agent** |**Restore**
--- | --- | --- | ---
backup diretto di macchine virtuali di Azure  | Backup dell'intera macchina virtuale.  | Non è necessario alcun agente aggiuntivo nella macchina virtuale di Azure. Backup di Azure installa e usa un'estensione dell'[agente di macchine virtuali di Azure](../virtual-machines/extensions/agent-windows.md) in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino nel modo seguente:<br/><br/> - **Creare una macchina virtuale di base**. Questa operazione è utile se la macchina virtuale non ha configurazioni speciali, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco e quindi collegarlo a una macchina virtuale esistente oppure creare una nuova macchina virtuale dal disco con PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare cartelle e file specifici di una macchina virtuale, anziché dell'intera macchina virtuale.
Backup diretto di macchine virtuali di Azure (solo Windows)  | Backup di file, cartelle e volumi specifici. | Installare l'[agente di Servizi di ripristino di Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
Backup di una macchina virtuale di Azure nel server di backup  | Backup di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app in System Center DPM o nel server di Backup di Microsoft Azure.<br/><br/> DPM o il server di Backup di Microsoft Azure esegue quindi il backup nell'insieme di credenziali di backup. | Installare l'agente protezione DPM o del server di Backup di Microsoft Azure nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app.

Altre informazioni sul backup [con un server di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sui [requisiti di supporto](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto**
--- | ---
Backup di una macchina virtuale arrestata/offline | Supportato.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Backup dei dischi dopo la migrazione in dischi gestiti | Supportato.<br/><br/> Il backup continuerà a funzionare. Non è richiesta alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportato.<br/><br/> Backup di Azure non può eliminare i punti di ripristino meno recenti e i backup iniziano ad avere esito negativo quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportato.<br/><br/> Il backup della macchina virtuale verrà eseguito usando le impostazioni di pianificazione e conservazione definite nei nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se vengono ridotte, i punti di ripristino esistenti verranno rimossi nel successivo processo di pulizia e infine eliminati.
Annullamento di un processo di backup| Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali.
Backup della macchina virtuale in un'area o una sottoscrizione diversa |Non supportato.<br><br>Per eseguire correttamente il backup, le macchine virtuali devono trovarsi nella stessa sottoscrizione dell'insieme di credenziali per il backup.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/>Il servizio Backup di Azure supporta fino a nove backup su richiesta al giorno, ma per avere prestazioni ottimali Microsoft consiglia non più di quattro backup su richiesta giornalieri.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno.
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale| Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportato.<br/><br/> Backup di Azure non si adatta automaticamente al passaggio all'ora legale per il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](./backup-azure-security-feature.md) |La disabilitazione delle funzionalità di sicurezza non è supportata.
Eseguire il backup della macchina virtuale la cui ora del computer è cambiata | Non supportato.<br/><br/> Se l'ora del computer viene modificata in una data e ora successiva dopo l'abilitazione del backup per la VM. Tuttavia, anche se la modifica dell'ora viene ripristinata, il backup non è garantito.
VM di Azure nei [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md) | Il backup e il ripristino sono supportati per le macchine virtuali con [modalità di orchestrazione](../virtual-machine-scale-sets/orchestration-modes.md#orchestration-modes) impostata su 3. <br><br>I set di disponibilità non sono supportati.

## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

La tabella seguente riepiloga i sistemi operativi supportati quando si esegue il backup di macchine virtuali di Azure per Windows.

**Scenario** | **Supporto del sistema operativo**
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | - Client Windows 10 (solo 64 bit) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2012 (Datacenter/standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> - Windows Server 2008 (solo 64 bit)
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati](backup-support-matrix-mars-agent.md#supported-operating-systems).
Backup con DPM o con il server di Backup di Microsoft Azure | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](/system-center/dpm/dpm-protection-matrix).

Backup di Azure non supporta i sistemi operativi a 32 bit.

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto**
--- | ---
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino è possibile creare una nuova macchina virtuale, ripristinare un disco e usarlo per creare una macchina virtuale oppure ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportato.<br/><br/> L'agente MARS può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportato.

## <a name="operating-system-support-linux"></a>Supporto dei sistemi operativi (Linux)

Per i backup Linux delle macchine virtuali di Azure, il servizio Backup di Azure supporta l'[elenco di distribuzioni di Linux approvate in Azure](../virtual-machines/linux/endorsed-distros.md). Tenere presente quanto segue:

- Backup di Azure non supporta Core OS Linux.
- Backup di Azure non supporta i sistemi operativi a 32 bit.
- Altre distribuzioni Bring Your Own Linux potrebbero funzionare, a condizione che l'[agente di macchine virtuali di Azure per Linux](../virtual-machines/extensions/agent-linux.md) sia disponibile nella macchina virtuale e che Python sia supportato.
- Backup di Azure non supporta una VM Linux configurata tramite proxy se non è installata la versione 2,7 di Python.

## <a name="backup-frequency-and-retention"></a>Frequenza e conservazione dei backup

**Impostazione** | **Limiti**
--- | ---
Numero massimo di punti di ripristino per ogni istanza protetta (computer/carico di lavoro) | 9999.
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite.
Frequenza massima di backup nell'insieme di credenziali (estensione della macchina virtuale di Azure) | Una volta al giorno.
Frequenza massima di backup nell'insieme di credenziali (agente di Servizi di ripristino di Microsoft Azure) | Tre backup al giorno.
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server.<br/><br/> Una volta all'ora per altri carichi di lavoro.
Conservazione dei punti di ripristino | Giornaliera, settimanale, mensile e annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per i file server, 448 per i server applicazioni.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la macchina virtuale, selezionare il gruppo di risorse e la rete virtuale in cui inserirla, nonché specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova macchina virtuale deve essere creata nella stessa area della macchina virtuale di origine.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato dall'utente.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, non è possibile usare questa opzione.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente prima della sostituzione del disco e l'archivia nella posizione di gestione temporanea specificata dall'utente. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e conservato in conformità con i criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene conservato nel gruppo di risorse. Se non sono necessari, è possibile scegliere di eliminare manualmente i dischi originali. <br/><br/>L'opzione Sostituisci esistente è supportata per le macchine virtuali gestite non crittografate. Non è supportata per i dischi non gestiti, le [macchine virtuali generalizzate](../virtual-machines/windows/capture-image-resource.md) o le macchine virtuali [create usando immagini personalizzate](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> Il parametro replace existing è supportato anche per le macchine virtuali con risorse collegate, ad esempio l'identità gestita e la [Key Vault](../key-vault/general/overview.md) [gestite dall'utente](../active-directory/managed-identities-azure-resources/overview.md) .
**Tra aree (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare macchine virtuali di Azure nell'area secondaria, che è un'[area associata di Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> È possibile ripristinare tutte le macchine virtuali di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Questa funzionalità è disponibile per le opzioni seguenti:<br> <li> [Creare una macchina virtuale](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Ripristino di dischi](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> L'opzione di [sostituzione di dischi esistenti](./backup-azure-arm-restore-vms.md#replace-existing-disks) non è attualmente supportata.<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita da amministratori del backup e amministratori di app.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Restore** | **Supportato**
--- | ---
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere la [tabella dei sistemi operativi compatibili](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file da macchine virtuali crittografate | Non supportato.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportato.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

La tabella seguente riepiloga il supporto per il backup durante le attività di gestione delle macchine virtuali, ad esempio l'aggiunta o la sostituzione di dischi della macchina virtuale.

**Restore** | **Supportato**
--- | ---
Ripristino a livello di sottoscrizione/area/zona. | Non supportato.
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportato.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti |Non supportato.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti.
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per i dischi gestiti, è possibile ripristinare il disco e usare l'opzione relativa al set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristino del backup di macchine virtuali non gestite dopo l'aggiornamento a macchine virtuali gestite| Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e si crea una macchina virtuale con PowerShell.
Ripristino di una macchina virtuale in una rete virtuale diversa |Supportato.<br/><br/> La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area.

## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Calcolo** | **Supporto**
--- | ---
Dimensioni macchina virtuale |Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Altre informazioni.](../virtual-machines/sizes.md)
Backup di macchine virtuali in [set di disponibilità](../virtual-machines/availability.md#availability-sets) | Supportato.<br/><br/> Non è possibile ripristinare una macchina virtuale in un set di disponibilità usando l'opzione di creazione rapida di una macchina virtuale. Quando si ripristina la macchina virtuale, ripristinare il disco e usarlo per distribuire una macchina virtuale oppure ripristinare un disco e usarlo per sostituire un disco esistente.
Backup di macchine virtuali distribuite con il [vantaggio Hybrid Use (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Supportato.
Backup di macchine virtuali distribuite in un [set di scalabilità](../virtual-machine-scale-sets/overview.md) |Supportata. Per il dominio di errore è necessario impostare la [modalità di orchestrazione](../virtual-machine-scale-sets/orchestration-modes.md) su 2. Il set di disponibilità non è supportato.
Eseguire il backup di macchine virtuali distribuite da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (pubblicazione da parte di Microsoft, terze parti) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo). Le macchine virtuali di Azure Marketplace supportate come macchine virtuali non vengono ripristinate, in quanto queste richiedono informazioni sugli acquisti. Vengono ripristinati solo come dischi.
Backup di macchine virtuali distribuite da un'immagine personalizzata (terze parti) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Backup di macchine virtuali di cui è stata eseguita la migrazione in Azure| Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione.
Backup della coerenza tra più macchine virtuali | Backup di Azure non garantisce la coerenza dei dati e delle applicazioni tra più macchine virtuali.
Backup con [impostazioni di diagnostica](../azure-monitor/platform/platform-logs-overview.md)  | Non supportato. <br/><br/> Se il ripristino della macchina virtuale di Azure con impostazioni di diagnostica viene attivato usando l'opzione [Crea nuova](backup-azure-arm-restore-vms.md#create-a-vm), il ripristino ha esito negativo.
Ripristino di macchine virtuali aggiunte alla zona | Supportato (per le macchine virtuali di cui viene eseguito il backup dopo il mese di gennaio 2019 e dove sono disponibili [zone di disponibilità](https://azure.microsoft.com/global-infrastructure/availability-zones/)).<br/><br/>È attualmente supportato il ripristino nella stessa zona a cui le macchine virtuali sono aggiunte. Se tuttavia la zona non è disponibile, il ripristino ha esito negativo.
Macchine virtuali di seconda generazione | Supportato <br> Backup di Azure supporta il backup e il ripristino di [macchine virtuali di seconda generazione](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando queste VM vengono ripristinate dal punto di ripristino, vengono ripristinate come [macchine virtuali Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).
Backup di macchine virtuali di Azure con blocchi | Non supportato per le macchine virtuali non gestite. <br><br> Supportato per le macchine virtuali gestite.
[VM Spot](../virtual-machines/windows/spot-vms.md) | Non supportato. Backup di Azure Ripristina le VM spot come normali macchine virtuali di Azure.

## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Dischi di dati delle VM di Azure | Il supporto per il backup di macchine virtuali di Azure con un massimo di 32 dischi è in anteprima pubblica in tutte le aree.<br><br> Il supporto per il backup di macchine virtuali di Azure con dischi non gestiti o di macchine classiche copre un massimo di soli 16 dischi.
Dimensioni del disco dati | Le dimensioni possono raggiungere i 32 TB per un disco singolo e un massimo di 256 TB combinati per tutti i dischi in una VM.
Tipo di archiviazione | HDD Standard, SDD Standard, SDD Premium.
Dischi gestiti | Supportato.
Dischi crittografati | Supportato.<br/><br/> Le macchine virtuali di Azure abilitate con Crittografia dischi di Azure possono essere sottoposte a backup (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportato.<br/><br/> Backup di Azure esclude automaticamente i dischi con acceleratore di scrittura abilitato durante il backup. Poiché non vengono sottoposti a backup, non è possibile ripristinare questi dischi dai punti di ripristino della macchina virtuale. <br><br> **Nota importante**: per la riuscita del backup delle macchine virtuali con dischi con acceleratore di scrittura è necessaria la connettività Internet (anche se tali dischi sono esclusi dal backup).
Backup e ripristino di macchine virtuali o dischi deduplicati | Backup di Azure non supporta la deduplicazione. Per altre informazioni, vedere questo [articolo](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -Backup di Azure non viene deduplicato tra le macchine virtuali nell'insieme di credenziali di servizi di ripristino <br/> <br/>  -Se sono presenti macchine virtuali in stato di deduplicazione durante il ripristino, i file non possono essere ripristinati perché l'insieme di credenziali non comprende il formato. Tuttavia, è possibile eseguire correttamente il ripristino completo della macchina virtuale.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.
Archiviazione condivisa| Il backup di macchine virtuali con Volume condiviso cluster (CSV) o File server di scalabilità orizzontale non è supportato. È probabile che i writer Volume condiviso cluster generino un errore durante il backup. Al momento del ripristino, i dischi contenenti volumi Volume condiviso cluster potrebbero non essere disponibili.
[Dischi condivisi](../virtual-machines/disks-shared-enable.md) | Non supportato.

## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Numero di schede di interfaccia di rete | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. La rimozione di schede di interfaccia di rete dopo l'abilitazione della protezione non incide sul conteggio.
Bilanciamento del carico interno/esterno |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete| Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici| Supportato.<br/><br/> Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete oppure creare un indirizzo e associarlo a tale scheda dopo l'esecuzione del ripristino.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |Supportato.
Indirizzo IP statico | Non supportato.<br/><br/> A una nuova macchina virtuale creata a partire da un punto di ripristino viene assegnato un indirizzo IP dinamico.<br/><br/> Per le macchine virtuali classiche, non è possibile eseguire il backup con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |Supportato.<br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine usa indirizzi IP dinamici, per impostazione predefinita farà altrettanto anche la scheda di interfaccia di rete nella macchina virtuale ripristinata.
Gestione traffico di Azure| Supportato.<br/><br/>Se la macchina virtuale sottoposta a backup è in Gestione traffico, aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di Gestione traffico.
DNS di Azure |Supportato.
DNS personalizzato |Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato.
Endpoint servizio di rete virtuale| Supportato.<br/><br/> Le impostazioni degli account di archiviazione di firewall e rete virtuale devono consentire l'accesso da tutte le reti.

## <a name="vm-security-and-encryption-support"></a>Supporto della sicurezza e della crittografia delle macchine virtuali

Backup di Azure supporta la crittografia per i dati in movimento e inattivi:

Traffico di rete verso Azure:

- Il traffico di backup dai server all'insieme di credenziali di Servizi di ripristino viene crittografato usando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Per sbloccare i dati è sufficiente la chiave di crittografia. Microsoft non può decrittografare i dati di backup in nessun caso.

  > [!WARNING]
  > Dopo che l'insieme di credenziali è stato configurato, solo il cliente ha accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Sicurezza dei dati:

- Per il backup delle macchine virtuali di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Backup di Azure supporta Crittografia dischi di Azure, che usa BitLocker per le macchine virtuali Windows e **dm-crypt** per le macchine virtuali Linux.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Sì][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]

## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Tenere presente quanto segue:

- Per le VM di Azure, l'estensione VM legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione. Non è necessario comprimere il traffico.
- Se si usa DPM o un server di Backup di Microsoft Azure, per risparmiare larghezza di banda è possibile comprimere i dati prima di eseguirne il backup in DPM o nel server di Backup di Microsoft Azure.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Compressione in insieme di credenziali (HTTPS)**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ND | ![Sì][green]
Macchine virtuali di Azure | ND | ND
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup delle macchine virtuali di Azure](backup-azure-arm-vms-prepare.md).
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
