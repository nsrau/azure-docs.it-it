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
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Funzionamento di Azure Site Recovery

## Informazioni sull’articolo

Questo articolo illustra l'architettura sottostante di Site Recovery e i componenti necessari per il funzionamento di questo servizio. Dopo avere letto questo articolo, sarà possibile inserire domande nel [Forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Panoramica

Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono disponibili durante periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile. La strategia BCDR sarà incentrata principalmente sulle soluzioni che assicurano la sicurezza e la possibilità di recuperare i dati aziendali e di mantenere i carichi di lavoro continuamente disponibili in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover al sito secondario per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località.

È possibile usare Site Recovery in diversi scenari per proteggere più carichi di lavoro.

- **Proteggere macchine virtuali VMware**: è possibile proteggere macchine virtuali VMware locali mediante la replica delle VM in Azure o in un data center secondario.- **Proteggere VM Hyper-V**: è possibile proteggere macchine virtuali Hyper-V locali mediante la replica delle VM sul cloud (Azure) o in un data center secondario.  
- **Proteggere i server fisici**: è possibile proteggere i computer fisici che eseguono Windows o Linux mediante la replica dei computer in Azure o in un data center secondario.
- **Eseguire la migrazione delle VM**: è possibile usare Site Recovery per eseguire la migrazione delle VM IaaS di Azure tra aree geografiche oppure per eseguire la migrazione di istanze Windows di AWS nelle VM IaaS di Azure.

Per un riepilogo completo delle distribuzioni supportate, vedere [Panoramica di Azure Site Recovery](site-recovery-overview.md) e [Indicazioni relative al carico di lavoro di Site Recovery](site-recovery-workload.md).

## Replica tra un server fisico locale o una macchina virtuale VMware e Azure

Per proteggere VM VMware o computer fisici Windows/Linux mediante la replica in Azure, sono necessari gli elementi seguenti.

**Posizione** | **Elementi necessari** 
--- | --- 
 Locale | **Server di elaborazione**: questo server ottimizza i dati da macchine virtuali VMware protette o computer fisici Windows/Linux prima dell'invio in Azure. Gestisce anche l'installazione push del componente del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle macchine virtuali VMware. <br/><br/> **Server vCenter VMware**: se si proteggono VM VMware, sarà necessario un server vCenter VMware per la gestione degli hypervisor vSphere<br/><br/> **Server ESX**: se si proteggono VM VMware, sarà necessario un server che esegue ESX/ESXi versione 5.1 o 5.5 con gli aggiornamenti più recenti.<br/><br/> **Computer**: se si proteggono computer VMware, è necessario avere a disposizione VM VMware con strumenti VMware installati e in esecuzione. Se si proteggono computer fisici, è necessario che eseguano una versione supportata del sistema operativo Windows o Linux. Vedere le [informazioni sui componenti supportati](site-recovery-vmware-to-azure.md/#before-you-start). <br/><br/> **Servizio Mobility**: deve essere installato nei computer da proteggere per acquisire le modifiche e comunicarle al server di elaborazione. <br/><br/>Componenti di terze parti: questa distribuzione dipende da alcuni [componenti di terze parti](http://download.microsoft.com/download/C/D/7/CD79E327-BF5A-4026-8FF4-9EB990F9CEE2/Third-Party_Notices.txt).
Azure | **Server di configurazione**: VM Standard A3 di Azure che coordina le comunicazioni tra i computer protetti, il server di elaborazione e i server di destinazione master in Azure. Configura la replica e coordina il ripristino in caso di failover. <br/><br/>**Server di destinazione master**: VM di Azure che include i dati replicati dai computer protetti mediante VHD associati creati nell'archivio BLOB nell'account di archiviazione di Azure. Un server di destinazione master di failback viene eseguito in locale, in modo che sia possibile eseguire il failback di VM d Azure in VM VMware. <br/><br/> **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery. <br/><br/> **Rete virtuale**: una rete di Azure in cui si trovano il server di configurazione e i server di destinazione master, nella stessa sottoscrizione e area del servizio Site Recovery. <br/><br/> **Archiviazione di Azure**: account di archiviazione di Azure in cui archiviare i dati replicati. Deve essere un account con ridondanza geografica Standard o un account Premium nella stessa area della sottoscrizione a Site Recovery.


In questo scenario è possibile che si verifichino comunicazioni su connessione VPN verso porte interne nella rete di Azure (mediante Azure ExpressRoute o una VPN da sito a sito) oppure tramite una connessione Internet sicura verso gli endpoint pubblici mappati nel servizio cloud di Azure per le VM di configurazione e del server di destinazione master.

Il servizio Mobility nei computer protetti invia i dati di replica al server di elaborazione e invia i metadati di replica al server di configurazione. Il server di elaborazione comunica con il server di configurazione per informazioni relative alla gestione e al controllo. Invia le informazioni sulla replica al server di destinazione master e ottimizza e invia i dati replicati al server di destinazione master.

## Replica di VM Hyper-V in Azure (con VMM)

Se le VM si trovano in un host Hyper-V gestito in un cloud System Center VMM, per eseguirne la replica in Azure sono necessari gli elementi seguenti.

**Posizione** | **Elementi necessari** 
--- | --- 
Locale | **Server VMM**: almeno un server VMM configurato con almeno un cloud privato VMM. Il provider di Azure Site Recovery verrà installato in ogni server VMM<br/><br/>**Server Hyper-V**: almeno un server host Hyper-V posizionato nel cloud VMM. L'Agente di servizi di ripristino Microsoft verrà installato in ogni server Hyper-V. <br/><br/> **Macchine virtuali**: almeno una macchina virtuale in esecuzione nel server Hyper-V. Non viene installato niente nella macchina virtuale.
Azure | **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery. <br/><br/>**Account di archiviazione**: un account di archiviazione di Azure nella stessa sottoscrizione del servizio Site Recovery. I computer replicati vengono archiviati nell'archiviazione di Azure. 

In questo scenario il provider in esecuzione nel server VMM coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra l'Agente di servizi di ripristino in esecuzione nel server Hyper-V locale e l'archiviazione di Azure su HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

![Da VMM locale ad Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

## Replica di VM Hyper-V in Azure (senza VMM)

Se le VM non sono gestite da un server System Center VMM, per replicarle in Azure sono necessari gli elementi seguenti.

**Posizione** | **Elementi necessari**
--- | --- 
 Locale | **Server Hyper-V**: almeno un server host Hyper-V. Il provider di Azure Site Recovery e l'Agente di servizi di ripristino Microsoft verranno installati in ogni server Hyper-V. <br/><br/>**Macchine virtuali**: almeno una macchina virtuale in esecuzione nel server Hyper-V. Non viene installato niente nella macchina virtuale.
Azure | **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery. <br/><br/>**Account di archiviazione**: un account di archiviazione di Azure nella stessa sottoscrizione del servizio Site Recovery. I computer replicati vengono archiviati nell'archiviazione di Azure.

In questo scenario il provider in esecuzione nel server Hyper-V coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra l'Agente di servizi di ripristino in esecuzione nel server Hyper-V locale e l'archiviazione di Azure su HTTPS 443. Le comunicazioni dal provider e dall'agente sono protette e crittografate. I dati replicati nell'archiviazione di Azure vengono anche crittografati.

![Da VMM locale ad Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)



## Replica di VM Hyper-V in un data center secondario

Per proteggere VM Hyper-V mediante la replica in un data center secondario, sono necessari gli elementi seguenti. Si noti che è possibile eseguire questa operazione solo se il server host Hyper-V è gestito in un cloud System Center VMM.

**Posizione** | **Elementi necessari** 
--- | --- 
 Locale | **Server VMM**: un server VMM nel sito primario e uno nel sito secondario. Il provider di Azure Site Recovery verrà installato in ogni server VMM.<br/><br/>**Server Hyper-V**: almeno un server host Hyper-V posizionato nel cloud VMM. Non viene installato niente nei server Hyper-V. <br/><br/> **Macchine virtuali**: almeno una macchina virtuale in esecuzione nel server Hyper-V. Non viene installato niente nella macchina virtuale.
Azure | **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery. 

In questo scenario il provider nel server VMM coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra il server host Hyper-V primario e secondario su Internet mediante Kerberos o l'autenticazione del certificato. Le comunicazioni dal provider e tra i server host Hyper-V sono protette e crittografate.

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem.png)

## Replica di VM Hyper-V in un data center secondario mediante la replica SAN

Se le VM si trovano in un host Hyper-V gestito in un cloud System Center VMM e si usa l'archiviazione SAN, per eseguirne la replica tra due data center sono necessari gli elementi seguenti.

**Posizione** | **Elementi necessari** 
--- | --- 
 Data center primario | **Matrice SAN**: una [matrice SAN supportata](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gestita dal server VMM primario. La matrice SAN condivide un'infrastruttura di rete con un'altra matrice SAN nel sito secondario. <br/><br/> **Server VMM**: almeno un server VMM con uno o più cloud VMM e gruppi di replica configurati. Il provider di Azure Site Recovery verrà installato in ogni server VMM. <br/><br/> **Server Hyper-V**: almeno un server host Hyper-V con macchine virtuali, posizionato in un gruppo di replica. Non viene installato niente nei server Hyper-V.<br/><br/> **Macchine virtuali**: almeno una macchina virtuale in esecuzione nel server host Hyper-V. Non viene installato niente nella macchina virtuale. 
Centro dati secondario | **Matrice SAN**: una [matrice SAN supportata](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gestita dal server VMM primario. <br/><br/>**Server VMM**: almeno un server VMM con uno o più cloud VMM.<br/><br/> **Server Hyper-V**: almeno un server host Hyper-V. 
Azure | **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery.

In questo scenario il provider nel server VMM coordina e orchestra la replica con il servizio Site Recovery su Internet. I dati vengono replicati tra le matrici di archiviazione primaria e secondaria mediante la replica SAN sincrona.

![Da sito locale a sito locale](./media/site-recovery-components/arch-onprem-onprem-san.png)



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

## Eseguire la replica di macchine virtuali VMware e server fisici in Azure

È possibile eseguire la replica di macchine virtuali VMware e server fisici (Windows/Linux) in Azure su una connessione VPN da sito a sito o su Internet.

### Eseguire la replica su connessione VPN da sito a sito o ExpressRoute in Azure

![Da computer fisico o VMware ad Azure tramite Internet](./media/site-recovery-components/arch-onprem-azure-vmware-vpn.png)

#### Replica su Internet

![Da computer fisico o VMware ad Azure tramite Internet](./media/site-recovery-components/arch-onprem-azure-vmware-internet.png)

## Eseguire la replica tra server fisici locali o macchine virtuali VMware in data center primari e secondari

Per proteggere VM VMware o computer fisici Windows/Linux mediante la replica tra due data center locali, sono necessari gli elementi seguenti.

**Posizione** | **Elementi necessari** 
--- | --- 
 Primario locale | **Server di elaborazione**: configurare il componente del server di elaborazione nel sito primario per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati. Gestisce anche l'installazione push di Unified Agent nei computer da proteggere. <br/><br/> **Protezione VMware**: se si proteggono VM VMware, sarà necessario un hypervisor VMware EXS/ESXi o un server VMware vCenter che gestisce più hypervisor.<br/><br/> **Protezione del server fisico**: se si proteggono computer fisici, è necessario che eseguano Windows o Linux. <br/><br/> **Unified Agent**: deve essere installato nei computer da proteggere e nel computer che funge da server di destinazione master. Funge da provider di comunicazioni tra tutti i componenti InMage.
Secondario locale | **Server di configurazione**: il server di configurazione è il primo componente da installare e viene installato nel sito secondario per gestire, configurare e monitorare la distribuzione mediante il sito Web di gestione o la console vContinuum. I server di configurazione include anche il meccanismo push per la distribuzione remota di Unified Agent. In una distribuzione è disponibile solo un server di configurazione, che deve essere installato in un computer che esegue Windows Server 2012 R2. <br/><br/> **Server vContinuum**: deve essere installato nella stessa posizione (sito secondario) del server di configurazione. Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto. In un'installazione predefinita il server vContinuum è il primo server di destinazione master e include l'installazione di Unified Agent. <br/><br/> **Server di destinazione master**: il server di destinazione master include i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario, quindi include i punti di conservazione dei dati. Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere. Se si vuole eseguire il failback al sito primario, dovrà essere disponibile anche un server di destinazione master. 
Azure | **Insieme di credenziali di Site Recovery**: almeno un insieme di credenziali di Azure Site Recovery, configurato con una sottoscrizione al servizio Site Recovery. Per configurare la distribuzione dopo la creazione dell'insieme di credenziali, scaricare InMage Scout. È anche necessario installare l'aggiornamento più recente per tutti i server del componente InMage.


In questo scenario le modifiche della replica differenziale vengono inviate da Unified Agent in esecuzione nel computer protetto al server di elaborazione. Il server di elaborazione ottimizza i dati e li trasferisce al server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.




## Passaggi successivi

[Preparazione della distribuzione](site-recovery-best-practices.md).

<!---HONumber=AcomDC_1210_2015-->