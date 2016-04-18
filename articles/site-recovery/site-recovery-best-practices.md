<properties
	pageTitle="Preparare la distribuzione di Site Recovery | Microsoft Azure"
	description="Questo articolo descrive come preparare la distribuzione della replica con Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/29/2016"
	ms.author="raynew"/>

# Preparare la distribuzione di Azure Site Recovery

Leggere questo articolo per ottenere una panoramica generale dei requisiti di distribuzione per ogni scenario di replica supportato dal servizio Azure Site Recovery. Dopo la lettura dei requisiti generali per ogni scenario, sono disponibili collegamenti a dettagli di distribuzione specifici nella sezione dei prerequisiti di ogni articolo sulla distribuzione.

Dopo la lettura di questo articolo, è possibile inviare commenti o domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica

Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono in esecuzione e disponibili durante i periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile. La strategia BCDR è incentrata sulle soluzioni che garantiscono la sicurezza e la possibilità di recuperare i dati aziendali e di mantenere i carichi di lavoro continuamente disponibili in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover al sito secondario per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. È possibile usare Site Recovery in diversi scenari per proteggere più carichi di lavoro. Altre informazioni sono disponibili in [Informazioni su Azure Site Recovery](site-recovery-overview.md).


## Requisiti per la replica di macchine virtuali Hyper-V

**Componente** | **Replicare in Azure (con VMM)** | **Replicare in Azure (senza VMM)** | **Replicare in un sito secondario (con VMM)**
---|---|---|---
**VMM** | Uno o più server VMM in esecuzione su System Center 2012 R2. Il server VMM deve avere almeno un cloud contenente uno o più gruppi host VMM. | Non applicabile | Almeno un server VMM in esecuzione su System Center 2012 R2. È consigliabile un server VMM in ogni sito. Il server VMM deve avere almeno un cloud contenente uno o più gruppi host VMM. I cloud devono avere impostato il profilo funzionalità di Hyper-V. 
**Hyper-V** | Uno o più server host Hyper-V nel data center locale che eseguono almeno Windows Server 2012 R2. Il server Hyper-V deve trovarsi in un gruppo host in un cloud VMM. | Uno o più server Hyper-V nei siti di origine e di destinazione che eseguono almeno Windows Server 2012 R2. | Uno o più server Hyper-V nei siti di origine e di destinazione che eseguono almeno Windows Server 2012 con gli aggiornamenti più recenti. Il server Hyper-V deve trovarsi in un gruppo host in un cloud VMM.
**Macchine virtuali** | È necessaria almeno una VM nel server Hyper-V di origine. Le VM che eseguono la replica in Azure devono essere conformi ai [prerequisiti delle macchine virtuali di Azure](#azure-virtual-machine-requirements). | Almeno una VM nel server Hyper-V di origine. Le VM che eseguono la replica in Azure devono essere conformi ai [prerequisiti delle macchine virtuali di Azure](#azure-virtual-machine-requirements). | Almeno una VM nel cloud VMM di origine.
**Account di Azure** | Sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/). | Non applicabile | Sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/).
**Archiviazione di Azure** | Per archiviare i dati replicati, sarà necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover. | Non applicabile | Per archiviare i dati replicati, sarà necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover.
**Provider/Agenti** | Durante la distribuzione verranno installati il provider di Azure Site Recovery nei server VMM e l'agente di Servizi di ripristino di Azure nei server host Hyper-V. Il provider comunica con Azure Site Recovery. L'agente gestisce la replica dei dati tra i server Hyper-V di origine e di destinazione. Nelle VM non viene installato niente. | Durante la distribuzione verranno installati sia il provider Azure Site Recovery che l'agente Servizi di ripristino di Azure nel cluster o nel server host Hyper-V. Nelle VM non viene installato niente. | Durante la distribuzione verrà installato il provider di Azure Site Recovery nei server VMM per comunicare con Azure Site Recovery. La replica avviene tra i server Hyper-V di origine e di destinazione tramite LAN/VPN.
**Connettività del provider/agente** | Se il provider si connette al servizio Site Recovery tramite un proxy, è necessario assicurarsi che il proxy possa accedere agli URL di Site Recovery. | Se il provider si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il proxy possa accedere agli URL di Site Recovery. | Se il provider si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il proxy possa accedere agli URL di Site Recovery.
**Connettività Internet** | Dal server VMM e dagli host Hyper-V. | Dagli host Hyper-V. | Solo sul server VMM.
**Mapping di rete** | Configurare il mapping di rete in modo che tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete di Azure possano connettersi le une alle altre, indipendentemente dal piano di ripristino in cui si trovano. Se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali. Se non si configura il mapping di rete, solo le macchine di cui si esegue il failover nello stesso piano di ripristino possono connettersi. | Non applicabile | Configurare il mapping di rete in modo che le macchine virtuali siano connesse a reti appropriate dopo il failover e che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V di destinazione. Se non si configura il mapping di rete, le macchine replicate non verranno connesse ad alcuna rete VM dopo il failover.
**Mapping dell'archiviazione** | Non applicabile | Non applicabile | È possibile configurare il mapping di archiviazione per assicurarsi che le macchine virtuali siano correttamente connesse all'archiviazione dopo il failover. Per impostazione predefinita, la VM di replica verrà archiviata nel percorso indicato nel server Hyper-V di destinazione.
**Replica SAN** | Non applicabile | Non applicabile | Per eseguire la replica tra due siti VMM locali, è possibile usare l'ambiente SAN esistente. Vedere la pagina relativa agli [array SAN supportati](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
**Altre informazioni** | [Prerequisiti dettagliati di distribuzione](site-recovery-vmm-to-azure.md#before-you-start) | [Prerequisiti dettagliati di distribuzione](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | [Prerequisiti dettagliati di distribuzione](site-recovery-vmm-to-vmm.md#before-you-start)




## Requisiti per la replica di VM VMware e di server fisici

Nella tabella vengono riepilogati i requisiti per la replica di VM VMware e di server fisici Windows/Linux in Azure e in un sito secondario.

>[AZURE.NOTE] È possibile eseguire la replica di VM VMware e server fisici in Azure tramite un modello di distribuzione [avanzata](site-recovery-vmware-to-azure-classic.md) o con un modello [legacy](site-recovery-vmware-to-azure-classic-legacy.md) usato per le distribuzioni precedenti. La tabella seguente include i requisiti di distribuzione per il modello avanzato.

**Componente** | **Replicare in Azure (avanzato)** | **Replicare in un sito secondario**
---|---|---
**Sito primario locale** | Installare un server di gestione che esegue tutti i componenti di Site Recovery, ovvero configurazione, processo, destinazione master. | Installare un server di elaborazione per la memorizzazione nella cache, la compressione e la crittografia dei dati di replica prima dell'invio al sito secondario. È possibile installare altri server di elaborazione per il bilanciamento del carico o per la tolleranza di errore. 
**Sito secondario locale** | Non applicabile | Installare un singolo server di configurazione usato per configurare, gestire e monitorare la distribuzione.<br/><br>È consigliabile installare un server vContinuum per semplificare la gestione del server di configurazione.<br/><br/>Sarà necessario configurare il server di destinazione master come una VM in esecuzione sul server vSphere secondario. 
**vCenter/ESXi per VMware** | Se si esegue la replica di VM VMware o se si vuole eseguire il failback di server fisici, è necessario un server vSphere ESX/ESXi nel sito primario. È anche consigliabile usare un server vCenter per gestire gli host ESXi. | Nel sito primario e secondario saranno necessari uno o più host ESXi VMware e facoltativamente un server vCenter. 
**Failback** | È necessario un ambiente VMware per il failback da Azure, anche se si esegue la replica di server fisici.<br/><br/>Sarà necessario configurare un server di elaborazione come VM di Azure. <br/><br/>Il server di configurazione funge da server di destinazione master, ma se si esegue il failback di volumi elevati di traffico, è consigliabile configurare un server di destinazione master locale aggiuntivo. [Altre informazioni](site-recovery-failback-azure-to-vmware-classic.md)| Il failback dal sito secondario al sito primario viene eseguito solo verso VMware, anche se è stato eseguito il failover su un computer fisico. Per il failback è necessario configurare un server di destinazione master come VM sul server vSphere primario.
**Account di Azure** | Sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/). | Non applicabile
**Archiviazione di Azure** | Per archiviare i dati replicati, sarà necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover. | Non applicabile
**Rete virtuale di Azure** | È necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando si verifica il failover. Per eseguire il failback dopo il failover, è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale. | Non applicabile
**Computer protetti** | Almeno una macchina virtuale VMware o un server fisico Windows/Linux. Durante la distribuzione viene installato il servizio Mobility in ogni computer da replicare. | Almeno una macchina virtuale VMware o un server fisico Windows/Linux. Durante la distribuzione viene installato Unified Agent in ogni computer da replicare.
**Connettività** | Se il server di gestione si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il server proxy possa connettersi a URL specifici. | Il server di configurazione necessita dell'accesso a Internet.
**Altre informazioni** | [Prerequisiti dettagliati di distribuzione](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment). | [Scaricare](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) il manuale dell'utente per InMage Scout.


## Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Sarà necessario verificare che le macchine virtuali locali da proteggere siano conformi ai requisiti di Azure.


**Funzionalità** | **Requisiti** | **Dettagli**
---|---|---
Host Hyper-V | Deve essere in esecuzione Windows Server 2012 R2. | Il controllo dei prerequisiti avrà esito negativo se il sistema operativo non è supportato.
Hypervisor VMware | Sistema operativo supportato. | [Controllare i requisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo guest | Da Hyper-V alla replica di Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) di Windows e Linux. | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni disco del sistema operativo | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Conteggio dischi del sistema operativo | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Conteggio dischi dati | 16 o meno (il valore massimo è una funzione delle dimensioni della macchina virtuale creata; 16 = XL) | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni VHD dischi dati | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Schede di rete | Sono supportate più schede |
Indirizzo IP statico | Supportato | Se la macchina virtuale principale usa un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure Si noti che l'indirizzo IP statico per una macchina virtuale Linux in esecuzione su Hyper-V non è supportato. 
Disco iSCSI | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
VHD condiviso | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
Disco FC | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
Formato disco rigido| VHD <br/><br/> VHDX | Anche se VHDX al momento non è supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
Nome macchina virtuale| Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery
Tipo di macchina virtuale | <p>Prima generazione</p> <p>Seconda generazione - Windows</p> | È supportata una macchina virtuale di seconda generazione con disco del sistema operativo di base che include uno o due volumi di dati in formato VHDX inferiore a 300 GB. Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni del disco dati**: se si esegue la replica ad Azure è possibile avere fino a 32 dischi dati in una macchina virtuale, ciascuno con un massimo di 1 TB. È possibile replicare in modo efficiente una macchina virtuale di ~32 TB ed eseguirne il failover.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi Azure**: ogni sottoscrizione ad Azure include un insieme di limiti predefiniti su core, servizi cloud e via di seguito. Si consiglia di eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: altre informazioni sulla [pianificazione della capacità](site-recovery-capacity-planner.md) per Site Recovery.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
	- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
	- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
	- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta. Inoltre, se si esegue la replica a Site Recovery di Azure utilizzando l'agente do backup di Azure, è possibile configurare la limitazione per tale agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: se si desidera misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare qual è il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.





## Passaggi successivi

Dopo aver compreso e confrontato i requisiti generali di distribuzione, è possibile leggere i prerequisiti dettagliati e iniziare la distribuzione di ogni scenario.

- [Replicare le macchine virtuali VMWare locali in Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare i server fisici in Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare il server Hyper-V in cloud VMM in Azure](site-recovery-vmm-to-azure.md)
- [Replicare le macchine virtuali Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicare le VM Hyper-V in un sito secondario](site-recovery-vmm-to-vmm.md)
- [Replicare le VM Hyper-V in un sito secondario con SAN](site-recovery-vmm-san.md)
- [Replicare le VM Hyper-V con un singolo server VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0406_2016-->