<properties 
	pageTitle="Azure Site Recovery: domande frequenti"
	description="Questo articolo illustra le domande frequenti sull'utilizzo di Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="08/26/2015"
	ms.author="lauraa"/>


# Azure Site Recovery: domande frequenti
## Informazioni sull’articolo

In questo articolo sono riportate le domande frequenti su Azure Site Recovery. Per un'introduzione a Site Recovery e agli scenari di distribuzione correlati, leggere [Panoramica di Site Recovery](site-recovery-overview.md).

Dopo la lettura di questo articolo, è possibile pubblicare eventuali dubbi e domande nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Generale
### ASR è sicuro? Quali dati si inviano ad Azure?

Sì, ASR è sicuro. Non intercetta i dati dell'applicazione né dispone di informazioni su ciò che viene eseguito all'interno delle macchine virtuali. Non è necessaria alcuna connessione a Internet dagli host Hyper-V o dalle macchine virtuali.

Poiché la replica avviene tra il sito dell’azienda e i siti dei provider di servizi, ad Azure non vengono inviati dati relativi alle applicazioni. Solo i metadati, ad esempio nomi di macchine virtuali o cloud, necessari per orchestrare il failover, vengono inviati ad Azure. ASR non può intercettare i dati delle applicazioni, che rimangono sempre in locale.

ASR è certificato ISO 27001:2005 e sono in fase di completamento le valutazioni HIPAA, DPA e FedRAMP JAB.

### I requisiti di conformità richiedono che anche i metadati degli ambienti locali restino all'interno della stessa area geografica. ASR può garantire che tale requisito sia soddisfatto?

Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area di propria scelta, tutti i metadati necessari per abilitare e orchestrare la configurazione del ripristino di emergenza restano all'interno dei confini di tale area geografica.

### È disponibile un SDK che consenta di automatizzare il flusso di lavoro ASR?

Sì. I flussi di lavoro ASR possono essere automatizzati tramite API Rest, PowerShell o Azure SDK. È possibile trovare ulteriori dettagli nel post del blog [Introduzione a PowerShell per Azure Site Recovery](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/).

### Il ripristino automatico di sistema crittografa la replica? 
La replica da siti locali ad Azure e la replica tra siti locali supporta la crittografia in transito per *gli scenari di protezione di Hyper-V e VMM*. La *protezione Hyper-V e VMM* per Azure supporta anche la crittografia in locale. Per altre informazioni, fare riferimento a [questo articolo](https://azure.microsoft.com/blog/2014/09/02/azure-site-recovery-privacy-security-part1/).

### È possibile aumentare la frequenza di replica o copia di oltre 15 minuti?
* **Scenari Hyper-V e VMM**: no, la replica delle macchine virtuali Hyper-V usando la replica basata su host può essere configurata solo per 30 secondi, 5 minuti e 15 min.
* **Scenario VMware/fisico**: non è applicabile per la replica in-guest in quanto la tecnologia usa la protezione dei dati continua.

### È possibile escludere dischi specifici dalla replica mediante il ripristino automatico di sistema?
Questa funzione non è supportata. Inviare commenti e suggerimenti tramite il [forum dei commenti di Azure Site Recovery - Escludere un disco dalla replica](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication).

### È possibile replicare macchine virtuali basate su dischi dinamici?
Gli *scenari Hyper-V e VMM* supportano i dischi dinamici. *Gli scenari con macchine virtuali VMware o macchine fisiche* non supportano i dischi dinamici. Inviare commenti e suggerimenti tramite il [forum dei commenti di Azure Site Recovery](http://feedback.azure.com/forums/256299-site-recovery).

### Quali tipi di account di archiviazione sono supportati?
È supportata l'[archiviazione con ridondanza geografica standard](../storage/storage-redundancy.md#geo-redundant-storage). L'[account di archiviazione Premium] (../storage/storage-premium-storage-preview-portal/) è supportato solo per [scenari con macchine virtuali VMware o macchine fisiche](site-recovery-vmware-to-azure.md). Il supporto per l'archiviazione con ridondanza locale standard è in backlog, inviare commenti e suggerimenti tramite [il supporto per l'archiviazione con ridondanza locale](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support).

### È possibile estendere la replica dal sito di ripristino esistente a un sito terziario?
Questa funzione non è supportata. Inviare commenti e suggerimenti tramite il [forum dei commenti di Azure Site Recovery - Supporto per l'estensione della replica](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### È possibile eseguire il seeding dei dischi iniziali in Azure tramite un meccanismo offline?
Questa funzione non è supportata. Inviare commenti e suggerimenti tramite il [forum dei commenti di Azure Site Recovery - Supporto per la replica offline](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

## Supporto della versione

### Quali versioni di cluster e host di Windows Server sono supportate?
Windows Server 2012 e Windows Server 2012 R2 possono essere utilizzati quando si sceglie Replica Hyper-V per abilitare la replica e la protezione tra siti Hyper-V.


### Quali versioni di sistemi operativi guest Hyper-V sono supportate?
L'elenco più recente dei sistemi operativi guest supportati è disponibile nell'argomento [Informazioni sulle macchine virtuali e sui sistemi operativi guest](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### È possibile configurare la protezione della macchina virtuale quando Hyper-V viene eseguito in un sistema operativo client?

Non è possibile configurare l'esecuzione di Hyper-V in un sistema operativo client di replicare macchine virtuali in Azure. La replica Hyper-V è supportata solo nei sistemi operativi server. È possibile replicare i computer client fisici in Azure usando [in questo articolo](site-recovery-vmware-to-azure.md).

### ASR supporta le macchine virtuali di seconda generazione?

Sì, il ripristino automatico di sistema supporta la replica delle macchine virtuali di seconda generazione su Hyper-V in Azure. ASR esegue la conversione dalla seconda generazione alla prima durante il failover. In caso di failback, la macchina virtuale viene riconvertita nella prima generazione. Per altre informazioni, [leggere qui](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


## Distribuire tra siti di provider di servizi 

### Questa soluzione funziona per modelli di infrastruttura dedicati o condivisi?
Sì, ASR supporta sia il modello di infrastruttura dedicato che quello condiviso.

### L'identità del mio tenant è condiviso con Azure?
No. Per i tenant non è necessario l'accesso al portale di ASR. Solo l'amministratore del provider di servizi esegue azioni nel portale di ASR in Azure.

### I tenant riceveranno una fattura da Azure?
No. La fatturazione di Microsoft Azure avviene direttamente con il provider del servizio. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### I dati delle applicazioni tenant saranno ricevuti da Azure?
Quando si utilizza un sito di proprietà del provider di servizi per il ripristino di emergenza, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati e replicati direttamente tra i siti del provider di servizi. Quando si utilizza Azure come sito di ripristino di emergenza, i dati delle applicazioni vengono inviati ad Azure: i dati a riposo e i dati in transito restano crittografati. È anche possibile utilizzare Internet pubblica, ExpressRoute o VPN per stabilire la connessione ad Azure.

### Quando si utilizza Azure come sito di ripristino di emergenza, è sempre necessario eseguire le macchine virtuali in Azure?
No, ASR è progettato come soluzione DRaaS di cloud pubblico di prima classe. I dati vengono replicati in un account di archiviazione di Azure con ridondanza geografica nella propria sottoscrizione. Quando si esegue un’analisi del ripristino di emergenza o un failover effettivo, ASR crea automaticamente macchine virtuali nella sottoscrizione.

### È possibile garantire isolamento a livello di tenant quando si sceglie di utilizzare Azure come un sito di ripristino di emergenza?
Sì.

### Quali piattaforme sono attualmente supportate?
Sono supportate distribuzioni di Hyer 2012 o versioni successive basate su System Center, Cloud Platform System e Azure Pack. Per ulteriori informazioni sull'integrazione di ASR e Azure Pack, vedere [Configurare la protezione per le macchine virtuali](https://technet.microsoft.com/library/dn850370.aspx).

### Sono supportate anche singole distribuzioni di Azure Pack e del server VMM?
Sì, sono supportate singole distribuzioni di SCVMM per entrambi gli scenari: ripristino di emergenza tra siti del provider di servizi e ripristino di emergenza tra il sito del provider di servizi e Azure.

La singola distribuzione di Azure Pack è supportata anche quando si abilita il ripristino di emergenza tra siti del provider di servizi, tuttavia, per questa topologia non è disponibile l’integrazione ASR Runbook.

## Distribuire tra siti Hyper-V e Azure (senza VMM)

### ASR richiede l’abilitazione della VPN tra siti?
No, non è obbligatoria. ASR funziona anche tramite Internet pubblica. Tuttavia, se è stata configurata la VPN da sito a sito, sarà possibile accedere alle macchine virtuali di failover nello stesso modo di prima. Per ulteriori informazioni sulle considerazioni di rete quando si abilita il ripristino di emergenza in Azure, vedere il [blog Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site ](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/).

### È previsto un costo oltre l'addebito per la protezione di ASR?
Nello stato stazionario, le modifiche vengono replicate in archiviazione di Azure con ridondanza geografica e non è necessario pagare costi di macchine virtuali IaaS di Azure (un grande vantaggio). Quando si esegue un failover in Azure, ASR crea automaticamente le macchine virtuali IaaS di Azure, dopodiché vengono fatturate le risorse di calcolo utilizzate in Azure.

### In una filiale non è installato VMM. È possibile abilitare la protezione dei carichi di lavoro nella filiale in Azure? Quali sono i principali vantaggi dell'utilizzo di ASR per eseguire questa operazione?
Sì, è possibile utilizzare ASR per proteggere le macchine virtuali in esecuzione negli host Hyper-V non gestiti da SCVMM.

Quando si utilizza ASR per gestire le esigenze di ripristino di emergenza di tutte le filiali, si ottiene una visualizzazione unificata dei carichi di lavoro di tutte le filiali in una posizione centrale. È inoltre possibile ottenere con un solo clic il failover e l’amministrazione del ripristino di emergenza di tutte le filiali dalla sede centrale, senza mai passare a tale filiale. Attualmente, la rete di mapping e la crittografia a riposo non sono supportate per il ripristino di emergenza delle filiali.

### Per tali sedi, esiste un limite al numero di macchine virtuali che è possibile proteggere?
No. Il supporto rimane invariato, come accade dello scenario aziendale.

### È necessario installare un agente nelle macchine virtuali da proteggere?

Non è necessario un agente nelle macchine virtuali. È necessario installare il provider di Site Recovery e l'agente di Servizi di ripristino in ogni server Hyper-V che esegue macchine virtuali da proteggere. Questi due componenti vengono installati con un singolo file del provider che può essere scaricato durante la distribuzione di Site Recovery.

### Per il provider in esecuzione sul server Hyper-V sono necessarie impostazioni del firewall speciali?

Non sono necessarie impostazioni specifiche. I componenti del provider nel server di comunicano tramite una connessione HTTPS con Azure e usano l'impostazione Internet predefinita sul server host Hyper-V, se non diversamente specificato.

### Il server Hyper-V deve essere un membro del dominio?

Nessun server deve trovarsi in un dominio.

### Quali versioni di cluster e host di Windows Server sono supportate?
Windows Server 2012 e Windows Server 2012 R2 possono essere usati quando si implementano ASR e la replica Hyper-V tra i siti Hyper-V e Azure.

### Quali versioni di sistemi operativi guest sono supportate?
L'elenco più recente dei sistemi operativi guest supportati è disponibile nell'articolo [Informazioni sulle macchine virtuali e sui sistemi operativi guest](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

## Distribuire tra due data center VMM

### È possibile replicare una macchina virtuale che è stata replicata in un sito secondario in Azure? 

No, questo tipo di replica concatenata non è supportato.

### Sono necessari certificati per configurare la protezione tra due data center VMM?

No. Specificare il tipo di autenticazione durante la configurazione della protezione tra cloud VMM nel ripristino automatico di sistema. Selezionare HTTPS a meno che sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Se si seleziona Kerberos, verrà utilizzato un ticket Kerberos per l'autenticazione reciproca dei server host. Per impostazione predefinita, le porte 8083 (per Kerberos) e 8084 (per i certificati) sono aperte in Windows Firewall per i server host Hyper-V. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.



## Distribuire tra due data center VMM con SAN

### Se si dispone già di una replica basata su SAN configurata e non si desidera modificarla?
Nessun problema. ASR supporta lo scenario in cui la replica potrebbe già essere configurata. In questo caso, è possibile utilizzare ASR per l'orchestrazione del ripristino di emergenza basato su macchina virtuale, la configurazione della rete del ripristino di emergenza, i piani di ripristino compatibili con le applicazioni, le analisi del ripristino di emergenza e i requisiti dei report di conformità e controllo.


### È necessario VMM? È necessario VMM su entrambi i lati?
Sì. È necessario che l'array SAN sia gestito da VMM tramite un provider SMI-S specifico dell'array.

Sono supportate le distribuzioni a disponibilità elevata di VMM in base al tipo di array, anche se la configurazione consigliata prevede l'uso di server VMM separati per la gestione dei siti.


### Quali sono gli array di archiviazione supportati?

NetApp, EMC e HP hanno attivato il supporto per la replica SAN di Azure Site Recovery con aggiornamenti per i propri provider SMI-S. Per altre informazioni, dettagliate vedere i collegamenti seguenti.

- [NetApp Clustered Data ONTAP 8.2](http://community.netapp.com/t5/Technology/NetApp-Unveils-Support-for-Microsoft-Azure-SAN-Replication-with-SMI-S-and/ba-p/94483)
- [Serie VMAX EMC](https://thecoreblog.emc.com/high-end-storage/microsoft-azure-site-recovery-now-generally-available-vmax-srdf-integration-pack-ready-for-public-review/)    
- [HP 3PAR](http://h20195.www2.hp.com/V2/GetDocument.aspx?docname=4AA5-7068ENW&cc=us&lc=en)


### Cosa accade in caso di dubbi sull’amministrazione dell’archiviazione?
Lavoriamo con la replica esistente configurata dall'amministratore di archiviazione, il che significa che l'amministratore di archiviazione non ha bisogno di apportare modifiche negli array. Tuttavia, le organizzazioni che desiderano automatizzare la gestione dell'archiviazione tramite SCVMM possono anche eseguire il provisioning dell'archiviazione usando ASR e VMM.

### È possibile supportare la replica sincrona? I cluster guest? L’archiviazione condivisa?
Sì, Sì e Sì.

### Da rete a rete: cosa è necessario installare in locale perché questa soluzione funzioni?
Quando si utilizza la replica basata su array (SAN) per attivare la replica e la protezione tra siti Hyper-V, è sufficiente che il provider del ripristino di emergenza di ASR sia installato nei server SCVMM. Tenere presente che questo è anche l’*unico* host che richiede la connettività a Internet.


Gli array devono essere individuati da SCVMM tramite un provider SMI-S aggiornato reso disponibile dai fornitori delle risorse di archiviazione.

## Distribuire tra server fisici e Azure

### È possibile proteggere un server fisico locale in Azure?

Sì, si può replicare un server fisico mediante ASR ed eseguirlo come macchina virtuale in Azure, in caso di inattività del server locale. Il failback a un server fisico locale non è attualmente supportato. È possibile eseguire il failback a una macchina virtuale eseguita in Hyper-V o VMware.


## Failover

### Cosa accade se Azure è inattivo? È possibile attivare un failover da VMM?

Anche se Azure è progettato per la resilienza del servizio, sono state previste le situazioni peggiori. ASR è già progettato per il failover a un centro dati secondario di Azure in conformità al contratto di servizio di Azure. Anche se in questo caso, i metadati e l’insieme di credenziali ASR restano all'interno della stessa area geografica scelta.


### Cosa accade se l'ISP per il centro dati primario subisce un'interruzione durante un'emergenza? Cosa accade se l'ISP per il centro dati secondario subisce un'interruzione durante un'emergenza?

È possibile utilizzare il portale di ASR per eseguire un failover non pianificato con un solo clic. Per eseguire il failover non è necessaria la connettività dal centro dati primario.

È probabile che le applicazioni che devono essere in esecuzione nel centro dati secondario dopo un failover richiederanno una connessione a Internet. ASR presume che anche in caso di ripercussioni sul sito primario e sull’ISP in una situazione di emergenza, il server SCVMM del sito secondario è ancora connesso ad ASR.

### ASR supporta anche il failback?
Sì. Il failback da Azure al sito locale è semplice con il processo con un solo clic tramite il portale di ASR.

### È possibile il failover automatico?

Non esiste un'opzione per il failover automatico. Per attivare il failover, è possibile scegliere un singolo pulsante nel portale per attivare un failover oppure usare i [cmdlet di PowerShell per Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx). È possibile usare Orchestrator o Operations Manager in locale per controllare un errore della macchina virtuale e quindi decidere di attivare il failover tramite l'SDK. In questo modo il processo diventa automatico.




## Monitoraggio

### Quali soluzioni sono fornite per monitorare installazione e replica in corso?
È possibile sottoscrivere avvisi di posta elettronica direttamente dal servizio ASR per essere informati di eventuali problemi che richiedono attenzione. È inoltre possibile monitorare l'integrità della replica in corso mediante SCOM.

## Passaggi successivi

Per avviare la distribuzione  

- [Configurare la protezione tra un sito VMM locale e Azure](site-recovery-vmm-to-azure.md) 
- [Configurare la protezione tra un sito Hyper-V locale e Azure](site-recovery-hyper-v-site-to-azure.md) 
- [Configurare la protezione tra due siti VMM locali](site-recovery-vmm-to-vmm.md) 
- [Configurare la protezione tra due siti VMM locali con SAN](site-recovery-vmm-san.md) 
- [Configurare la protezione con un singolo server VMM](site-recovery-single-vmm.md) 

 

<!---HONumber=September15_HO1-->