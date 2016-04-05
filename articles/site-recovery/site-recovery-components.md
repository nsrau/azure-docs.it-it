<properties
	pageTitle="Funzionamento di Azure Site Recovery | Microsoft Azure"
	description="Questo articolo offre una panoramica dell'architettura di Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/27/2016"
	ms.author="raynew"/>

# Funzionamento di Azure Site Recovery

Leggere questo articolo per comprendere l'architettura sottostante del servizio Azure Site Recovery e i componenti necessari per il funzionamento.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Panoramica

Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono disponibili durante periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali nel cloud (Azure) o in un sito secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover al sito secondario per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località.

È possibile distribuire Site Recovery per orchestrare la replica in diversi scenari:

- **Replicare macchine virtuali VMware**: è possibile replicare le macchine virtuali VMware locali in [Azure](site-recovery-vmware-to-azure-classic.md) o in un [data center secondario](site-recovery-vmware-to-vmware.md).
- **Replicare computer fisici**: è possibile replicare i computer fisici che eseguono Windows o Linux in [Azure](site-recovery-vmware-to-azure-classic.md) o in un [data center secondario](site-recovery-vmware-to-vmware.md).
- **Replicare VM Hyper-V gestite in cloud VMM di System Center **: è possibile replicare le macchine virtuali Hyper-V locali in cloud VMM in [Azure](site-recovery-vmm-to-azure.md) o in un [data center secondario](site-recovery-vmm-to-vmm.md). 
- **Replicare VM Hyper-V (senza VMM)**: è possibile replicare le VM Hyper-V non gestite da VMM in [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Eseguire la migrazione di VM**: è possibile usare Site Recovery per eseguire la [migrazione delle VM IaaS di Azure](site-recovery-migrate-azure-to-azure.md) tra aree geografiche oppure per [eseguire la migrazione di istanze Windows di AWS](site-recovery-migrate-aws-to-azure.md) nelle VM IaaS di Azure. Attualmente è supportata solo la migrazione. Ciò significa che è possibile eseguire il failover di queste macchine virtuali, ma non il failback.

Site Recovery può replicare la maggior parte delle app in esecuzione su queste VM e questi server fisici. Per un riepilogo completo delle app supportate, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md)

## Replicare le macchine virtuali VMware locali o i server fisici in Azure

Sono attualmente disponibili due diverse architetture per la replica di macchine virtuali VMware o di server fisici Windows/Linux in Azure:

- [Architettura legacy](site-recovery-vmware-to-azure-classic-legacy.md): questa architettura non deve essere usata per nuove distribuzioni. 
- [Architettura avanzata ](site-recovery-vmware-to-azure-classic.md): questa è l'architettura più recente e deve essere usata per tutte le nuove distribuzioni. Se questo scenario è già stato distribuito con l'architettura legacy, vedere le [informazioni sulla migrazione](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) alla distribuzione avanzata.

Nella distribuzione avanzata è necessario configurare un server di gestione locale con tutti i componenti di Site Recovery. In ogni computer che si vuole proteggere effettuare automaticamente il push o installare manualmente il servizio Mobility. Dopo la replica iniziale il servizio Mobility nel computer invia dati di replica differenziale al server di elaborazione, che li ottimizza prima di inviarli all'archiviazione di Azure.

![Avanzato](./media/site-recovery-components/arch-enhanced.png) ![Avanzato](./media/site-recovery-components/arch-enhanced2.png)

### Locale
Ecco gli elementi richiesti in locale:

- **Server di gestione**: è necessario un computer Windows Server 2012 R2 configurato come server di gestione. In questo server si installeranno tutti i componenti di Site Recovery con un singolo file di installazione:

	- **Componente del server di configurazione**: per coordinare la comunicazione tra l'ambiente locale e Azure e gestire la replica e il ripristino dei dati.
	- **Componente del server di elaborazione**: agisce come un gateway di replica. Riceve i dati di replica dai computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle VM VMware. Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione dedicati e distinti per gestire i volumi più elevati di traffico di replica.
	- **Componente del server di destinazione master**: gestisce i dati di replica durante il failback da Azure. 
- **Server vCenter e host VMware ESX/ESXi**: sono necessari uno o più server host ESX/ESXi che eseguono VM VMware. È consigliabile distribuire un server vCenter per gestire questi host. **Nota:** **anche se si esegue la replica di server fisici, è necessario eseguirne il failback in VMware**. Quando si replica un server fisico, viene eseguito come una VM di Azure quando si esegue il failover in Azure. Verrà eseguito il failback in locale come una VM VMware. 
	
- **VM/server fisici**: in ogni computer che si vuole replicare in Azure dovrà essere installato il componente del servizio Mobility. Questo servizio acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione. Questo componente può essere installato manualmente oppure si può effettuarne il push e installarlo automaticamente tramite il server di elaborazione quando si abilita la replica per un computer.

### Azure

Ecco cosa occorre nell'infrastruttura di Azure:- **Account Azure**: è necessario un account Microsoft Azure. - **Archiviazione di Azure**: è necessario un account di archiviazione di Azure per archiviare i dati replicati. I dati replicati vengono archiviati in archiviazione di Azure e le VM di Azure vengono riattivate quando si verifica il failover. - **Rete azure**: è necessaria una rete virtuale di Azure alla quale le VM di Azure riattivate si connettono in caso di failover.
	
	
### Failback

Il failback in locale viene sempre eseguito nelle VM VMware, anche se è stato eseguito il failover in un server fisico. Sono necessari gli elementi seguenti:

- **Server di elaborazione temporaneo in Azure**: se si vuole eseguire il failback da Azure dopo il failover, è necessario impostare una VM di Azure configurata come server di elaborazione per gestire la replica da Azure. Questa VM può essere eliminata al termine del failback.
- **Connessione VPN**: per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.
- **Server di destinazione master locale distinto**: il server di destinazione master locale gestisce il failback. Il server di destinazione master viene installato per impostazione predefinita nel server di gestione, ma se si esegue il failback di volumi di traffico più grandi, è consigliabile configurare un server di destinazione master locale distinto a questo scopo. 

![Failback avanzato](./media/site-recovery-components/enhanced-failback.png)

[Altre informazioni](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sui requisiti di distribuzione avanzati. [Altre informazioni](site-recovery-failback-azure-to-vmware-classic.md) sul failback per la distribuzione avanzata.




## Replicare le VM Hyper-V nei cloud VMM in Azure.

Per distribuire questo scenario durante la distribuzione di Site Recovery, installare il provider di Azure Site Recovery nel server VMM. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. Durante la distribuzione di Site Recovery nel server host Hyper-V viene installato l'agente di Servizi di ripristino di Azure e i dati vengono replicati tra il server e Archiviazione di Azure tramite HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

- Locale: 
	- **Server VMM**: almeno un server VMM configurato con almeno un cloud privato VMM. Nel server deve essere in esecuzione System Center 2012 R2 e deve essere disponibile la connettività Internet. Se si vuole garantire che le macchine virtuali di Azure siano connesse a una rete dopo il failover, è necessario configurare il mapping della rete. A tale scopo le macchine virtuali di origine devono essere connesse a una rete VM di VMM. È necessario che tale rete VM sia collegata a una rete logica associata al cloud.
	- **Server Hyper-V**: almeno un server host Hyper-V disponibile nel cloud VMM. Negli host Hyper-V deve essere in esecuzione Windows Server 2012 R2.
	- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
- Azure:
	- **Account Azure**: sarà necessario un account Microsoft Azure.
	- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.
	- **Rete di Azure**: se si vuole configurare il mapping della rete per assicurare che le VM di Azure siano connesse alle reti dopo il failover, è necessario configurare una rete di Azure.

	![Da VMM ad Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Altre informazioni sui [requisiti di distribuzione](site-recovery-vmm-to-azure.md#before-you-start) esatti.

## Replicare i server fisici e le macchine virtuali VMware in un sito secondario

Per replicare le VM VMware o i server fisici Windows/Linux in un sito secondario, scaricare InMage Scout incluso nella sottoscrizione di Azure Site Recovery. Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare. Dopo la replica iniziale l'agente in ogni computer invia le modifiche della replica differenziale al server di elaborazione. Il server di elaborazione ottimizza i dati e li trasferisce al server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.

![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)

### Sito primario locale

- **Server di elaborazione**: configurare il componente del server di elaborazione nel sito primario per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati. Gestisce anche l'installazione push di Unified Agent nei computer da proteggere. 
- **VMware ESX/ESXi e Server vCenter**: se si prevede di proteggere macchine virtuali VMware, è necessario un hypervisor VMware EXS/ESXi e facoltativamente un server VMware vCenter per gestire gli hypervisor.
- **VM/server fisici**: nelle VM VMware o nei server fisici Windows/Linux da proteggere deve essere installato Unified Agent. Unified Agent viene installato anche nei computer configurati come server di destinazione master. L'agente funge da provider di comunicazioni tra tutti i componenti. 
	
### Sito secondario locale
 
- **Server di configurazione**: il server di configurazione è il primo componente da installare e viene installato nel sito secondario per gestire, configurare e monitorare la distribuzione mediante il sito Web di gestione o la console vContinuum. È presente un solo server di configurazione in una distribuzione e deve essere installato in un computer che esegue Windows Server 2012 R2.
- **Server vContinuum**: è installato nella stessa posizione (sito secondario) del server di configurazione. Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto. In un'installazione predefinita il server vContinuum è il primo server di destinazione master e include l'installazione di Unified Agent.
- **Server di destinazione master**: questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati. Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere. Se si vuole eseguire il failback al sito primario, dovrà essere disponibile anche un server di destinazione master. 

### Azure

Questo scenario viene distribuito con InMage Scout. Per ottenerlo, è necessario avere una sottoscrizione di Azure. Dopo aver creato un insieme di credenziali di Site Recovery, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.


## Replicare VM Hyper-V in Azure (nessuna VMM)

Per replicare in Azure le VM Hyper-V non gestite nei cloud VMM, installare il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure nell'host Hyper-V durante la distribuzione di Site Recovery. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. L'agente gestisce i dati di replica tramite HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

![Da sito Hyper-V ad Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### Locale

- **Server Hyper-V**: almeno un server host Hyper-V. Negli host Hyper-V deve essere in esecuzione Windows Server 2012 R2.
- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
### Azure

- **Account Azure**: sarà necessario un account Microsoft Azure.
- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.

[Altre informazioni](site-recovery-hyper-v-site-to-azure.md#before-you-start) sui requisiti di distribuzione.


## Replicare le VM Hyper-V nei cloud VMM in Azure.

Per distribuire questo scenario, durante la distribuzione di Site Recovery installare il provider di Azure Site Recovery nel server VMM e l'agente di Servizi di ripristino di Azure nell'host Hyper-V. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. L'agente gestisce i dati di replica tramite HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure (inattivi) vengono anche crittografati.

![Da VMM ad Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### Locale

- **Server VMM**: almeno un server VMM configurato con almeno un cloud privato VMM. Nel server deve essere in esecuzione System Center 2012 R2 e deve essere disponibile la connettività Internet. Se si vuole garantire che le macchine virtuali di Azure siano connesse a una rete dopo il failover, è necessario configurare il mapping della rete. A questo scopo si devono connettere le VM di origine a una rete di VM VMM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
- **Server Hyper-V**: almeno un server host Hyper-V disponibile nel cloud VMM. Negli host Hyper-V deve essere in esecuzione Windows Server 2012 R2.
- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
### Azure

- **Account Azure**: sarà necessario un account Microsoft Azure.
- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.
- **Rete di Azure**: se si vuole garantire che le VM di Azure siano connesse alle reti dopo il failover, è necessario configurare il mapping della rete. A questo scopo è necessaria una rete di Azure.

[Altre informazioni](site-recovery-vmm-to-azure.md#before-you-start) sui requisiti di distribuzione.

## Replica di VM Hyper-V in un data center secondario

Per distribuire questo scenario, durante la distribuzione di Site Recovery, installare il provider di Azure Site Recovery nel server VMM. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra il server host Hyper-V primario e secondario sulla LAN o la VPN usando l'autenticazione Kerberos o del certificato. Le comunicazioni dal provider e tra i server host Hyper-V sono protette e crittografate.

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

### Locale

- **Server VMM**: è consigliabile avere un server VMM nel sito primario e uno nel sito secondario, ognuno dei quali contiene almeno un cloud privato VMM. Il server deve eseguire almeno System Center 2012 SP1 con gli ultimi aggiornamenti cumulativi ed essere connesso a Internet. I cloud devono avere impostato il profilo funzionalità di Hyper-V.
- **Server Hyper-V**: i server host Hyper-V devono trovarsi nei cloud VMM primario e secondario. I server host devono eseguire almeno Windows Server 2012 con gli aggiornamenti più recenti installati ed essere connessi a Internet.
- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
### Azure

È necessaria una sottoscrizione di Azure.

[Altre informazioni](site-recovery-vmm-to-vmm.md#before-you-start) sui requisiti di distribuzione.


## Replica di VM Hyper-V in un data center secondario mediante la replica SAN

In questo scenario durante la distribuzione di Site Recovery installare il provider di Azure Site Recovery nei server VMM. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra le matrici di archiviazione primaria e secondaria mediante la replica SAN sincrona.

![Replica SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

### Locale

- **Matrice SAN**: una [matrice SAN supportata](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gestita dal server VMM primario. La rete SAN condivide un'infrastruttura di rete con un altro array SAN nel sito secondario.
- **Server VMM**: è consigliabile avere un server VMM nel sito primario e uno nel sito secondario, ognuno dei quali contiene almeno un cloud privato VMM. Il server deve eseguire almeno System Center 2012 SP1 con gli ultimi aggiornamenti cumulativi ed essere connesso a Internet. I cloud devono avere impostato il profilo funzionalità di Hyper-V.
- **Server Hyper-V**: server host Hyper-V presenti nei cloud VMM primario e secondario. I server host devono eseguire almeno Windows Server 2012 con gli aggiornamenti più recenti installati ed essere connessi a Internet.
- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
### Azure

È necessaria una sottoscrizione di Azure.

[Altre informazioni](site-recovery-vmm-san.md#before-you-start) sui requisiti di distribuzione.


## Ciclo di vita di protezione di Hyper-V

Questo flusso di lavoro mostra il processo di protezione, replica e failover delle macchine virtuali Hyper-V.

1. **Abilitare la protezione**: configurare l'insieme di credenziali di Site Recovery, configurare le impostazioni di replica per un cloud VMM o un sito Hyper-V e abilitare la protezione per le VM. Un processo denominato **Abilita protezione** viene avviato e può essere monitorato nella scheda **Processi**. Il processo verifica che il computer sia conforme ai prerequisiti e quindi richiama il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), che configura la replica in Azure con le impostazioni configurate. Il processo **Abilita protezione** richiama anche il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) per inizializzare una replica completa della VM.
2. **Replica iniziale**: viene creato uno snapshot macchina virtuale e i dischi rigidi virtuali vengono replicati uno alla volta, fino a quando non sono stati tutti copiati in Azure o nel data center secondario. Il tempo necessario per completare l'operazione dipende dalle dimensioni della VM, dalla larghezza di banda della rete e dal metodo di replica iniziale. Se vengono apportate modifiche al disco mentre è in corso la replica iniziale, Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (con estensione hrl), che si trovano nella stessa cartella dei dischi. A ogni disco è associato un file con estensione hrl, che verrà inviato alla risorsa di archiviazione secondaria. Si noti che lo snapshot e i file di log utilizzano risorse del disco durante l'esecuzione della replica iniziale. Al termine della replica iniziale, lo snapshot della VM viene eliminato e le modifiche differenziali al disco nel log vengono sincronizzate e unite.
3. **Finalizzare la protezione**: al termine della replica iniziale, il processo di ** finalizzazione della protezione** configura la rete e altre impostazioni successive alla replica, così che la macchina virtuale risulti protetta. Se si esegue la replica in Azure, potrebbe essere necessario modificare le impostazioni della macchina virtuale, in modo che sia pronta per il failover. A questo punto è possibile eseguire un failover di test per controllare che tutto funzioni come previsto.
4. **Replica**: dopo la replica differenziale iniziale inizia la sincronizzazione, in base alle impostazioni della replica. 
	- **Errore di replica**: se la replica differenziale non riesce e una replica completa risulta costosa a livello di larghezza di banda o del tempo richiesto, verrà eseguita la risincronizzazione. Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la VM verrà contrassegnata per la risincronizzazione. La risincronizzazione riduce al minimo la quantità di dati inviati calcolando i checksum delle macchine virtuali di origine e di destinazione e inviando solo il relativo differenziale. Al termine della risincronizzazione, riprendere la replica differenziale. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica negli orari non lavorativi, ma è possibile risincronizzare manualmente una macchina virtuale.
	- **Errore di replica**: se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. Se si tratta di un errore irreversibile, ad esempio un errore di autenticazione o di autorizzazione oppure se lo stato del computer di replica non è valido, non verranno effettuati nuovi tentativi. Se si tratta di un errore reversibile, ad esempio un errore di rete o spazio su disco o memoria insufficiente, verranno eseguiti nuovi tentativi a intervalli crescenti (ogni 1, 2, 4, 8, 10 e quindi 30 minuti).
4. **Failover pianificati/non pianificati**: è possibile eseguire i failover pianificati/non pianificati secondo le esigenze. Se si esegue un failover pianificato, le VM di origine vengono arrestate per assicurare che non si verifichino perdite di dati. Dopo la creazione, le VM di replica sono in uno stato di commit in sospeso. È necessario eseguirne il commit per completare il failover, a meno che non si stia eseguendo la replica con SAN. In questo caso, il commit è automatico. Quando il sito primario è attivo e in esecuzione, è possibile eseguire il failback. Se è stata eseguita la replica in Azure, la replica inversa sarà automatica. In caso contrario, è possibile attivare una replica inversa manualmente.
 

![flusso di lavoro](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Passaggi successivi

[Preparare la distribuzione](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0330_2016-->