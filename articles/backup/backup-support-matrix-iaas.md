---
title: Matrice di supporto per il backup di macchine virtuali di Azure
description: Informazioni riepilogative su impostazioni e limitazioni del supporto durante il backup di macchine virtuali di Azure con il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80389291"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matrice di supporto per il backup di macchine virtuali di Azure

È possibile usare il [servizio backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali e di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si esegue il backup di macchine virtuali di Azure con backup di Azure.

Altre matrici di supporto:

- [Matrice di supporto generale](backup-support-matrix.md) per backup di Azure
- [Matrice di supporto](backup-support-matrix-mabs-dpm.md) per il server di backup di Azure/System Center Data Protection Manager (DPM) backup
- [Matrice di supporto](backup-support-matrix-mars-agent.md) per il backup con l'agente di servizi di ripristino di Microsoft Azure (Mars)

## <a name="supported-scenarios"></a>Scenari supportati

Di seguito viene illustrato come è possibile eseguire il backup e il ripristino di macchine virtuali di Azure con il servizio Backup di Azure.

**Scenario** | **Backup** | **Agente** |**Recupera**
--- | --- | --- | ---
backup diretto di macchine virtuali di Azure  | Eseguire il backup dell'intera VM.  | Nella macchina virtuale di Azure non è necessario alcun agente aggiuntivo. Backup di Azure installa e usa un'estensione per l' [agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) in esecuzione nella macchina virtuale. | È possibile eseguire il ripristino nel modo seguente:<br/><br/> - **Creare una VM di base**. Questa operazione è utile se la macchina virtuale non ha una configurazione speciale, ad esempio più indirizzi IP.<br/><br/> - **Ripristinare il disco della macchina virtuale**. Ripristinare il disco Quindi collegarlo a una macchina virtuale esistente o creare una nuova macchina virtuale dal disco usando PowerShell.<br/><br/> - **Sostituire il disco della macchina virtuale**. Se è presente una macchina virtuale che usa dischi gestiti (non crittografati), è possibile ripristinare un disco e usarlo per sostituire un disco esistente nella macchina virtuale.<br/><br/> - **Ripristinare cartelle e file specifici**. È possibile ripristinare file e cartelle da una macchina virtuale anziché dall'intera VM.
Backup diretto di macchine virtuali di Azure (solo Windows)  | Eseguire il backup di file/cartelle/volumi specifici. | Installare l' [agente di servizi di ripristino di Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> È possibile eseguire l'agente di Servizi di ripristino di Microsoft Azure insieme all'estensione di backup per l'agente di macchine virtuali di Azure per eseguire il backup della macchina virtuale a livello di file o di cartella. | Ripristino di cartelle e file specifici.
Backup di una macchina virtuale di Azure nel server di backup  | Eseguire il backup di file/cartelle/volumi; file bare metal e dello stato del sistema; dati delle app in System Center DPM o in Backup di Microsoft Azure Server (MAB).<br/><br/> DPM/MAB esegue quindi il backup nell'insieme di credenziali per il backup. | Installare l'agente protezione DPM/MAB nella macchina virtuale. L'agente di Servizi di ripristino di Microsoft Azure viene installato in DPM o nel server di Backup di Microsoft Azure.| Ripristino di file, cartelle e volumi; file dello stato del sistema/bare metal; dati delle app.

Altre informazioni sul backup [con un server di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sui [requisiti di supporto](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Backup di Azure supporta ora il backup e il ripristino dei dischi selettivi con la soluzione di backup della macchina virtuale di Azure.**
>
>Attualmente, backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale con la soluzione di backup della macchina virtuale. Con la funzionalità Escludi disco è possibile scegliere di eseguire il backup di uno o più dischi dati in una macchina virtuale. Questo offre una soluzione efficiente ed economica per le esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup, che consente di avere un subset di dischi ripristinati dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dall'insieme di credenziali.
>
>**Per iscriverti all'anteprima, scrivici all'indirizzoAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Azioni di backup supportate

**Azione** | **Supporto tecnico**
--- | ---
Eseguire il backup di una macchina virtuale arrestata/offline | Supportato.<br/><br/> Lo snapshot è coerente solo con l'arresto anomalo del sistema, non con l'app.
Eseguire il backup dei dischi dopo la migrazione a Managed Disks | Supportato.<br/><br/> Il backup continuerà a funzionare. Non è richiesta alcuna azione.
Backup dei dischi gestiti dopo l'abilitazione del blocco del gruppo di risorse | Non supportata.<br/><br/> Backup di Azure non è in grado di eliminare i punti di ripristino precedenti e i backup inizieranno ad avere esito negativo quando viene raggiunto il limite massimo di punti di ripristino.
Modifica dei criteri di backup per una macchina virtuale | Supportato.<br/><br/> Verrà eseguito il backup della macchina virtuale utilizzando le impostazioni pianificazione e conservazione in nuovi criteri. Se le impostazioni di conservazione vengono estese, i punti di ripristino esistenti verranno contrassegnati e mantenuti. Se sono ridotti, i punti di ripristino esistenti verranno eliminati nel processo di pulizia successivo e infine eliminati.
Annullamento di un processo di backup| Supportata durante il processo di snapshot.<br/><br/> Non supportata quando lo snapshot viene trasferito nell'insieme di credenziali.
Backup della macchina virtuale in un'area o una sottoscrizione diversa |Non supportata.
Backup al giorno (tramite l'estensione della macchina virtuale di Azure) | Un backup pianificato al giorno.<br/><br/>Il servizio backup di Azure supporta fino a nove backup su richiesta al giorno, ma Microsoft consiglia di non avere più di quattro backup su richiesta giornalieri per garantire prestazioni ottimali.
Backup al giorno (tramite l'agente di Servizi di ripristino di Microsoft Azure) | Tre backup pianificati al giorno.
Backup al giorno (tramite DPM o il server di Backup di Microsoft Azure) | Due backup pianificati al giorno.
Backup mensile/annuale| Non supportata quando si esegue il backup con l'estensione della macchina virtuale di Azure. È supportato solo il backup giornaliero e settimanale.<br/><br/> È possibile configurare i criteri in modo da conservare i backup giornalieri/settimanali per il periodo di conservazione mensile/annuale.
Regolazione automatica dell'orologio | Non supportata.<br/><br/> Backup di Azure non viene regolato automaticamente per le modifiche all'ora legale quando si esegue il backup di una macchina virtuale.<br/><br/>  Modificare manualmente i criteri in base alle esigenze.
[Funzionalità di sicurezza per il backup ibrido](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |La disabilitazione delle funzionalità di sicurezza non è supportata.
Eseguire il backup della VM di cui è stata modificata l'ora del computer | Non supportata.<br/><br/> Se l'ora del computer viene modificata in una data e ora successiva dopo l'abilitazione del backup per la VM. Tuttavia, anche se la modifica dell'ora viene ripristinata, il backup non è garantito.  

## <a name="operating-system-support-windows"></a>Supporto dei sistemi operativi (Windows)

Nella tabella seguente sono riepilogati i sistemi operativi supportati per il backup di macchine virtuali di Windows Azure.

**Scenario** | **Supporto del sistema operativo**
--- | ---
Backup con l'estensione dell'agente di macchine virtuali di Azure | -Client Windows 10 (solo 64 bit) <br/><br/>-Windows Server 2019 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2016 (Datacenter/datacenter core/standard) <br/><br/> -Windows Server 2012 R2 (Datacenter/standard) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> -Windows Server 2008 (solo bit 64)
Backup con l'agente di Servizi di ripristino di Microsoft Azure | Sistemi operativi [supportati](backup-support-matrix-mars-agent.md#supported-operating-systems) .
Backup con DPM/MAB | Sistemi operativi supportati per il backup con il [server di Backup di Microsoft Azure](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Backup di Azure non supporta i sistemi operativi a 32 bit.

## <a name="support-for-linux-backup"></a>Supporto per il backup Linux

Se si vuole eseguire il backup di computer Linux, sono supportati gli scenari seguenti.

**Azione** | **Supporto tecnico**
--- | ---
Backup di macchine virtuali di Azure per Linux con l'agente di macchine virtuali di Azure per Linux | Backup coerente con i file.<br/><br/> Backup coerenti con le app tramite [script personalizzati](backup-azure-linux-app-consistent.md).<br/><br/> Durante il ripristino, è possibile creare una nuova macchina virtuale, ripristinare un disco e usarla per creare una VM oppure ripristinare un disco e usarlo per sostituire un disco in una macchina virtuale esistente. È anche possibile ripristinare cartelle e file singoli.
Backup di macchine virtuali di Azure per Linux con l'agente di Servizi di ripristino di Microsoft Azure | Non supportata.<br/><br/> L'agente MARS può essere installato solo in computer Windows.
Backup di macchine virtuali di Azure per Linux con DPM o il server di Backup di Microsoft Azure | Non supportata.

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

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la VM, selezionare il gruppo di risorse e la rete virtuale (VNet) in cui verrà inserito e specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova VM deve essere creata nella stessa area della VM di origine.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, non è possibile usare questa opzione.<br/><br/> Backup di Azure esegue uno snapshot della macchina virtuale esistente prima di sostituire il disco e lo archivia nel percorso di gestione temporanea specificato. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e mantenuto in base ai criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene conservato nel gruppo di risorse. Se non sono necessari, è possibile scegliere di eliminare manualmente i dischi originali. <br/><br/>L'opzione Sostituisci esistente è supportata per le macchine virtuali gestite non crittografate. Non è supportata per i dischi non gestiti, le [macchine virtuali generalizzate](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) o le macchine virtuali [create usando immagini personalizzate](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> Sostituisci esistente non è supportato per le macchine virtuali con risorse collegate (ad esempio, identità gestite o [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) [assegnate dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ) perché l'app client di backup non dispone delle autorizzazioni per queste risorse durante l'esecuzione del ripristino.
**Area geografica (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare macchine virtuali di Azure nell'area secondaria, ovvero un' [area abbinata ad Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> È possibile ripristinare tutte le VM di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Questa funzionalità è disponibile per le opzioni seguenti:<br> * [Creare una macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Ripristinare i dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Attualmente non è supportata l'opzione [Sostituisci dischi esistenti](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita da amministratori di backup e amministratori di app.

## <a name="support-for-file-level-restore"></a>Supporto per il ripristino a livello di file

**Recupera** | **Supportato**
--- | ---
Ripristino dei file nei sistemi operativi | È possibile ripristinare i file in qualsiasi computer che abbia un sistema operativo uguale (o compatibile) a quello della macchina virtuale sottoposta a backup. Vedere la [tabella del sistema operativo compatibile](backup-azure-restore-files-from-vm.md#system-requirements).
Ripristino dei file da macchine virtuali crittografate | Non supportata.
Ripristino dei file da account di archiviazione con limitazioni di rete | Non supportata.
Ripristino dei file nelle macchine virtuali con spazi di archiviazione di Windows | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Ripristinare invece i file in una macchina virtuale compatibile.
Ripristino dei file in una macchina virtuale Linux con LVM/matrici RAID | Il ripristino nella stessa macchina virtuale non è supportato.<br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.
Ripristino dei file con impostazioni di rete speciali | Il ripristino nella stessa macchina virtuale non è supportato. <br/><br/> Eseguire il ripristino in una macchina virtuale compatibile.

## <a name="support-for-vm-management"></a>Supporto per la gestione delle macchine virtuali

La tabella seguente riepiloga il supporto per il backup durante le attività di gestione delle macchine virtuali, ad esempio l'aggiunta o la sostituzione dei dischi delle macchine virtuali.

**Recupera** | **Supportato**
--- | ---
Ripristino a livello di sottoscrizione/area/zona. | Non supportata.
Ripristino in una macchina virtuale esistente | Usare l'opzione relativa alla sostituzione del disco.
Ripristino del disco con un account di archiviazione abilitato per la crittografia del servizio di archiviazione di Azure | Non supportata.<br/><br/> Eseguire il ripristino in un account per cui la crittografia del servizio di archiviazione non sia abilitata.
Ripristino in account di archiviazione misti |Non supportata.<br/><br/> A seconda del tipo di account di archiviazione, tutti i dischi ripristinati saranno Premium o Standard e non misti.
Ripristino della macchina virtuale direttamente in un set di disponibilità | Per Managed disks, è possibile ripristinare il disco e usare l'opzione set di disponibilità nel modello.<br/><br/> Non supportato per i dischi non gestiti. In tal caso, ripristinare il disco e quindi creare una macchina virtuale nel set di disponibilità.
Ripristinare il backup di macchine virtuali non gestite dopo l'aggiornamento alla macchina virtuale gestita| Supportato.<br/><br/> È possibile ripristinare i dischi e quindi creare una macchina virtuale gestita.
Ripristino di una macchina virtuale a un punto di ripristino prima della migrazione di tale macchina a dischi gestiti | Supportato.<br/><br/> È possibile eseguire il ripristino in dischi non gestiti (impostazione predefinita), convertire i dischi ripristinati in un disco gestito e creare una macchina virtuale con i dischi gestiti.
Ripristino di una macchina virtuale eliminata. | Supportato.<br/><br/> È possibile ripristinare la macchina virtuale da un punto di ripristino.
Ripristino di una macchina virtuale controller di dominio che fa parte di una configurazione con più controller di dominio tramite il portale | Supportato se si ripristina il disco e si crea una VM tramite PowerShell.
Ripristinare una macchina virtuale in una rete virtuale diversa |Supportato.<br/><br/> La rete virtuale deve trovarsi nella stessa sottoscrizione e nella stessa area.

## <a name="vm-compute-support"></a>Supporto del calcolo delle macchine virtuali

**Compute** | **Supporto tecnico**
--- | ---
Dimensioni macchina virtuale |Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM.<br/><br/> [Altre informazioni.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup di macchine virtuali in [set di disponibilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Supportato.<br/><br/> Non è possibile ripristinare una macchina virtuale in un set disponibile usando l'opzione per creare rapidamente una macchina virtuale. Al contrario, quando si ripristina la macchina virtuale, ripristinare il disco e usarlo per distribuire una VM oppure ripristinare un disco e usarlo per sostituire un disco esistente.
Eseguire il backup di macchine virtuali distribuite con il [vantaggio Hybrid use (hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Supportato.
Eseguire il backup di macchine virtuali distribuite in un [set di scalabilità](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Non supportata.
Eseguire il backup di macchine virtuali distribuite da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Pubblicato da Microsoft, terze parti) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo). Non vengono ripristinate le macchine virtuali di Azure Marketplace supportate come macchine virtuali, in quanto queste necessitano di informazioni di acquisto ma solo come dischi.
Eseguire il backup di macchine virtuali distribuite da un'immagine personalizzata (di terze parti) |Supportato.<br/><br/> È necessario che la macchina virtuale esegua un sistema operativo supportato.<br/><br/> Quando si ripristinano i file nella macchina virtuale, è possibile eseguire il ripristino solo in un sistema operativo compatibile (non in un sistema operativo precedente o successivo).
Eseguire il backup di macchine virtuali di cui viene eseguita la migrazione in Azure| Supportato.<br/><br/> Per eseguire il backup della macchina virtuale, l'agente di macchine virtuali deve essere installato nella macchina sottoposta a migrazione.
Backup della coerenza tra più macchine virtuali | Backup di Azure non garantisce la coerenza dei dati e delle applicazioni tra più macchine virtuali.
Backup con [impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Non supportato. <br/><br/> Se il ripristino della macchina virtuale di Azure con le impostazioni di diagnostica viene attivato usando [Crea nuova](backup-azure-arm-restore-vms.md#create-a-vm) opzione, il ripristino ha esito negativo.
Ripristino di macchine virtuali con aggiunta di zona | Supportato (per la macchina virtuale di cui è stato eseguito il backup dopo Jan 2019 e la [zona di disponibilità](https://azure.microsoft.com/global-infrastructure/availability-zones/) è disponibile).<br/><br/>È attualmente supportato il ripristino nella stessa zona bloccata nelle VM. Tuttavia, se la zona non è disponibile, il ripristino ha esito negativo.
VM Gen2 | Supportato <br> Backup di Azure supporta il backup e il ripristino di [macchine virtuali Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando queste VM vengono ripristinate dal punto di ripristino, vengono ripristinate come [macchine virtuali Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Supporto per l'archiviazione delle macchine virtuali

**Componente** | **Supporto tecnico**
--- | ---
Dischi di dati delle VM di Azure | Eseguire il backup di una macchina virtuale con un massimo di 16 dischi dati.<BR> Per iscriversi all'anteprima privata delle macchine virtuali con più di 16 dischi (massimo 32), scrivere all'indirizzo AskAzureBackupTeam@microsoft.com
Dimensioni del disco dati | Le dimensioni del disco singolo possono essere fino a 32 TB e un massimo di 256 TB combinati per tutti i dischi in una macchina virtuale.
Tipo di archiviazione | HDD Standard, SDD Standard, SSD Premium.
Dischi gestiti | Supportato.
Dischi crittografati | Supportato.<br/><br/> È possibile eseguire il backup delle macchine virtuali di Azure abilitate con crittografia dischi di Azure (con o senza l'app Azure AD).<br/><br/> Le macchine virtuali crittografate non possono essere recuperate a livello di file o cartella. È necessario ripristinare l'intera macchina virtuale.<br/><br/> È possibile abilitare la crittografia nelle macchine virtuali che sono già protette dal servizio Backup di Azure.
Dischi con l'acceleratore di scrittura abilitato | Non supportata.<br/><br/> Backup di Azure esclude automaticamente i dischi con acceleratore di scrittura abilitati durante il backup. Poiché non viene eseguito il backup, non sarà possibile ripristinare questi dischi dal punto di ripristino della macchina virtuale.
Eseguire il backup & ripristinare VM/dischi deduplicati | Backup di Azure non supporta la deduplicazione. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  -Backup di Azure non viene deduplicato tra VM nell'insieme di credenziali di servizi di ripristino <br/> <br/>  -Se sono presenti macchine virtuali in stato di deduplicazione durante il ripristino, non è possibile ripristinare i file perché l'insieme di credenziali non riconosce il formato. Sarà tuttavia possibile eseguire correttamente il ripristino completo della macchina virtuale.
Aggiunta di un disco a una macchina virtuale protetta | Supportato.
Ridimensionamento di un disco in una macchina virtuale protetta | Supportato.
Archiviazione condivisa| Il backup di macchine virtuali con Volume condiviso cluster (CSV) o File server di scalabilità orizzontale non è supportato. Probabilmente i writer CSV avranno esito negativo durante il backup. Durante il ripristino, i dischi contenenti volumi CSV potrebbero non essere disponibili.
[Dischi condivisi](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Non supportata.

## <a name="vm-network-support"></a>Supporto della rete delle macchine virtuali

**Componente** | **Supporto tecnico**
--- | ---
Numero di interfacce di rete (NIC) | Fino al numero massimo di schede di interfaccia di rete supportato per le specifiche dimensioni della macchina virtuale di Azure.<br/><br/> Vengono create schede di interfaccia di rete contestualmente alla creazione della macchina virtuale durante il processo di ripristino.<br/><br/> Il numero di schede di interfaccia di rete nella macchina virtuale ripristinata rispecchia il numero di schede di interfaccia di rete presenti nella macchina virtuale quando è stata abilitata la protezione. La rimozione delle schede di rete dopo l'abilitazione della protezione non influisce sul conteggio.
Bilanciamento del carico interno/esterno |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Più indirizzi IP riservati |Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con più schede di rete| Supportato. <br/><br/> [Vedere altre informazioni](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sul ripristino delle macchine virtuali con impostazioni di rete speciali.
Macchine virtuali con indirizzi IP pubblici| Supportato.<br/><br/> Associare un indirizzo IP pubblico esistente alla scheda di interfaccia di rete oppure creare un indirizzo e associarlo alla scheda di interfaccia di rete dopo aver eseguito il ripristino.
Gruppo di sicurezza di rete (NSG) nella scheda di interfaccia di rete/subnet. |Supportato.
Indirizzo IP statico | Non supportata.<br/><br/> A una nuova macchina virtuale creata a partire da un punto di ripristino viene assegnato un indirizzo IP dinamico.<br/><br/> Per le macchine virtuali classiche non è possibile eseguire il backup di una macchina virtuale con un indirizzo IP riservato e nessun endpoint definito.
Indirizzo IP dinamico |Supportato.<br/><br/> Se la scheda di interfaccia di rete nella macchina virtuale di origine USA indirizzi IP dinamici, per impostazione predefinita la scheda di interfaccia di rete nella macchina virtuale ripristinata lo userà anche.
Gestione traffico di Azure| Supportato.<br/><br/>Se la macchina virtuale di cui è stato eseguito il backup si trova in gestione traffico, aggiungere manualmente la macchina virtuale ripristinata alla stessa istanza di gestione traffico.
Azure DNS |Supportato.
DNS personalizzato |Supportato.
Connettività in uscita attraverso il proxy HTTP | Supportato.<br/><br/> Un proxy autenticato non è supportato.
Endpoint servizio di rete virtuale| Supportato.<br/><br/> Le impostazioni dell'account di archiviazione di rete virtuale e del firewall devono consentire l'accesso da tutte le reti.

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
Computer Windows locali senza DPM/MABS | ![Sì][green] | ![Sì][green]
Macchine virtuali di Azure | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con DPM | ![Sì][green] | ![Sì][green]
Computer locali/VM di Azure con MABS | ![Sì][green] | ![Sì][green]

## <a name="vm-compression-support"></a>Supporto della compressione delle macchine virtuali

Backup supporta la compressione del traffico di backup, come riepilogato nella tabella seguente. Tenere presente quanto segue:

- Per le macchine virtuali di Azure, l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure tramite la rete di archiviazione. Non è necessario comprimere il traffico.
- Se si usa DPM o MAB, è possibile risparmiare larghezza di banda comprimendo i dati prima di eseguirne il backup in DPM/MAB.

**Computer** | **Compressione in MABS/DPM (TCP)** | **Comprimi nell'insieme di credenziali (HTTPS)**
--- | --- | ---
Computer Windows locali senza DPM/MABS | N/D | ![Sì][green]
Macchine virtuali di Azure | N/D | N/D
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
