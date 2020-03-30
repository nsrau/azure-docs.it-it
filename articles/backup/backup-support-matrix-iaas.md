---
title: Matrice di supporto per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389291"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure

È possibile usare il servizio Backup di Azure per eseguire il backup di macchine e carichi di lavoro locali e di macchine virtuali di Azure.You can use the [Azure Backup service](backup-overview.md) to back up on on-premises machines and workloads, and Azure virtual machines (VMs). Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si esegue il backup delle macchine virtuali di Azure con Backup di Azure.This article summarizes support settings and limitations when you back up Azure VMs with Azure Backup.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per Backup di AzureGeneral support matrix for Azure Backup
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il backup del server di backup di Azure/System Center Data Protection Manager (DPM)
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.

**Scenario** | **Backup** | **Agente** |**Ripristina**
--- | --- | --- | ---
backup diretto di macchine virtuali di Azure  | Eseguire il backup dell'intera macchina virtuale.  | Non è necessario alcun agente aggiuntivo nella macchina virtuale di Azure.No additional agent is needed on the Azure VM. Azure Backup installa e usa un'estensione per [l'agente di macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) di Azure in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino nel modo seguente:<br/><br/> - **Creare una macchina virtuale di base**. Ciò è utile se la macchina virtuale non ha una configurazione speciale, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco Quindi collegarlo a una macchina virtuale esistente o creare una nuova macchina virtuale dal disco usando PowerShell.Then attach it to an existing VM, or create a new VM from the disk by using PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare file/cartelle da una macchina virtuale anziché dall'intera macchina virtuale.
Backup diretto di macchine virtuali di Azure (solo Windows)  | Eseguire il backup di file/cartelle/volume specifici. | Installare [l'agente di Servizi di ripristino di Azure.](backup-azure-file-folder-backup-faq.md)<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
Backup di una macchina virtuale di Azure nel server di backup  | Eseguire il backup di file/cartelle/volumi; file metallici di stato del sistema; dati dell'app a System Center DPM o al server di backup di Microsoft Azure (MABS).<br/><br/> DPM/MABS esegue quindi il backup nell'insieme di credenziali di backup. | Installare l'agente protezione DPM/MABS nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app.

Ulteriori informazioni sul backup [utilizzando un server](backup-architecture.md#architecture-back-up-to-dpmmabs) di backup e sui requisiti di [supporto.](backup-support-matrix-mabs-dpm.md)

>[!NOTE]
> **Backup di Azure supporta ora il backup e il ripristino selettivi del disco tramite la soluzione di backup della macchina virtuale di Azure.Azure Backup now supports selective disk backup and restore using the Azure Virtual Machine backup solution.**
>
>Oggi, Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. Con la funzionalità exclude-disk, è possibile eseguire il backup di uno o pochi dischi dati dai molti dischi dati in una macchina virtuale. Ciò offre una soluzione efficiente ed economica per le vostre esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup, che consente di ripristinare un sottoinsieme di dischi dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dal vault.
>
>**Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto**
--- | ---
Eseguire il backup di una macchina virtuale arrestata/offline | Supportato.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Eseguire il backup dei dischi dopo la migrazione a dischi gestiti | Supportato.<br/><br/> Il backup continuerà a funzionare. Non è richiesta alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportato.<br/><br/> Backup di Azure non è in grado di eliminare i punti di ripristino precedenti e i backup inizieranno a non riuscire quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportato.<br/><br/> Verrà eseguito il backup della macchina virtuale usando le impostazioni di pianificazione e conservazione nei nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se vengono ridotti, i punti di ripristino esistenti verranno eliminati nel processo di pulizia successivo ed eventualmente eliminati.
Annullamento di un processo di backup| Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali.
Backup della macchina virtuale in un'area o una sottoscrizione diversa |Non supportato.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/>Il servizio Backup di Azure supporta fino a nove backup su richiesta al giorno, ma Microsoft consiglia non più di quattro backup giornalieri su richiesta per garantire prestazioni ottimali.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno.
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale| Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportato.<br/><br/> Backup di Azure non viene regolato automaticamente per le modifiche dell'ora legale durante il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |La disabilitazione delle funzionalità di sicurezza non è supportata.
Eseguire il backup della macchina virtuale la cui ora del computer viene modificata | Non supportato.<br/><br/> Se l'ora del computer viene modificata in una data-ora futura dopo l'abilitazione del backup per tale macchina virtuale; Tuttavia, anche se la modifica dell'ora viene ripristinata, il backup riuscito non è garantito.  

## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

The following table summarizes the supported operating systems when backing up Windows Azure VMs.

**Scenario** | **Supporto del sistema operativo**
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | - Windows 10 Client (solo 64 bit) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> - Windows Server 2008 (solo 64 bit)
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati.](backup-support-matrix-mars-agent.md#supported-operating-systems)
Eseguire il backup con DPM/MABS | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Backup di Azure non supporta i sistemi operativi a 32 bit.

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto**
--- | ---
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino, è possibile creare una nuova macchina virtuale, ripristinarla e usarla per creare una macchina virtuale o ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportato.<br/><br/> L'agente MARS può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportato.

## <a name="operating-system-support-linux"></a>Supporto dei sistemi operativi (Linux)

Per i backup Linux delle macchine virtuali di Azure, il servizio Backup di Azure supporta l'[elenco di distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tenere presente quanto segue:

- Backup di Azure non supporta Core OS Linux.
- Backup di Azure non supporta i sistemi operativi a 32 bit.
- Altre distribuzioni Linux bring-your-own potrebbero funzionare finché [l'agente di macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) di Azure per Linux è disponibile nella macchina virtuale e finché Python è supportato.
- Backup di Azure non supporta una macchina virtuale Linux configurata dal proxy se non è installata una versione 2.7 di Python.Azure Backup doesn't support a proxy-configured Linux VM if it does not have Python versione 2.7 installed.

## <a name="backup-frequency-and-retention"></a>Frequenza e conservazione dei backup

**Impostazione** | **Limiti**
--- | ---
Numero massimo di punti di ripristino per istanza protetta (computer/carico di lavoro)Maximum recovery points per protected instance (machine/workload) | 9999.
Tempo massimo prima della scadenza di un punto di ripristino | Nessun limite.
Frequenza massima di backup nell'insieme di credenziali (estensione della macchina virtuale di Azure) | Una volta al giorno.
Frequenza massima di backup nell'insieme di credenziali (agente di Servizi di ripristino di Microsoft Azure) | Tre backup al giorno.
Frequenza massima di backup in DPM/MABS | Ogni 15 minuti per SQL Server.<br/><br/> Una volta all'ora per altri carichi di lavoro.
Conservazione dei punti di ripristino | Giornaliero, settimanale, mensile e annuale.
Periodo massimo di conservazione | Dipende dalla frequenza dei backup.
Punti di ripristino su disco DPM/MABS | 64 per i file server e 448 per i server applicazioni.<br/><br/> I punti di ripristino su nastro sono illimitati per DPM locale.

## <a name="supported-restore-methods"></a>Metodi di ripristino supportati

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la macchina virtuale, selezionare il gruppo di risorse e la rete virtuale (VNet) in cui verrà inserita e specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova macchina virtuale deve essere creata nella stessa area della macchina virtuale di origine.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, questa opzione non può essere utilizzata.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente prima di sostituire il disco e lo archivia nel percorso di gestione temporanea specificato. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e mantenuto in base ai criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene mantenuto nel gruppo di risorse. È possibile scegliere di eliminare manualmente i dischi originali se non sono necessari. <br/><br/>L'opzione Sostituisci esistente è supportata per le macchine virtuali gestite non crittografate. Non è supportata per i dischi non gestiti, le [macchine virtuali generalizzate](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) o le macchine virtuali [create usando immagini personalizzate](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> La sostituzione esistente non è supportata per le macchine virtuali con risorse collegate (ad esempio [l'identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) o [l'insieme](https://docs.microsoft.com/azure/key-vault/key-vault-overview)di credenziali delle chiavi) perché l'app client di backup non dispone delle autorizzazioni per queste risorse durante l'esecuzione del ripristino.
**Regione trasversale (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare le macchine virtuali di Azure nell'area secondaria, ovvero un'area associata ad Azure.Cross Region restore can be used to restore Azure VMs in the secondary region, which is an [Azure paired region.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> È possibile ripristinare tutte le macchine virtuali di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Questa funzione è disponibile per le seguenti opzioni:<br> * [Creare una macchina virtualeCreate a VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Ripristina dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Al momento non è supportata l'opzione [Sostituisci dischi esistenti.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita dagli amministratori di backup e dagli amministratori dell'app.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Ripristina** | **Supportato**
--- | ---
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere la [tabella Sistema operativo compatibile](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file da macchine virtuali crittografate | Non supportato.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportato.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

Nella tabella seguente viene riepilogato il supporto per il backup durante le attività di gestione delle macchine virtuali, ad esempio l'aggiunta o la sostituzione di dischi della macchina virtuale.

**Ripristina** | **Supportato**
--- | ---
Ripristino a livello di sottoscrizione/area/zona. | Non supportato.
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportato.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti |Non supportato.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti.
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per i dischi gestiti, è possibile ripristinare il disco e usare l'opzione del set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristinare il backup delle macchine virtuali non gestite dopo l'aggiornamento alla macchina virtuale gestitaRestore backup of unmanaged VMs after upgrading to managed VM| Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e si crea una macchina virtuale tramite PowerShell.Supported if you restore the disk and create a VM by using PowerShell.
Ripristinare la macchina virtuale in una rete virtuale diversaRestore VM in different virtual network |Supportato.<br/><br/> La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area.

## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Calcolare** | **Supporto**
--- | ---
Dimensioni macchina virtuale |Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Scopri di più.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup di macchine virtuali in [set di disponibilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Supportato.<br/><br/> Non è possibile ripristinare una macchina virtuale in un set disponibile usando l'opzione per creare rapidamente una macchina virtuale. Al contrario, quando si ripristina la macchina virtuale, ripristinare il disco e usarlo per distribuire una macchina virtuale o ripristinare un disco e usarlo per sostituire un disco esistente.
Eseguire il backup delle macchine virtuali distribuite con [Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Supportato.
Eseguire il backup delle macchine virtuali distribuite in un set di [scalabilitàBack](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) up VMs that are deployed in a scale set |Non supportato.
Eseguire il backup delle macchine virtuali distribuite da [Azure MarketplaceBack](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) up VMs that are deployed from the Azure Marketplace<br/><br/> (Pubblicato da Microsoft, terze parti) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo). Le macchine virtuali di Azure Marketplace non vengono ripristinate come macchine virtuali, poiché sono necessarie informazioni di acquisto, ma solo come dischi.
Eseguire il backup delle macchine virtuali distribuite da un'immagine personalizzata (di terze parti)Back up VMs that are deployed from a custom image (third-party) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Eseguire il backup delle macchine virtuali migrate in AzureBack up VMs that are migrated to Azure| Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione.
Eseguire il backup della coerenza multi-VMBack up Multi-VM consistency | Backup di Azure non fornisce la coerenza dei dati e delle applicazioni tra più macchine virtuali.
Backup con [impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Non supportato. <br/><br/> Se il ripristino della macchina virtuale di Azure con le impostazioni di diagnostica viene attivato usando l'opzione [Crea nuovo,](backup-azure-arm-restore-vms.md#create-a-vm) il ripristino non riesce.
Ripristino delle macchine virtuali aggiunte alla zona | Supportato (per le macchine virtuali di cui viene eseguito il backup dopo gennaio 2019 e dove sono disponibili le zone di [disponibilità).](https://azure.microsoft.com/global-infrastructure/availability-zones/)<br/><br/>Attualmente è supportato il ripristino nella stessa area aggiunta nelle macchine virtuali. Tuttavia, se la zona non è disponibile, il ripristino non riesce.
Macchine virtuali Gen2 | Supportato <br> Backup di Azure supporta il backup e il ripristino delle [macchine virtuali Gen2.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Quando queste macchine virtuali vengono ripristinate dal punto di ripristino, vengono ripristinate come [macchine virtuali Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Dischi di dati delle VM di Azure | Eseguire il backup di una macchina virtuale con 16 o meno dischi dati.<BR> Per iscriversi all'anteprima privata delle macchine virtuali con più di 16 dischi (massimo 32), scrivere all'indirizzo AskAzureBackupTeam@microsoft.com
Dimensioni del disco dati | Le dimensioni dei singoli dischi possono essere fino a 32 TB e un massimo di 256 TB combinate per tutti i dischi in una macchina virtuale.
Tipo di archiviazione | HDD standard, SSD standard, SSD Premium.
Dischi gestiti | Supportato.
Dischi crittografati | Supportato.<br/><br/> È possibile eseguire il backup delle macchine virtuali di Azure abilitate con Crittografia disco di Azure (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere ripristinate a livello di file/cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportato.<br/><br/> Il backup di Azure esclude automaticamente i dischi con L'acceleratore di scrittura abilitato durante il backup. Poiché non ne viene eseguito il backup, non sarà possibile ripristinare questi dischi dai punti di ripristino della macchina virtuale.
Eseguire il backup & ripristinare macchine virtuali/dischi deduplicati | Backup di Azure non supporta la deduplicazione. Per altre informazioni, vedere questo [articoloFor](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) more information, see this article <br/> <br/>  - Backup di Azure non viene deduplicato tra le macchine virtuali nell'insieme di credenziali di Servizi di ripristino-Azure Backup does not deduplicate across VMs in the Recovery Services vault <br/> <br/>  - Se sono presenti macchine virtuali in stato di deduplicazione durante il ripristino, i file non possono essere ripristinati come vault non capisce il formato. Tuttavia, sarà possibile eseguire correttamente il ripristino completo della macchina virtuale.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.
Archiviazione condivisa| Il backup delle macchine virtuali tramite il volume condiviso del cluster (CSV) o il file server di scalabilità orizzontale non è supportato. È probabile che i writer CSV non riescano durante il backup. Durante il ripristino, i dischi con volumi CSV potrebbero non essere attivati.
[Dischi condivisi](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Non supportato.

## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali

**Componente** | **Supporto**
--- | ---
Numero di interfacce di rete (NIC) | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. La rimozione delle schede di interfaccia di rete dopo aver attivato la protezione non influisce sul conteggio.
Bilanciamento del carico interno/esterno |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete| Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici| Supportato.<br/><br/> Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete oppure creare un indirizzo e associarlo alla scheda di interfaccia di rete al termine del ripristino.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |Supportato.
Indirizzo IP statico | Non supportato.<br/><br/> A una nuova macchina virtuale creata da un punto di ripristino viene assegnato un indirizzo IP dinamico.<br/><br/> Per le macchine virtuali classiche, non è possibile eseguire il backup di una macchina virtuale con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |Supportato.<br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine usa l'indirizzamento IP dinamico, per impostazione predefinita verrà usata anche la scheda di interfaccia di rete nella macchina virtuale ripristinata.
Gestione traffico di Azure| Supportato.<br/><br/>Se la macchina virtuale di cui è stato eseguito il backup è in Gestione traffico, aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di Gestione traffico.
DNS di Azure |Supportato.
DNS personalizzato |Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato.
Endpoint servizio di rete virtuale| Supportato.<br/><br/> Le impostazioni dell'account di archiviazione della rete virtuale e del firewall devono consentire l'accesso da tutte le reti.

## <a name="vm-security-and-encryption-support"></a>Supporto per la sicurezza e la crittografia delle macchine virtualiVM security and encryption support

Backup di Azure supporta la crittografia per i dati in movimento e inattivi:

Traffico di rete verso Azure:

- Il traffico di backup dai server all'insieme di credenziali di Servizi di ripristino viene crittografato utilizzando Advanced Encryption Standard 256.
- I dati di backup vengono inviati tramite un collegamento HTTPS sicuro.
- I dati di backup vengono archiviati nell'insieme di credenziali di Servizi di ripristino in formato crittografato.
- Solo il cliente ha la passphrase per sbloccare questi dati. Microsoft non può decrittografare i dati di backup in nessun caso.

  > [!WARNING]
  > Dopo aver impostato l'insieme di credenziali, solo tu hai accesso alla chiave di crittografia. Microsoft non conserva mai una copia e non ha accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Sicurezza dei dati:

- Quando si esegue il backup delle macchine virtuali di Azure, è necessario configurare la crittografia *all'interno* della macchina virtuale.
- Azure Backup supporta Crittografia disco di Azure, che usa BitLocker nelle macchine virtuali Windows e dm-crypt nelle macchine virtuali Linux.Azure Backup supports Azure Disk Encryption, which uses BitLocker on Windows virtual machines and us **dm-crypt** on Linux virtual machines.
- Nel back-end Backup di Azure usa [Crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che protegge i dati inattivi.

**Computer** | **In movimento** | **Inattivi**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Sì][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]

## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Il backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Tenere presente quanto segue:

- Per le macchine virtuali di Azure, l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure tramite la rete di archiviazione. Non è necessario comprimere questo traffico.
- Se si utilizza DPM o MABS, è possibile risparmiare larghezza di banda comprimendo i dati prima che venga eseguito il backup in DPM/MABS.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimi nel vault (HTTPS)**
--- | --- | ---
Computer Windows locali senza DPM/MABS | ND | ![Sì][green]
Macchine virtuali di Azure | ND | ND
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il backup delle macchine virtuali](backup-azure-arm-vms-prepare.md)di Azure.
- [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
- [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in MABS.
- [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
