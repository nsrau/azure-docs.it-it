<properties 
	pageTitle="Azure Site Recovery: domande frequenti | Microsoft Azure" 
	description="Questo articolo illustra le domande frequenti su Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="get-started-article"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016" 
	ms.author="raynew"/>


# Azure Site Recovery: domande frequenti
## Informazioni sull'articolo

In questo articolo sono riportate le domande frequenti su Azure Site Recovery. Eventuali domande successive alla lettura di questo articolo possono essere pubblicate nel [forum relativo ai Servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Generale

###Quali sono le funzioni di Site Recovery?

Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza mediante la gestione e l'automatizzazione delle operazioni di replica dalle macchine virtuali e dai server fisici locali a Azure o a un data center secondario. [Altre informazioni](site-recovery-overview.md)


### Quali elementi può proteggere Site Recovery?

- **Macchine virtuali Hyper-V**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale Hyper-V.
- **Server fisici**: Site Recovery può proteggere server fisici che eseguono Windows o Linux.
- **Macchine virtuali VMware**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale VMware.

### Site Recovery supporta il modello Azure Resource Manager? 

Oltre che nel portale di Azure classico, Site Recovery è disponibile nel portale di Azure con il supporto di Resource Manager. Per la maggior parte degli scenari di distribuzione, Site Recovery nel portale di Azure offre un'esperienza di distribuzione semplificata e la possibilità di eseguire la replica di VM e server fisici nell'archiviazione classica o di Resource Manager. Ecco le distribuzioni supportate:

- [Replica da VM VMware o server fisici ad Azure nel portale di Azure](site-recovery-vmware-to-azure.md)
- [Replica da VM Hyper-V nei cloud VMM ad Azure nel portale di Azure](site-recovery-vmm-to-azure.md)
- [Replica da VM Hyper-V (senza VMM) ad Azure nel portale di Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replica da VM Hyper-V nei cloud VMM a un sito secondario nel portale di Azure](site-recovery-vmm-to-vmm.md)


### Cosa occorre in Hyper-V per orchestrare la replica con Site Recovery? 

Gli elementi necessari per il server host Hyper-V dipendono dallo scenario di distribuzione. Verificare i prerequisiti di Hyper-V in:

- [Replica di macchine virtuali Hyper-V in Azure (senza VMM)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replica di macchine virtuali Hyper-V in Azure (con VMM)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replica di macchine virtuali Hyper-V in un data center secondario](site-recovery-vmm-to-vmm.md#before-you-start)

- Se si esegue la replica in un centro dati secondario, vedere [Sistemi operativi guest supportati per le VM Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se si esegue la replica in Azure, Site Recovery supporta tutti i sistemi operativi guest [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### È possibile proteggere le macchine virtuali quando Hyper-V è in esecuzione in un sistema operativo client?

No, le VM devono trovarsi in un server host Hyper-V in esecuzione in un computer server Windows supportato. Se è necessario proteggere un computer client, è possibile replicarlo come computer fisico in [Azure](site-recovery-vmware-to-azure.md) o in un [centro dati secondario](site-recovery-vmware-to-vmware.md).


### Quali carichi di lavoro è possibile proteggere con Site Recovery?

È possibile usare Site Recovery per proteggere la maggior parte dei carichi di lavoro in esecuzione in una VM o in un server fisico supportati. Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il ripristino delle app a uno stato intelligente. Si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.


### Gli host Hyper-V devono trovarsi nei cloud VMM? 

Per la replica in un data center secondario, le VM Hyper-V devono trovarsi in server host Hyper-V in un cloud VMM. Se si vuole eseguire la replica in Azure, è possibile replicare le VM nei server host Hyper-V con o senza cloud VMM. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md)

### È possibile distribuire Site Recovery con VMM se si dispone di un solo server VMM? 

Sì. È possibile replicare le VM in server Hyper-V nel cloud VMM in Azure oppure tra cloud VMM nello stesso server. Per la replica da locale a locale è consigliabile avere un server VMM nei siti primario e secondario. [Altre informazioni](site-recovery-single-vmm.md)

### Quali server fisici è possibile proteggere?

È possibile replicare server fisici che eseguono Windows e Linux in Azure o in un sito secondario. [Informazioni](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sui requisiti del sistema operativo. Gli stessi requisiti valgono se si esegue la replica di server fisici in Azure o in un sito secondario.

Si noti che se il server locale si arresta, i server fisici vengono eseguiti come le macchine virtuali in Azure. Il failback in un server fisico locale non è attualmente supportato, ma è possibile eseguirlo in una macchina virtuale in esecuzione in Hyper-V o VMware.


### Quali macchine virtuali VMware è possibile proteggere?

Per proteggere le VM VMware, è necessario un hypervisor vSphere e macchine virtuali che eseguono strumenti VMware. È anche consigliabile avere un server VMware vCenter per gestire gli hypervisor. [Altre informazioni](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sui requisiti specifici per la replica di server e VM VMware in Azure o in un sito secondario.

### È possibile gestire il ripristino di emergenza per le succursali con Site Recovery?

Sì. Quando si usa Site Recovery per gestire la replica e il failover nelle succursali, si ottiene una gestione unificata e vengono visualizzati tutti i carichi di lavoro delle succursali in una posizione centrale. È possibile eseguire facilmente i failover e amministrare il ripristino di emergenza di tutte le succursali senza abbandonare la sede centrale.

## Sicurezza

### I dati di replica vengono inviati al servizio Site Recovery?

No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che eseguono le macchine virtuali o i server fisici. I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware o server fisici e Archiviazione di Azure o il sito secondario. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.


### Per la conformità è necessario che anche i metadati degli ambienti locali restino all'interno della stessa area geografica. Site Recovery può offrire vantaggi?

Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area, tutti i metadati necessari per abilitare e gestire la replica e il failover restano all'interno dei confini di tale area geografica.

### Site Recovery consente di crittografare la replica?

Per la replica di macchine virtuali e server fisici tra siti locali, è supportata la crittografia in transito. Per la replica di macchine virtuali e server fisici in Azure, sono supportate sia la crittografia in transito che la crittografia inattiva (in Azure).


## Replica


### È necessario disporre di prerequisiti specifici per la replica di macchine virtuali in Azure?

Le macchine virtuali da replicare in Azure devono essere conformi ai [requisiti di Azure](site-recovery-best-practices.md#virtual-machines).

### È possibile replicare le macchine virtuali Hyper-V di seconda generazione in Azure?

Sì. Site Recovery esegue la conversione dalla seconda generazione alla prima durante il failover. In caso di failback, la macchina virtuale viene riconvertita nella seconda generazione. [Altre informazioni](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Se si esegue la replica in Azure, come vengono addebitati i costi per le macchine virtuali di Azure? 

Durante la replica regolare, i dati vengono replicati nell'archiviazione di Azure con ridondanza geografica e non vengono addebitati i costi di macchine virtuali IaaS di Azure. Questo aspetto rappresenta un vantaggio significativo. Quando si esegue un failover in Azure, Site Recovery crea automaticamente le macchine virtuali IaaS di Azure e in seguito vengono fatturate le risorse di calcolo utilizzate in Azure.


### È disponibile un SDK che consenta di automatizzare il flusso di lavoro di Azure Site Recovery?

Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la distribuzione di Site Recovery tramite PowerShell:

- [Replica da VM Hyper-V nei cloud VMM ad Azure PowerShell classico](site-recovery-deploy-with-powershell.md)
- [Replica da VM Hyper-V nei cloud VMM ad Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Replica da VM Hyper-V senza VMM ad Azure PowerShell classico](site-recovery-hyper-v-site-to-azure-classic.md)
- [Replica da VM Hyper-V senza VMM ad Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)


### Se si esegue la replica in Azure che tipo di account di archiviazione è necessario?

- **Portale di Azure classico**: se si distribuisce Site Recovery nel portale di Azure classico, sarà necessario un [account di archiviazione con ridondanza geografica standard](../storage/storage-redundancy.md#geo-redundant-storage). Archiviazione Premium non è attualmente supportata. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Site Recovery.

- **Portale di Azure**: se si distribuisce Site Recovery nel portale di Azure, sarà necessario un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Attualmente, l'archiviazione Premium è supportata solo se si esegue la replica da VM VMware o server fisici.

### Con quale frequenza è possibile eseguire la replica dei dati?

- **Hyper-V:** le macchine virtuali Hyper-V possono essere replicate ogni 30 secondi, 5 minuti o 15 minuti. Se è stata configurata la replica SAN, questa sarà sincrona.
- **VMware e server fisici:** in questo caso la frequenza di replica non è rilevante. La replica è continua.

### È possibile estendere la replica dal sito di ripristino esistente a un sito terziario?

No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### È possibile eseguire una replica offline la prima volta che si esegue la replica in Azure? 

Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### È possibile escludere dischi specifici dalla replica?

Questa opzione è supportata quando si [esegue la replica da VM VMware e server fisici](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) ad Azure tramite il portale di Azure.


### È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?

I dischi dinamici sono supportati durante la replica delle macchine virtuali Hyper-V, anche durante la replica da VM VMware e computer fisici ad Azure se si usa il [portale di Azure](site-recovery-vmware-to-azure.md) o il [portale di Azure classico con distribuzione avanzata](site-recovery-vmware-to-azure-classic.md). Si noti che il disco del sistema operativo deve essere un disco di base.

### È possibile applicare limitazioni della larghezza di banda allocata per il traffico di replica Hyper-V?

Sì. Ulteriori informazioni sulla limitazione della larghezza di banda sono disponibili negli articoli sulla distribuzione:

- [Pianificazione della capacità per la replica da VM VMware e server fisici](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Pianificazione della capacità per la replica da VM Hyper-V nei cloud VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Pianificazione della capacità per la replica da VM Hyper-V senza VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## Failover


### Se si esegue il failover in Azure, come è possibile accedere alle macchine virtuali dopo il failover? 

È possibile accedere alle VM di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. Ulteriori informazioni sono disponibili in:

- [Connessione alle VM di Azure dopo il failover di VM VMware o server fisici](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Connessione alle VM di Azure dopo il failover di VM Hyper-V nei cloud VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Connessione alle VM di Azure dopo il failover di VM Hyper-V senza VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### Se si esegue il failover in Azure, in che modo Azure si assicura che i dati siano resilienti?

Azure è progettato nell'ottica della resilienza. Site Recovery è già progettato per il failover a un centro dati secondario di Azure in conformità con il contratto di servizio di Azure, se necessario. In questo caso, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.

### Se si esegue la replica tra due data center, che cosa accade se nel data center principale si verifica un'interruzione imprevista?

È possibile attivare un failover non pianificato dal sito secondario. Per eseguire il failover, in Site Recovery non è necessaria la connettività dal sito primario.

### Il failover è automatico?

Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure mediante [PowerShell per Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx). Anche il failback è un'operazione semplice nel portale di Site Recovery.

Per avviarlo in automatico, è possibile usare Orchestrator o Operations Manager locale per rilevare un errore della macchina virtuale e quindi attivare il failover tramite l'SDK.

- [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
- [Altre informazioni](site-recovery-failover.md) sul failover.
- [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sul failback di VM VMware e server fisici


## Provider di servizi


### Per i provider di servizi, Site Recovery funziona per modelli di infrastruttura dedicati o condivisi?
Sì, Site Recovery supporta i modelli di infrastruttura dedicati e condivisi.

### Per i provider di servizi l'identità del tenant è condivisa con il servizio Site Recovery?
No. L'identità del tenant rimane anonima. Per i tenant non è necessario l'accesso al portale di Site Recovery. Solo l'amministratore del provider di servizi interagisce con il portale.


### I dati delle applicazioni tenant saranno ricevuti da Azure?
Quando si esegue la replica tra i siti di proprietà del provider di servizi, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati in transito e replicati direttamente tra i siti del provider di servizi.

Se si esegue la replica in Azure, i dati dell'applicazione vengono inviati all'archiviazione di Azure, ma non al servizio Site Recovery. I dati vengono crittografati in transito e rimangono crittografati in Azure.


### I tenant riceveranno una fattura per ogni servizio Azure?

No. La fatturazione di Azure avviene direttamente con il provider di servizi. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### Se si esegue la replica in Azure, è sempre necessario eseguire le macchine virtuali in Azure?

No, i dati vengono replicati in un account di archiviazione di Azure nella sottoscrizione. Quando si esegue un failover di test (un'analisi del ripristino di emergenza) o un failover effettivo, Site Recovery crea automaticamente macchine virtuali nella sottoscrizione.

### È possibile garantire isolamento a livello di tenant durante la replica in Azure?

Sì.

### Quali piattaforme sono attualmente supportate?

Sono supportate distribuzioni basate su Azure Pack, Cloud Platform System e System Center (2012 e versioni successive). [Alter informazioni](https://technet.microsoft.com/library/dn850370.aspx) sull'integrazione di Azure Pack e Site Recovery.

### Sono supportate singole distribuzioni di Azure Pack e del server VMM?

Sì, è possibile eseguire la replica da macchine virtuali Hyper-V ad Azure o tra siti del provider di servizi. Si noti che se si esegue la replica tra i siti del provider di servizi, l'integrazione del runbook di Azure non è disponibile.


## Passaggi successivi

- Leggere la [panoramica di Site Recovery](site-recovery-overview.md)
- Informazioni sull'[architettura di Site Recovery](site-recovery-components.md)

 

<!---HONumber=AcomDC_0720_2016-->