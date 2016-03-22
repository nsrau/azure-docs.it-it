<properties 
	pageTitle="Site Recovery: domande frequenti | Microsoft Azure" 
	description="Questo articolo risponde alle domande frequenti su Azure Site Recovery."
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Azure Site Recovery: domande frequenti

Questo articolo riporta le domande frequenti su Site Recovery.

Nel caso di altre domande dopo la lettura di questo articolo, è possibile pubblicarle in fondo a questo articolo o nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Panoramica

Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) per assicurare che i carichi di lavoro e le app rimangano disponibili in caso di interruzioni. Site Recovery orchestra e automatizza la replica di macchine virtuali e server fisici locali in Azure o in un data center secondario. [Altre informazioni](site-recovery-overview.md)


### Quali elementi può proteggere Site Recovery?

- **Macchine virtuali Hyper-V**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale Hyper-V. 
- **Server fisici**: Site Recovery può proteggere i carichi di lavoro in esecuzione in server fisici Windows o Linux.
- **Macchine virtuali VMware**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una VM VMware.


### Requisiti di Hyper-V

I requisiti per il server host Hyper-V dipendono dallo scenario di distribuzione. Verificare i prerequisiti in:

- [Replica di macchine virtuali Hyper-V in Azure (senza VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replica di macchine virtuali Hyper-V in Azure (con VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replica di macchine virtuali Hyper-V in un data center secondario](site-recovery-vmm-to-vmm.md#before-you-start)

Requisiti per i guest in esecuzione nel server host Hyper-V:

- Se si esegue la replica in un sito secondario, vedere i sistemi operativi guest supportati per le VM nell'articolo relativo ai [sistemi operativi guest supportati per le macchine virtuali Hyper-V](https://technet.microsoft.com/library/mt126277.aspx) eseguite in server host Hyper-V.
- Se si esegue la replica in Azure, Site Recovery supporta tutti i sistemi operativi guest [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### È possibile proteggere le macchine virtuali quando Hyper-V è in esecuzione in un sistema operativo client?

No, le VM devono trovarsi in un server host Hyper-V in esecuzione in un computer server Windows supportato. Se è necessario proteggere un computer client, è possibile replicarlo come computer fisico [in Azure](site-recovery-vmware-to-azure-classic.md) o in un [data center secondario](site-recovery-vmware-to-vmware.md).


### Quali carichi di lavoro è possibile proteggere con Site Recovery?

Site Recovery può replicare la maggior parte dei carichi di lavoro in esecuzione in una VM supportate o un server fisico. Site Recovery fornisce anche il supporto per la replica compatibile con l'applicazione per consentire il ripristino delle app a uno stato intelligente. Si integra con le applicazioni Microsoft, tra cui SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.


### È sempre necessario un server VMM di System Center per proteggere le macchine virtuali Hyper-V? 

Per la replica in un data center secondario, le VM Hyper-V devono trovarsi in server host Hyper-V in un cloud VMM. Se però si vuole eseguire la replica in Azure, è possibile replicare le VM nei server host Hyper-V con o senza cloud VMM. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md)

### È possibile distribuire Site Recovery con VMM se si dispone di un solo server VMM? 

Sì. È possibile replicare le macchine virtuali Hyper-V nel cloud sul server VMM in Azure oppure tra cloud VMM sullo stesso server. Per la replica da locale a locale è consigliabile avere un server VMM nei siti primario e secondario. [Altre informazioni](site-recovery-single-vmm.md)

### Quali server fisici è possibile proteggere?

È possibile proteggere server fisici che eseguono Windows e Linux in Azure o in un sito secondario. [Informazioni](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sui requisiti del sistema operativo. Le stesse limitazioni si applicano se si esegue la replica di server fisici in Azure o in un sito secondario.

Si noti che se il server locale si arresta, i server fisici vengono eseguiti come le macchine virtuali in Azure. Il failback a un server fisico locale non è attualmente supportato. È possibile eseguire il failback solo a una macchina virtuale in esecuzione in VMware.

### Quali macchine virtuali VMware è possibile proteggere?

Per proteggere le VM VMware, è necessario un hypervisor vSphere e macchine virtuali che eseguono strumenti VMware. È anche consigliabile avere un server VMware vCenter per gestire gli hypervisor. [Altre informazioni](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sui requisiti specifici per i server e le VM VMware. Le stesse limitazioni si applicano se si esegue la replica di VM VMware in Azure o in un sito secondario.

### È necessario disporre di prerequisiti specifici per la replica di macchine virtuali in Azure?

Le macchine virtuali da replicare in Azure devono essere conformi ai [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### È possibile replicare le macchine virtuali Hyper-V di seconda generazione in Azure?

Sì. Site Recovery converte le VM dalla seconda generazione alla prima durante il failover. In caso di failback la VM viene riconvertita nella seconda generazione. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se si esegue la replica in Azure, come vengono addebitati i costi per le macchine virtuali di Azure? 

Durante la replica regolare, i dati vengono replicati nell'archiviazione di Azure con ridondanza geografica e non vengono addebitati i costi di macchine virtuali IaaS di Azure. Questo aspetto rappresenta un vantaggio significativo. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure e in seguito vengono fatturate le risorse di calcolo utilizzate in Azure.

### È possibile gestire il ripristino di emergenza per le succursali con Site Recovery?

Sì. Quando si usa Site Recovery per gestire la replica e il failover nelle succursali, si ottiene una gestione unificata e vengono visualizzati tutti i carichi di lavoro delle succursali in una posizione centrale. È possibile eseguire facilmente i failover e amministrare il ripristino di emergenza di tutte le succursali senza abbandonare la sede centrale.

### È disponibile un SDK che consenta di automatizzare il flusso di lavoro di Site Recovery?

Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Altre informazioni su [Azure Site Recovery con PowerShell e Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).


## Sicurezza e conformità

### I dati di replica vengono inviati al servizio Site Recovery?

No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che eseguono le macchine virtuali o i server fisici.

I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware o server fisici e Archiviazione di Azure o il sito secondario. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.

Site Recovery è certificato ISO 27001:2005 e le valutazioni HIPAA, DPA e FedRAMP JAB sono in fase di completamento.

### Per la conformità è necessario che anche i metadati degli ambienti locali restino all'interno della stessa area geografica. Site Recovery può garantire questo requisito?

Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area, tutti i metadati necessari per abilitare e gestire la replica e il failover restano all'interno dei confini di tale area geografica.

### Site Recovery consente di crittografare la replica?
Per la replica di macchine virtuali e server fisici tra siti locali, è supportata la crittografia in transito. Per la replica di macchine virtuali e server fisici in Azure, sono supportate sia la crittografia in transito che la crittografia inattiva (in Azure).




## Replica e failover

### Se si esegue la replica in Azure che tipo di account di archiviazione è necessario?

È necessario un account di archiviazione con [archiviazione con ridondanza geografica standard](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Archiviazione Premium non è attualmente supportata.

### Con quale frequenza è possibile eseguire la replica dei dati?
- **Hyper-V:** le VM Hyper-V eseguite in Windows Server 2012 R2 possono essere replicate ogni 30 secondi, 5 minuti o 15 minuti. Se è stata configurata la replica SAN, questa sarà sincrona.
- **VMware e server fisici:** in questo caso la frequenza di replica non è rilevante. La replica sarà continua. 

### È possibile estendere la replica dal sito di ripristino esistente a un altro sito di ripristino?
No, la replica concatenata o estesa non è supportata. [Inviare commenti e suggerimenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) su questa funzionalità.


### È possibile eseguire una replica offline la prima volta che si esegue la replica in Azure? 

Questa funzionalità non è supportata. [Inviare commenti e suggerimenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) su questa funzionalità.


### È possibile escludere dischi specifici dalla replica?

Questa funzionalità non è supportata. [Inviare commenti e suggerimenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) su questa funzionalità.

### È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?

I dischi dinamici sono supportati durante la replica delle macchine virtuali Hyper-V, anche durante la replica delle VM VMware e dei computer fisici se si usa la [distribuzione avanzata](site-recovery-vmware-to-azure-classic.md). Si noti che il disco del sistema operativo deve essere un disco di base.


### Come di accede alle macchine virtuali di Azure replicate dopo il failover in Azure? 

È possibile accedere alle VM di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Le comunicazioni tramite connessione VPN vengono eseguite alle porte interne della rete di Azure in cui si trova la macchina virtuale. Le comunicazioni tramite Internet sono mappate agli endpoint pubblici nel servizio cloud di Azure per le VM.

### Se si esegue il failover in Azure, in che modo Azure assicura che i dati siano resilienti?

Azure è progettato nell'ottica della resilienza. Site Recovery è già progettato per il failover a un centro dati secondario di Azure in conformità con il contratto di servizio di Azure, se necessario. In questo caso, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### Se si esegue la replica tra due siti, cosa accade se nel sito primario si verifica un'interruzione imprevista?

È possibile attivare un failover non pianificato dal sito secondario. Per eseguire il failover, in Site Recovery non è necessaria la connettività dal sito primario.


### Il failover è automatico?

Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure è possibile usare i [cmdlet di PowerShell per Azure Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx) per attivare un failover. Il failback è costituito da una serie di azioni nel portale di Site Recovery

Per avviare il failover in automatico, è possibile usare Orchestrator o Operations Manager locale per rilevare un errore della macchina virtuale e quindi attivare il failover tramite l'SDK.

### È possibile applicare limitazioni della larghezza di banda allocata per il traffico di replica Hyper-V?
- Se si esegue la replica tra due siti locali delle macchine virtuali Hyper-V, è possibile usare QoS di Windows. Ecco uno script di esempio: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Se si esegue la replica di macchine virtuali Hyper-V in Azure, è possibile configurare la limitazione tramite il cmdlet PowerShell di esempio seguente:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [Altre informazioni](https://support.microsoft.com/it-IT/kb/3056159) sulla limitazione del traffico di Hyper-V.
- [Altre informazioni](site-recovery-vmware-to-azure-classic.md#capacity-planning) sulla limitazione della replica VMware in Azure.



## Distribuzione del provider di servizi

### Site Recovery funziona per modelli di infrastruttura dedicati o condivisi?
Sì, Site Recovery supporta i modelli di infrastruttura dedicati e condivisi.

### L'identità del tenant è condivisa con il servizio Site Recovery?
No. Per i tenant non è necessario l'accesso al portale di Site Recovery. Solo l'amministratore del provider di servizi interagisce con il portale.


### I dati delle applicazioni tenant saranno ricevuti da Azure?
Quando si esegue la replica tra i siti di proprietà del provider di servizi, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati in transito e replicati direttamente tra i siti del provider di servizi.

Se si esegue la replica in Azure, i dati dell'applicazione vengono inviati all'archiviazione di Azure, ma non al servizio Site Recovery. I dati vengono crittografati in transito e rimangono crittografati in Azure.

### I tenant riceveranno una fattura per ogni servizio Azure?

No. La fatturazione di Azure avviene direttamente con il provider di servizi. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### Se si esegue la replica in Azure, è sempre necessario eseguire le macchine virtuali in Azure?

No, i dati vengono replicati in un account di archiviazione di Azure con ridondanza geografica nella propria sottoscrizione. Quando si esegue un failover di test (un'analisi del ripristino di emergenza) o un failover effettivo, Site Recovery crea automaticamente macchine virtuali nella sottoscrizione.

### È possibile garantire isolamento a livello di tenant durante la replica in Azure?

Sì.

### Quali piattaforme sono attualmente supportate?

Sono supportate distribuzioni in Azure Pack, Cloud Platform System e System Center (2012 e versioni successive). Per altre informazioni sull'integrazione di Azure Pack, vedere [Configurare la protezione per le macchine virtuali](https://technet.microsoft.com/library/dn850370.aspx).

### Sono supportate singole distribuzioni di Azure Pack e del server VMM?

Sì, è possibile replicare macchine virtuali Hyper-V e Azure o tra siti del provider di servizi. Si noti che se si esegue la replica tra i siti del provider di servizi, l'integrazione del runbook di Azure non è disponibile.


## Passaggi successivi

- Leggere la [panoramica di Site Recovery](site-recovery-overview.md)
- Informazioni sull'[architettura di Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0316_2016-->