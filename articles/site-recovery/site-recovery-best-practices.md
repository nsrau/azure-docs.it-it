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
	ms.date="07/06/2016"
	ms.author="raynew"/>  

# Preparare la distribuzione di Azure Site Recovery

Leggere questo articolo per ottenere una panoramica generale dei requisiti di distribuzione per ogni scenario di replica supportato dal servizio Azure Site Recovery. Dopo la lettura dei requisiti generali per ogni scenario, è possibile collegare a dettagli di distribuzione specifici per ogni distribuzione.

Dopo la lettura di questo articolo, è possibile inviare commenti o domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Overview

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. La strategia di continuità aziendale e ripristino di emergenza deve garantire la sicurezza dei dati aziendali e la possibilità di recuperarli, oltre alla disponibilità costante dei carichi di lavoro in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Informazioni su Site Recovery](site-recovery-overview.md).

## Selezionare il modello di distribuzione

Azure offre due [modelli di distribuzione](../resource-manager-deployment-model.md) diversi per la creazione e l'uso delle risorse: il modello Azure Resource Manager e il modello di gestione classica dei servizi. Sono anche disponibili il [portale di Azure classico](https://manage.windowsazure.com/), che supporta il modello di distribuzione classica, e il [portale di Azure](https://ms.portal.azure.com/), che supporta entrambi i modelli di distribuzione.

Site Recovery è disponibile sia nel portale classico che nel portale di Azure. Nel portale di Azure classico è possibile supportare Site Recovery con il modello di gestione classica dei servizi. Nel portale di Azure è possibile supportare i modelli di distribuzione classica e Resource Manager. [Altre informazioni](site-recovery-overview.md#site-recovery-in-the-azure-portal) sulla distribuzione con il portale di Azure.

Quando si sceglie un modello di distribuzione, tenere presente che:

- Si consiglia di usare il [portale di Azure](https://portal.azure.com) e il modello Resource Manager laddove possibile.
- Site Recovery fornisce un'esperienza introduttiva più semplice e più intuitiva nel portale di Azure.
- Tramite il portale di Azure, è possibile eseguire la replica dei computer nell'archiviazione classica e Resource Manager in Azure. Inoltre, nel portale di Azure è possibile usare gli account di archiviazione con ridondanza locale o con ridondanza geografica.
- Il portale di Azure combina i servizi Site Recovery e Backup in un singolo insieme di credenziali di Servizi di ripristino, per consentire di configurare e gestire i servizi di continuità aziendale e ripristino di emergenza da un'unica posizione.
- Gli utenti con sottoscrizioni di Azure in cui è stato effettuato il provisioning del programma Cloud Solution Provider (CSP) ora possono gestire le operazioni di Site Recovery nel portale di Azure.
- La replica da VM VMware o computer fisici ad Azure nel portale di Azure fornisce una serie di nuove funzionalità, tra cui il supporto dell'archiviazione Premium e la possibilità di escludere dischi specifici dalla replica.


## Selezionare lo scenario di distribuzione

**Distribuzione** | **Dettagli** | **Portale di Azure** | **Portale classico** | **PowerShell**
---|---|---|---|---
**Da VM VMware ad Azure** | Replica da VM VMware all'archiviazione di Azure | Nel portale di Azure le VM possono eseguire il failover su archiviazione classica o Resource Manager<br/><br/>. La distribuzione nel [portale di Azure](site-recovery-vmware-to-azure.md) offre un'esperienza di distribuzione semplificata e numerosi vantaggi in termini di funzionalità. | Nel portale di Azure classico è possibile distribuire con il [modello avanzato](site-recovery-vmware-to-azure-classic.md) ed eseguire il failover sull'archiviazione classica di Azure.<br/><br/> Esiste anche un modello legacy che non bisognerebbe usare per le nuove distribuzioni. | PowerShell non è attualmente supportato.
**Da VM Hyper-V ad Azure** | Da VM Hyper-V all'archiviazione di Azure Le VM possono trovarsi in host Hyper-V gestiti nei cloud System Center VMM o senza VMM. | Nel portale di Azure le VM possono eseguire il failover sull'archiviazione classica o Resource Manager.<br/><br/> Il portale di Azure offre un'esperienza di distribuzione semplificata. [Ulteriori informazioni](site-recovery-vmm-to-azure.md) sulla replica delle VM Hyper-V nei cloud VMM. [Ulteriori informazioni](site-recovery-hyper-v-site-to-azure.md) sulla replica delle VM Hyper-V (senza VMM).| Nel portale di Azure classico è possibile eseguire il failover delle VM sull'archiviazione di Azure classica | La distribuzione di PowerShell è supportata.
**Da server fisici ad Azure** | Replica da server fisici Windows e Linux all'archiviazione di Azure | Nel portale di Azure i server possono eseguire il failover sull'archiviazione classica o Resource Manager.<br/><br/> La distribuzione nel [portale di Azure](site-recovery-vmware-to-azure.md) offre un'esperienza di distribuzione semplificata e numerosi vantaggi in termini di funzionalità. | Nel portale di Azure classico è possibile distribuire con il [modello avanzato](site-recovery-vmware-to-azure-classic.md) ed eseguire il failover sull'archiviazione classica di Azure.<br/><br/> Esiste anche un modello legacy che non bisognerebbe usare per le nuove distribuzioni. | La distribuzione con PowerShell non è attualmente supportata.
**Da VM VMware/server fisici a un sito secondario* | Replica da VM VMware o server fisici Windows/Linux a un sito secondario [Scoprire ulteriori informazioni e scaricare](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) il manuale dell'utente per InMage Scout. | Non disponibile nel portale di Azure | Nel portale classico InMage Scout verrà scaricato da un insieme di credenziali di Site Recovery. | La distribuzione con PowerShell non è attualmente supportata
**Da VM Hyper-V a un sito secondario** | Replica da VM Hyper-V in cloud VMM a un data center secondario | Nel [portale di Azure](site-recovery-vmm-to-vmm.md) è possibile eseguire la replica di VM Hyper-V in cloud VMM in un sito secondario usando solo la replica Hyper-V. SAN non è attualmente supportato. | Nel portale di Azure classico è possibile eseguire la replica di VM Hyper-V in cloud VMM in un sito secondario usando la [replica Hyper-V](site-recovery-vmm-to-vmm-classic.md) o la [replica SAN](site-recovery-vmm-san.md) | La distribuzione di PowerShell è supportata.



## Verificare l'occorrente per la distribuzione

### Replicare in Azure

**Requisito** | **Dettagli** 
---|---
**Account di Azure** | È necessario un account [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.
**Archiviazione di Azure** | I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover. Per eseguire la replica in Azure, è necessario un [account di archiviazione di Azure](../storage/storage-introduction.md).<br/><br/>Se si distribuisce Site Recovery nel portale classico, sono necessari uno o più [account di archiviazione con ridondanza geografica standard](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Se si distribuisce nel portale di Azure, è possibile usare l'archiviazione con ridondanza geografica o con ridondanza locale.<br/><br/> Se si esegue la replica da VM VMware o server fisici al portale di Azure è supportata l'archiviazione Premium. Se si vuole usare un account di archiviazione Premium, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali. L'[archiviazione Premium](../storage/storage-premium-storage.md) viene in genere usata per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O.<br/><br/> Se si vuole usare un account Premium per archiviare i dati replicati, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
**Rete di Azure** | Per la replica in Azure, sarà necessaria una rete di Azure a cui le VM possano connettersi quando vengono create dopo il failover.<br/><br/> Se si distribuisce nel portale classico, si userà una rete classica. Se si distribuisce nel portale di Azure, è possibile usare una rete classica o Resource Manager.<br/><br/> La rete deve trovarsi nella stessa area dell'insieme di credenziali.
**Mapping di rete (da VMM ad Azure)** | Se si esegue la replica da VMM ad Azure, il [mapping di rete](site-recovery-network-mapping.md) garantisce che le VM di Azure siano connesse alle reti corrette dopo il failover.<br/><br/> Per configurare il mapping di rete è necessario configurare le reti di VM nel portale di VMM.
**Locale** | **VM VMware**: è necessario un computer locale che esegue i componenti di Site Recovery, gli host VMware vSphere/il server vCenter e le VM da replicare. [Altre informazioni](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Server fisici**: se si esegue la replica di server fisici, è necessario un computer locale che esegue i componenti di Site Recovery e i server fisici di cui eseguire la replica. [Altre informazioni](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Se si desidera eseguire il [failback](site-recovery-failback-azure-to-vmware.md) dopo il failover su Azure, è necessaria un'infrastruttura VMware.<br/><br/> **VM Hyper-V**: se si desidera eseguire la replica delle VM Hyper-V nei cloud VMM, è necessario disporre di un server VMM e degli host Hyper-V su cui si trovano le VM da proteggere. [Altre informazioni](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Se si desidera eseguire la replica delle VM Hyper-V senza VMM sono necessari gli host Hyper-V su cui si trovano le VM. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Computer protetti** | I computer protetti che eseguiranno la replica ad Azure devono essere conformi ai [prerequisiti di Azure](#azure-virtual-machine-requirements) descritti di seguito.


### Replica a un sito secondario

**Requisito** | **Dettagli** 
---|---
**Account di Azure** | È necessario un account [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.
**Locale** | **VM VMware**: nel sito principale è necessario un server di elaborazione per il caching, la compressione e la crittografia dei dati di replica prima dell'invio al sito secondario. Nel sito secondario è necessario installare un server di configurazione per gestire e monitorare la distribuzione e un server di destinazione master. Si consiglia inoltre un server vContinuum per una gestione più semplice. Inoltre, sono necessari uno o più host di VMware vSphere e, facoltativamente, un server vCenter. [Ulteriori informazioni](site-recovery-vmware-to-vmware.md)<br/><br/> **VM Hyper-V nei cloud VMM**: sarà necessario configurare i server VMM e gli host Hyper-V contenenti le VM di cui eseguire la replica. [Altre informazioni](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Mapping di rete (da VMM a VMM)** | Se si esegue la replica da VMM a VMM, il [mapping di rete](site-recovery-network-mapping.md) garantisce che le VM di replica siano connesse a reti appropriate dopo il failover e che siano correttamente posizionate su host Hyper-V. Per configurare il mapping di rete è necessario configurare le reti di VM nei server VMM.
**Mapping dell'archiviazione** | Se si esegue la replica da VMM a VMM è facoltativamente possibile impostare il [mapping di archiviazione](site-recovery-storage-mapping.md) in modo da specificare la destinazione di archiviazione per le VM di cui è stata eseguita la replica. Il mapping di archiviazione può essere impostato sia per la replica Hyper-V che per la replica SAN.<br/><br/> Il mapping di archiviazione non è attualmente supportato nel portale di Azure.


## Verificare l'accesso con l'URL

Assicurarsi che questi URL siano accessibili dai server.

**URL** | **Da VMM a VMM** | **Da VMM ad Azure** | **Da Hyper-V ad Azure** | **Da VMware ad Azure**
---|---|---|---|---
*.accesscontrol.windows.net | Consenti | Consenti | Consenti | Consenti
*.backup.windowsazure.com | Facoltativo | Consenti | Consenti | Consenti
*.hypervrecoverymanager.windowsazure.com | Consenti | Consenti | Consenti | Consenti
*. store.core.windows.net | Consenti | Consenti | Consenti | Consenti
*.blob.core.windows.net | Facoltativo | Consenti | Consenti | Consenti
https://www.msftncsi.com/ncsi.txt | Consenti | Consenti | Consenti | Consenti
https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | Facoltativo | Facoltativo | Facoltativo | Consenti

## Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Sarà necessario verificare che le macchine virtuali locali da proteggere siano conformi ai requisiti di Azure.


**Funzionalità** | **Requisiti** | **Dettagli**
---|---|---
Host Hyper-V | Deve essere in esecuzione Windows Server 2012 R2. | Il controllo dei prerequisiti avrà esito negativo se il sistema operativo non è supportato.
Hypervisor VMware | Sistema operativo supportato. | [Controllare i requisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo guest | Replica da Hyper-V ad Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) di Windows e Linux | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni disco del sistema operativo | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Conteggio dischi del sistema operativo | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
Conteggio dischi dati | 16 o meno (il valore massimo è una funzione delle dimensioni della macchina virtuale creata; 16 = XL) | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni VHD dischi dati | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Schede di rete | Sono supportate più schede |
Indirizzo IP statico | Supportato | Se la macchina virtuale principale usa un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure Si noti che l'indirizzo IP statico per una macchina virtuale Linux in esecuzione su Hyper-V non è supportato.
Disco iSCSI | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
VHD condiviso | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
Disco FC | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
Formato disco rigido| VHD <br/><br/> VHDX | Anche se VHDX al momento non è supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
BitLocker | Non supportate | Prima di proteggere una macchina virtuale è necessario disabilitare BitLocker.
Nome macchina virtuale| Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery
Tipo di macchina virtuale | <p>Prima generazione</p> <p>Seconda generazione - Windows</p> | È supportata una macchina virtuale di seconda generazione con disco del sistema operativo di base che include uno o due volumi di dati in formato VHDX inferiore a 300 GB. Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni del disco dati**: se si esegue la replica ad Azure è possibile avere fino a 32 dischi dati in una macchina virtuale, ciascuno con un massimo di 1 TB. È possibile replicare in modo efficiente una macchina virtuale di ~32 TB ed eseguirne il failover.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi di Azure**: ogni sottoscrizione di Azure include un set di limiti predefiniti su core, servizi cloud e così via. È consigliabile eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: altre informazioni sulla [pianificazione della capacità](site-recovery-capacity-planner.md) per Site Recovery.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
	- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
	- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
	- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta. Inoltre, se si esegue la replica ad Azure Site Recovery usando l'agente di backup di Azure è possibile configurare la limitazione per l'agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: per misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.


##URL del servizio
Assicurarsi che questi URL siano accessibili dal server


**URL** | **Da VMM a VMM** | **Da VMM ad Azure** | **Da sito Hyper-V ad Azure** | **Da VMware ad Azure**
---|---|---|---|---
 *.accesscontrol.windows.net | Accesso richiesto | Accesso richiesto | Accesso richiesto | Accesso richiesto
 *.backup.windowsazure.com | | Accesso richiesto | Accesso richiesto | Accesso richiesto
 *.hypervrecoverymanager.windowsazure.com | Accesso richiesto | Accesso richiesto | Accesso richiesto | Accesso richiesto
 *. store.core.windows.net | Accesso richiesto | Accesso richiesto | Accesso richiesto | Accesso richiesto
 *.blob.core.windows.net | | Accesso richiesto | Accesso richiesto | Accesso richiesto
 https://www.msftncsi.com/ncsi.txt   | Accesso richiesto | Accesso richiesto | Accesso richiesto | Accesso richiesto
 https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi   | | | | Accesso richiesto


## Passaggi successivi

Dopo aver compreso e confrontato i requisiti generali di distribuzione, è possibile leggere i prerequisiti dettagliati e iniziare la distribuzione di ogni scenario.

- [Replicare le macchine virtuali VMWare locali in Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare i server fisici in Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicare il server Hyper-V in cloud VMM in Azure](site-recovery-vmm-to-azure.md)
- [Replicare le macchine virtuali Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicare le VM Hyper-V in un sito secondario](site-recovery-vmm-to-vmm.md)
- [Replicare le VM Hyper-V in un sito secondario con SAN](site-recovery-vmm-san.md)
- [Replicare le VM Hyper-V con un singolo server VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0928_2016-->