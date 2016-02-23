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
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Funzionamento di Azure Site Recovery

Questo articolo illustra l'architettura sottostante di Site Recovery e i componenti necessari per il funzionamento di questo servizio. Dopo avere letto questo articolo, sarà possibile inserire domande nel [Forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono in esecuzione e disponibili durante i periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile. La strategia BCDR è incentrata sulle soluzioni che garantiscono la sicurezza e la possibilità di recuperare i dati aziendali e di mantenere i carichi di lavoro continuamente disponibili in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover al sito secondario per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località.

È possibile usare Site Recovery in diversi scenari per proteggere più carichi di lavoro.

- **Proteggere macchine virtuali VMware**: è possibile proteggere le macchine virtuali VMware locali mediante la relativa replica in [Azure](site-recovery-vmware-to-azure-classic.md) o in un [data center secondario](site-recovery-vmware-to-vmware.md).
- **Proteggere macchine virtuali Hyper-V**: possibile proteggere le macchine virtuali Hyper-V locali in cloud VMM mediante la relativa replica in [Azure](site-recovery-vmm-to-azure.md) o in un [data center secondario](site-recovery-vmm-to-vmm.md). È possibile replicare le macchine virtuali Hyper-V non gestite da VMM in [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Proteggere i server fisici in Azure**: è possibile proteggere i computer fisici che eseguono Windows o Linux mediante la relativa replica in [Azure](site-recovery-vmware-to-azure-classic.md) o in un [data center secondario](site-recovery-vmware-to-vmware.md).
- **Eseguire la migrazione delle VM**: è possibile usare Site Recovery per eseguire la [migrazione delle VM IaaS di Azure](site-recovery-migrate-azure-to-azure.md) tra aree geografiche oppure per [eseguire la migrazione di istanze Windows di AWS](site-recovery-migrate-aws-to-azure.md) nelle VM IaaS di Azure.

Site Recovery può replicare la maggior parte delle app in esecuzione su queste VM e questi server fisici. Per un riepilogo completo delle app supportate, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md)


## Replicare le macchine virtuali VMware locali o i server fisici in Azure

Per proteggere VM VMware o computer fisici Windows/Linux mediante la replica in Azure, sono necessari gli elementi seguenti.

Per questo scenario di replica sono attualmente disponibili due diverse architetture:

- **Architettura legacy**: questa architettura non deve essere usata per nuove distribuzioni. 
- **Avanzata architettura**: questa è la soluzione più recente e deve essere usata per tutte le nuove distribuzioni. È anche possibile [eseguire la migrazione dell'architettura legacy](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) a questa nuova soluzione.

Ecco l'architettura per la distribuzione avanzata

![Avanzato](./media/site-recovery-components/arch-enhanced.png)

- **Locale**: quando si distribuisce l'architettura avanzata non è necessario distribuire le VM dell'infrastruttura in Azure. Tutto il traffico viene anche crittografato e le comunicazioni relative alla gestione della replica vengono inviate tramite HTTPS 443. Ecco cosa occorre nell'infrastruttura locale:

	- **Server di gestione**: un singolo server di gestione che esegue tutti i componenti di Site Recovery, tra cui:

		- **Server di configurazione**: per coordinare la comunicazione tra l'ambiente locale e Azure e gestire i processi di replica e il ripristino dei dati.
		- **Server di elaborazione**: agisce come un gateway di replica. Riceve i dati da computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e invia i dati di replica all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle macchine virtuali VMware. Man mano che la distribuzione cresce, è possibile aggiungere altri server dedicati che vengono eseguiti come server di elaborazione solo per gestire i volumi più elevati di traffico di replica.
		- **Server di destinazione master**: gestisce i dati di replica durante il failback da Azure. 

	- **Server vCenter e host VMware ESX/ESXi**: uno o più server host ESX/ESXi in cui si trovano le macchine virtuali VMware. È consigliabile avere un server vCenter per gestire questi host. Si noti che anche se si stanno proteggendo server fisici, è necessario un ambiente VMware per eseguire il failback da Azure al sito locale.
	
	- **Computer protetti**: in ogni computer che si vuole replicare in Azure dovrà essere installato il componente del servizio Mobility. Acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione. Questo componente può essere installato manualmente oppure si può effettuarne il push e installarlo automaticamente tramite il server di elaborazione quando si abilita la protezione per un computer.

- **Azure**: ecco cosa occorre nell'infrastruttura di Azure:
	- **Account Azure**: è necessario un account Microsoft Azure.
	- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover. 
	- **Rete di Azure**: è necessaria una rete virtuale di Azure a cui le VM di Azure possano connettersi quando si verifica il failover. È anche necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.

	![Avanzato](./media/site-recovery-components/arch-enhanced2.png)

Altre informazioni sui [requisiti di distribuzione](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) esatti.

### Architettura di failback

- È necessario eseguire il failback da Azure alle VM VMware. Attualmente non è possibile eseguire il failback a un server fisico.
- Per eseguire il failback è necessaria una connessione VPN, oppure Azure ExpressRoute, dalla rete di Azure alla rete locale.
- Per il failback è necessario un server di elaborazione in Azure, che può essere eliminato al termine del failback.
- È necessario un server di destinazione master locale. Un server di destinazione master viene installato per impostazione predefinita nel server di gestione quando lo si configura in locale. Per volumi di traffico più elevati è tuttavia consigliabile configurare un server di destinazione master locale separato per il failback.

![Failback avanzato](./media/site-recovery-components/enhanced-failback.png)

Altre informazioni sul [failback](site-recovery-failback-azure-to-vmware-classic.md).

### Architettura legacy

L'architettura legacy richiede un server di configurazione e un server di elaborazione locale, host VMware ESX/ESXi e un server vCenter, oltre al servizio Mobility installato nei computer da proteggere. In Azure si configurano le VM di Azure per il server di configurazione e il server di destinazione master. Sono necessari anche una sottoscrizione di Azure, un account di archiviazione e una rete virtuale.



## Replicare le VM Hyper-V nei cloud VMM in Azure.

Se le VM si trovano in un host Hyper-V gestito in un cloud System Center VMM, per eseguirne la replica in Azure sono necessari gli elementi seguenti.

- Locale: 
	- **Server VMM**: almeno un server VMM configurato con almeno un cloud privato VMM. Il server deve essere eseguito in System Center 2012 R2. Il server VMM deve avere la connettività Internet. Se si vuole garantire che le macchine virtuali di Azure siano connesse a una rete dopo il failover, è necessario configurare il mapping della rete. A questo scopo si devono connettere le VM di origine a una rete di VM VMM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
	- **Server Hyper-V**: almeno un server host Hyper-V disponibile nel cloud VMM. Negli host Hyper-V deve essere in esecuzione Windows Server 2012 R2.
	- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
- Azure:
	- **Account Azure**: è necessario un account Microsoft Azure.
	- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.
	- **Rete di Azure**: se si vuole garantire che le macchine virtuali di Azure siano connesse alle reti dopo il failover, è necessario configurare il mapping della rete. A questo scopo è necessaria la configurazione di una rete di Azure.

	![Da VMM ad Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

In questo scenario il provider di Azure Site Recovery viene installato nel server VMM durante la distribuzione di Site Recovery. Coordina e orchestra la replica con il servizio Site Recovery su Internet. Durante la distribuzione di Site Recovery nel server host Hyper-V viene installato l'agente di Servizi di ripristino di Azure e i dati vengono replicati tra il server e Archiviazione di Azure tramite HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

Altre informazioni sui [requisiti di distribuzione](site-recovery-vmm-to-azure.md#before-you-start) esatti.

## Replicare VM Hyper-V in Azure (nessuna VMM)

Se le VM non sono gestite da un server System Center VMM, per replicarle in Azure sono necessari gli elementi seguenti.

- Locale: 
	- **Server Hyper-V**: almeno un server host Hyper-V. Negli host Hyper-V deve essere in esecuzione Windows Server 2012 R2.
	- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
- Azure:
	- **Account Azure**: è necessario un account Microsoft Azure.
	- **Archiviazione di Azure**: per archiviare i dati replicati è necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.

	![Da sito Hyper-V ad Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

In questo scenario il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure vengono installati nel server VMM durante la distribuzione di Site Recovery. Il provider coordina e orchestra la replica con il servizio Site Recovery su Internet. L'agente gestisce i dati di replica tramite HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

Altre informazioni sui [requisiti di distribuzione](site-recovery-hyper-v-site-to-azure.md#before-you-start) esatti.

## Replica di VM Hyper-V in un data center secondario

Per proteggere le VM Hyper-V replicandole in un data center secondario, ecco cosa occorre. Si noti che è possibile eseguire questa operazione solo se il server host Hyper-V è gestito in un cloud System Center VMM.

- **Locale**: 
	- **Server VMM**: è consigliabile un server VMM nel sito primario e uno nel sito secondario, ognuno dei quali con almeno un cloud privato VMM. Il server deve eseguire almeno System Center 2012 SP1 con gli aggiornamenti più recenti ed essere connesso a Internet. I cloud devono avere impostato il profilo funzionalità di Hyper-V.
	- **Server Hyper-V**: server host Hyper-V presenti nei cloud VMM primario e secondario. I server host devono eseguire almeno Windows Server 2012 con gli aggiornamenti più recenti installati ed essere connessi a Internet.
	- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
- **Azure**: è necessaria una sottoscrizione di Azure.

	![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

In questo scenario il provider di Azure Site Recovery viene installato nel server VMM durante la distribuzione di Site Recovery. Coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra il server host Hyper-V primario e secondario sulla LAN o la VPN usando l'autenticazione Kerberos o del certificato. Le comunicazioni dal provider e tra i server host Hyper-V sono protette e crittografate.

Altre informazioni sui [requisiti di distribuzione](site-recovery-vmm-to-vmm.md#before-you-start) esatti.



## Replica di VM Hyper-V in un data center secondario mediante la replica SAN

Se le VM si trovano in un host Hyper-V gestito in un cloud System Center VMM e si usa l'archiviazione SAN, per eseguirne la replica tra due data center sono necessari gli elementi seguenti.

- **Locale**: 
	- **Matrice SAN**: una [matrice SAN supportata](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gestita dal server VMM primario. La rete SAN condivide un'infrastruttura di rete con un altro array SAN nel sito secondario.
	- **Server VMM**: è consigliabile un server VMM nel sito primario e uno nel sito secondario, ognuno dei quali con almeno un cloud privato VMM. Il server deve eseguire almeno System Center 2012 SP1 con gli aggiornamenti più recenti ed essere connesso a Internet. I cloud devono avere impostato il profilo funzionalità di Hyper-V.
	- **Server Hyper-V**: server host Hyper-V presenti nei cloud VMM primario e secondario. I server host devono eseguire almeno Windows Server 2012 con gli aggiornamenti più recenti installati ed essere connessi a Internet.
	- **Computer protetti**: il server host Hyper-V di origine deve avere almeno una VM che si vuole proteggere.
	
- **Azure**: è necessaria una sottoscrizione di Azure.

	![Replica SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

In questo scenario il provider di Azure Site Recovery viene installato nel server VMM durante la distribuzione di Site Recovery. Coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra le matrici di archiviazione primaria e secondaria mediante la replica SAN sincrona.

Altre informazioni sui [requisiti di distribuzione](site-recovery-vmm-san.md#before-you-start) esatti.


## Replicare i server fisici e le macchine virtuali VMware in un sito secondario

Per proteggere VM VMware o computer fisici Windows/Linux mediante la replica tra due data center locali, sono necessari gli elementi seguenti.

- **Primario locale**: 
	- **Server di elaborazione**: configurare il componente del server di elaborazione nel sito primario per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati. Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.
	- **Server vCenter e VMware ESX/ESXi**: se si prevede di proteggere macchine virtuali VMware, è necessario un hypervisor VMware EXS/ESXi o un server VMware vCenter per la gestione di più hypervisor.
	- Computer protetti: nelle VM VMware o nei server fisici Windows/Linux da proteggere deve essere installato Unified Agent. Unified Agent viene installato anche nei computer configurati come server di destinazione master. Funge da provider di comunicazioni tra tutti i componenti InMage.
	
- **Secondario locale**:
	- **Server di configurazione**: il server di configurazione è il primo componente da installare e viene installato nel sito secondario per gestire, configurare e monitorare la distribuzione mediante il sito Web di gestione o la console vContinuum. I server di configurazione include anche il meccanismo push per la distribuzione remota di Unified Agent. È presente un solo server di configurazione in una distribuzione e deve essere installato in un computer che esegue Windows Server 2012 R2.
	- **server vContinuum**: è installato nella stessa posizione (sito secondario) del server di configurazione. Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto. In un'installazione predefinita il server vContinuum è il primo server di destinazione master e include l'installazione di Unified Agent.
	- **Server di destinazione master**: questo contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario, quindi include i punti di conservazione dei dati. Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere. Se si vuole eseguire il failback al sito primario, dovrà essere disponibile anche un server di destinazione master. 

- **Azure**: è necessaria una sottoscrizione di Azure. Per configurare la distribuzione dopo la creazione di un insieme di credenziali di Site Recovery, scaricare InMage Scout. È anche necessario installare l'aggiornamento più recente per tutti i server del componente InMage.


	![Da VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)

In questo scenario le modifiche della replica differenziale vengono inviate da Unified Agent in esecuzione nel computer protetto al server di elaborazione. Il server di elaborazione ottimizza i dati e li trasferisce al server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.


## Ciclo di vita di protezione di Hyper-V

Questo flusso di lavoro mostra il processo di protezione, replica e failover delle macchine virtuali Hyper-V.

1. **Abilitare la protezione**: configurare l'insieme di credenziali di Site Recovery, configurare le impostazioni di replica per un cloud VMM o un sito Hyper-V e abilitare la protezione per le VM. Un processo denominato **Abilita protezione** viene avviato e può essere monitorato nella scheda **Processi**. Il processo verifica che il computer sia conforme ai prerequisiti e quindi richiama il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), che configura la replica in Azure con le impostazioni configurate. Il processo **Abilita protezione** richiama anche il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) per inizializzare una replica completa della VM.
2. **Replica iniziale**: viene creato uno snapshot macchina virtuale e i dischi rigidi virtuali vengono replicati uno alla volta, fino a quando non sono stati tutti copiati in Azure o nel data center secondario. Il tempo necessario per il completamento dell'operazione dipende dalle dimensioni e dalla larghezza di banda e dal metodo di replica iniziale scelto. Se vengono apportate modifiche al disco mentre è in corso la replica iniziale, Hyper-V Replica Replication Tracker tiene traccia delle modifiche sotto forma di log di replica di Hyper-V (con estensione hrl), che si trovano nella stessa cartella dei dischi. A ogni disco è associato un file con estensione hrl, che verrà inviato alla risorsa di archiviazione secondaria. Si noti che lo snapshot e i file di log utilizzano risorse del disco durante l'esecuzione della replica iniziale. Al termine della replica iniziale, lo snapshot della VM viene eliminato e le modifiche differenziali al disco nel log vengono sincronizzate e unite.
3. **Finalizzare la protezione**: al termine della replica iniziale, il processo **Finalizza protezione** configura la rete e altre impostazioni successive alla replica e la macchina virtuale risulta protetta. Se si esegue la replica in Azure, potrebbe essere necessario modificare le impostazioni della macchina virtuale, in modo che sia pronta per il failover. A questo punto è possibile eseguire un failover di test per controllare che tutto funzioni come previsto.
4. **Replica**: dopo la replica differenziale iniziale viene eseguita la sincronizzazione, in base alle impostazioni e al metodo di replica. 
	- **Errore di replica**: se si verifica un errore della replica differenziale e una replica completa risulta costosa a livello di larghezza di banda o tempo richiesto, verrà eseguita la risincronizzazione. Ad esempio, se i file con estensione hrl raggiungono il 50% delle dimensioni del disco, la macchina virtuale verrà contrassegnata per la risincronizzazione. La risincronizzazione riduce al minimo la quantità di dati inviati calcolando i checksum delle macchine virtuali di origine e di destinazione e inviando solo il relativo differenziale. Al termine della risincronizzazione, dovrebbe riprendere la replica differenziale. Per impostazione predefinita, la risincronizzazione è pianificata per l'esecuzione automatica negli orari non lavorativi, ma è possibile risincronizzare manualmente una macchina virtuale.
	- **Errore di replica**: se si verifica un errore di replica, per impostazione predefinita viene effettuato un nuovo tentativo. Se si tratta di un errore irreversibile, ad esempio un errore di autenticazione o di autorizzazione oppure se lo stato del computer di replica non è valido, non verranno effettuati nuovi tentativi. Se si tratta di un errore reversibile, ad esempio un errore di rete o spazio su disco o memoria insufficiente, verranno eseguiti nuovi tentativi a intervalli crescenti (ogni 1, 2, 4, 8, 10 e quindi 30 minuti).
4. **Failover pianificati/non pianificati**: i failover pianificati/non pianificati vengono eseguiti in base alla necessità. Se si esegue un failover pianificato, le VM di origine vengono arrestate per assicurare che non si verifichi la perdita di dati. Dopo la creazione, lo stato delle VM di replica sarà commit in sospeso. È necessario eseguirne il commit per completare il failover, a meno che non si stia eseguendo la replica con SAN. In questo caso, il commit è automatico. Quando il sito primario è attivo e in esecuzione, è possibile eseguire il failback. Se è stata eseguita la replica in Azure, la replica inversa sarà automatica. In caso contrario, è possibile attivare una replica inversa.
 

![flusso di lavoro](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Passaggi successivi

[Preparazione della distribuzione](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0218_2016-->