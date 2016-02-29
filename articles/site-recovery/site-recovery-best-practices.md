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
	ms.date="02/15/2016"
	ms.author="raynew"/>

# Preparare la distribuzione di Azure Site Recovery

Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).

## Panoramica

Azure Site Recovery supporta la replica di VM VMware e Hyper-V e di server fisici in Azure o in un data center secondario. Questo articolo descrive come preparare la distribuzione di Azure Site Recovery per ognuno di questi scenari di replica.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Requisiti di distribuzione per la replica Hyper-V

Nella tabella vengono riepilogati i requisiti generali di distribuzione per la replica Hyper-V in Azure (con e senza VMM) e in un sito secondario. La tabella consente di comprendere e confrontare i requisiti generali per ogni scenario di replica. È inoltre disponibile un collegamento ai prerequisiti dettagliati di distribuzione.

**Replicare in Azure (con VMM)** | **Replicare in Azure (senza VMM)** | **Replicare in un sito secondario (con VMM)**
---|---|---
**VMM**: almeno un server VMM in esecuzione su System Center 2012 R2. Il server VMM deve avere almeno un cloud contenente uno o più gruppi host VMM.<br/><br/> **Hyper-V**: uno o più server host Hyper-V nel data center locale che eseguono almeno Windows Server 2012 R2. Il server Hyper-V deve trovarsi in un gruppo host in un cloud VMM.<br/><br/> **Macchine virtuali**: è necessaria almeno una VM nel server Hyper-V di origine. Le VM che eseguono la replica in Azure devono essere conformi ai [prerequisiti delle macchine virtuali di Azure](#azure-virtual-machine-requirements).<br/><br/> **Account di Azure**: sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/).<br/><br/> **Archiviazione di Azure**: per archiviare i dati replicati è necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le VM di Azure vengono attivate quando si verifica il failover.<br/><br/> **Mapping di rete**: configurare il mapping di rete per verificare che tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete di Azure possano connettersi le une alle altre, indipendentemente dal piano di ripristino in cui si trovano. Se è configurato un gateway di rete nella rete di Azure di destinazione, le macchine virtuali possono connettersi alle macchine virtuali locali. Se non si configura il mapping di rete, possono connettersi solo le macchine di cui si esegue il failover nello stesso piano di ripristino.<br/><br/> **Provider/Agenti**: durante la distribuzione verranno installati il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure nei server host Hyper-V. Il provider comunica con Azure Site Recovery. L'agente gestisce la replica dei dati tra i server Hyper-V di origine e di destinazione. Nelle VM non viene installato niente.<br/><br/> **Connettività Internet**: dal server VMM e dagli host Hyper-V.<br/><br/> **Connettività tramite provider**: se il provider si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il proxy possa accedere agli URL di Site Recovery.<br/><br/> [Prerequisiti dettagliati di distribuzione](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**: almeno un server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 R2.<br/><br/> **Macchine virtuali**: almeno una VM nel server Hyper-V di origine. Le VM che eseguono la replica in Azure devono essere conformi ai [prerequisiti delle macchine virtuali di Azure](#azure-virtual-machine-requirements)<br/><br/> **Account di Azure**: sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/).<br/><br/> **Archiviazione di Azure**: per archiviare i dati replicati è necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> **Provider/Agenti**: durante la distribuzione verranno installati il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure nei cluster o nei server host Hyper-V. Nelle VM non viene installato niente.<br/><br/> **Connettività Internet**: dagli host Hyper-V.<br/><br/> **Connettività tramite provider**: se il provider si connette tramite un proxy, è necessario assicurarsi che il proxy possa accedere agli URL di Site Recovery.<br/><br/> [Prerequisiti dettagliati di distribuzione](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**: il server VMM di origine deve avere almeno un cloud contenente uno o più gruppi host VMM. Nei cloud deve essere impostato il profilo funzionalità Hyper-V. <br/><br/>**Hyper-V**: uno o più server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 con gli aggiornamenti più recenti. Il server Hyper-V deve trovarsi in un gruppo host in un cloud VMM.<br/><br/> **Macchine virtuali**: almeno una VM nel cloud VMM di origine. <br/><br/> **Mapping di rete**: configurare il mapping di rete in modo che le macchine virtuali siano connesse a reti appropriate dopo il failover e che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V di destinazione. Se non si configura il mapping di rete, le macchine replicate non verranno connesse ad alcuna rete VM dopo il failover.<br/><br/> **Mapping di archiviazione**: è possibile configurare il mapping di archiviazione per assicurarsi che le macchine virtuali siano correttamente connesse all'archiviazione dopo il failover. Per impostazione predefinita, la VM di replica verrà archiviata nel percorso indicato nel server Hyper-V di destinazione.<br/><br/> **Replica SAN**: per eseguire la replica tra due siti VMM locali con la replica SAN, è possibile usare l'ambiente SAN esistente. Vedere la pagina relativa agli [array SAN supportati](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> **Provider/Agenti**: durante la distribuzione verrà installato il provider di Azure Site Recovery nei server VMM per comunicare con Azure Site Recovery. La replica avviene tra i server Hyper-V di origine e di destinazione tramite LAN/VPN.<br/><br/> **Connettività Internet**: solo nel server VMM.<br/><br/> **Connettività tramite provider**: se il provider si connette tramite un proxy, è necessario assicurarsi che gli URL di Site Recovery siano accessibili.<br/><br/> [Prerequisiti dettagliati di distribuzione](site-recovery-vmm-to-vmm.md#before-you-start)


## Requisiti di distribuzione per la replica di VM VMware e di server fisici

Nella tabella vengono riepilogati i requisiti per la replica di VM VMware e di server fisici Windows/Linux in Azure e in un sito secondario.

>[AZURE.NOTE] È possibile eseguire la replica di VM VMware e server fisici in Azure tramite un modello di distribuzione [avanzata](site-recovery-vmware-to-azure-classic.md) o con un modello [legacy](site-recovery-vmware-to-azure-classic-legacy.md) usato per le distribuzioni precedenti. La tabella seguente include i requisiti di distribuzione per ogni modello.

**Replicare in Azure (avanzato)** | **Replicare in Azure (legacy)** | **Replicare in un sito secondario**
---|---|---
**Server di gestione locale**: nel sito locale è necessario un server dedicato che fungerà da server di gestione. Tutti i componenti di Site Recovery vengono installati su questo server.<br/><br/> **Server di elaborazione aggiuntivi**: un server di elaborazione viene installato per impostazione predefinita nel server di gestione, ma se lo si vuole è possibile installare server di elaborazione locali aggiuntivi per ridimensionare la distribuzione.<br/><br/> **VMware vCenter/ESXi**: se si esegue la replica di VM VMware (o si vuole eseguire il failback di server fisici), è necessario un host vSphere ESX/ESXi in cui si trovano le VM. È consigliabile usare un server vCenter per gestire gli host ESXi.</br><br/> **Failback**: è necessario un ambiente VMware per eseguire il failback da Azure, anche se si esegue la replica di server fisici. È inoltre necessario configurare un server di elaborazione come una VM di Azure e, se si esegue il failback di elevati volumi di traffico, è possibile configurare un server di destinazione master locale aggiuntivo. [Altre informazioni](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **Account di Azure**: sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/).<br/><br/> **Archiviazione di Azure**: per archiviare i dati replicati è necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le VM di Azure vengono attivate quando si verifica il failover.<br/><br/> **Rete virtuale di Azure**: è necessaria una rete virtuale di Azure a cui le VM di Azure possano connettersi quando si verifica il failover. Per eseguire il failback dopo il failover, è necessaria una connessione VPN, oppure Azure ExpressRoute, configurata dalla rete di Azure al sito locale.<br/><br/> **Computer protetti**: almeno una macchina virtuale VMware o un server fisico Windows/Linux. Durante la distribuzione viene installato il servizio Mobility in ogni computer da replicare.<br/><br/> **Connettività**: se il server di gestione si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il server proxy possa connettersi a URL specifici.<br/><br/> [Prerequisiti dettagliati di distribuzione](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment). | **Sito primario**: è necessario configurare un server di elaborazione.<br/><br/> **Failback**: è necessario un ambiente VMware per eseguire il failback da Azure, anche se si esegue la replica di server fisici. Nel sito locale è necessario configurare un server vContinuum e un server di destinazione master. In Azure è necessario configurare un server di elaborazione. [Altre informazioni](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **Account di Azure**: sono necessari un account e una sottoscrizione di [Azure](https://azure.microsoft.com/).<br/><br/> **Archiviazione di Azure**: per archiviare i dati replicati è necessario un [account di archiviazione di Azure](../storage/storage-redundancy.md#geo-redundant-storage). I dati replicati vengono memorizzati nell'archiviazione di Azure e le VM di Azure vengono attivate quando si verifica il failover.<br/><br/> **VM dell'infrastruttura di Azure**: è necessario configurare un server di configurazione e un server di destinazione master come VM di Azure.<br/><br/> **Rete virtuale di Azure**: sarà necessaria una rete virtuale di Azure in cui saranno distribuiti il server di configurazione e il server di destinazione master. Le VM di Azure verranno connesse a questa rete dopo il failover.<br/><br/> **Computer protetti**: almeno una macchina virtuale VMware o un server fisico Windows/Linux. Durante la distribuzione viene installato il servizio Mobility in ogni computer da replicare.<br/><br/> **Connettività**: se il server di gestione si connette a Site Recovery tramite un proxy, è necessario assicurarsi che il server proxy possa connettersi a URL specifici.<br/><br/> [Prerequisiti dettagliati di distribuzione](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start). | **Sito primario**: un server Windows dedicato (macchina virtuale VMware o fisica).<br/><br/> **Sito secondario**: server di configurazione dedicato e server di destinazione master.<br/><br/> **Computer protetti**: almeno una macchina virtuale VMware o un server fisico Windows/Linux. Durante la distribuzione in ogni computer viene installato l'agente unificato.





## Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Sarà necessario verificare che le macchine virtuali locali da proteggere siano conformi ai requisiti di Azure.


**Funzionalità** | **Supporto** | **Dettagli**
---|---|---
Sistema operativo host Hyper-V | Windows Server 2012 R2 | Il controllo dei prerequisiti avrà esito negativo se non supportato
Sistema operativo di hypervisor VMware | Esecuzione di un sistema operativo supportato | [Dettagli](site-recovery-vmware-to-azure.md#before-you-start)
Sistema operativo guest | Per Hyper-V nella replica di Azure, Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure.md#before-you-start) di Windows e Linux | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni disco del sistema operativo | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Conteggio dischi del sistema operativo | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Conteggio dischi dati | 16 o meno (il valore massimo è una funzione delle dimensioni della macchina virtuale creata; 16 = XL) | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni VHD dischi dati | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Schede di rete | Sono supportate più schede |
Indirizzo IP statico | Supportato | Se la macchina virtuale principale usa un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure
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

<!---HONumber=AcomDC_0218_2016-->