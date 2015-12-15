<properties
	pageTitle="Preparare la distribuzione di Site Recovery | Microsoft Azure"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un centro dati secondario."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Preparare la distribuzione di Azure Site Recovery

## Informazioni sull’articolo

Questo articolo descrive come preparare la distribuzione di Azure Site Recovery. In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Protezione di macchine virtuali Hyper-V

Per proteggere le macchine virtuali Hyper-V, sono disponibili due scelte a livello di distribuzione. È possibile replicare le macchine virtuali Hyper-V locali in Azure o in un centro dati secondario. Esistono requisiti diversi per ogni distribuzione.

**Requisito** | **Replicare in Azure (con VMM)** | **Replicare VM Hyper-V in Azure (nessuna VMM)** | **Replicare VM Hyper-V in un sito secondario (con VMM)** | **Dettagli**
---|---|---|---|---
**VMM** | VMM in esecuzione in System Center 2012 R2 <br/><br/>Almeno un cloud VMM contenente uno o più gruppi host VMM. | ND | Server VMM nei siti primari e secondari in esecuzione almeno in System Center 2012 SP1 con gli aggiornamenti più recenti. <br/><br/> Almeno un cloud in ogni server VMM. I cloud devono avere il profilo di capacità Hyper-V impostato.<br/><br/> Il cloud di origine deve avere almeno un gruppo host VMM. | Facoltativa. Non è necessario distribuire System Center VMM per replicare le macchine virtuali Hyper-V in Azure, ma, se lo si distribuisce, sarà necessario verificare che il server VMM sia configurato correttamente. Verificare anche che sia in esecuzione la versione di VMM corretta e che i cloud siano configurati.
**Hyper-V** | Almeno un server host Hyper-V nel centro dati locale che esegue almeno Windows Server 2012 R2 | Almeno un server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 R2 e connesso a Internet.<br/><br/> I server Hyper-V devono essere in un gruppo host in un cloud VMM. | Almeno un server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 con gli aggiornamenti più recenti installati e connesso a Internet.<br/><br/> I server Hyper-V devono trovarsi in un gruppo host in un cloud VMM. | 
**Macchine virtuali** | Almeno una VM nel server host Hyper-V di origine | Almeno una VM nel server host Hyper-V nel cloud VMM di origine | Almeno una VM nel server host Hyper-V nel cloud VMM di origine. | Le VM che eseguono la replica in Azure devono essere conformi ai [prerequisiti delle macchine virtuali di Azure](site-recovery-best-practices.md/#virtual-machines)
**Account di Azure** | Saranno necessari un account di [Azure](http://azure.microsoft.com/) e una sottoscrizione al servizio Site Recovery. | Saranno necessari un account di [Azure](http://azure.microsoft.com/) e una sottoscrizione al servizio Site Recovery. | ND | Nel caso in cui non sia disponibile un account, iniziare con una [versione di valutazione gratuita](pricing/free-trial/). Informazioni sui [prezzi](pricing/details/site-recovery/) per il servizio.
**Archiviazione di Azure** | Sarà necessaria una sottoscrizione per un account di archiviazione di Azure con la replica geografica abilitata. | Sarà necessaria una sottoscrizione per un account di archiviazione di Azure con la replica geografica abilitata. | ND | L'account dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. [Altre informazioni sui servizi di archiviazione](../storage/storage-introduction.md).
**Mapping dell'archiviazione** | ND | ND | Facoltativamente è possibile configurare il mapping dell'archiviazione per verificare che le macchine virtuali siano connesse al servizio di archiviazione in modo ottimale dopo il failover. Durante la replica tra due siti VMM locali, per impostazione predefinita la macchina virtuale di replica verrà archiviata nel percorso indicato nel server host Hyper-V di destinazione. È possibile configurare il mapping tra classificazioni di archiviazione VMM nei server VMM di origine e di destinazione. | Per usare questa funzionalità, saranno necessarie classificazioni di archiviazione configurate prima di iniziare la distribuzione. [Altre informazioni](site-recovery-storage-mapping.md).
**Replica SAN** | ND | ND | Per eseguire la replica tra due siti VMM locali, è possibile usare l'ambiente SAN esistente. | Sarà necessaria una [matrice SAN supportata](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) e l'archivio SAN deve essere trovato e classificato in VMM.<br/><br/>Se attualmente non si sta eseguendo la replica, sarà necessario creare LUN e allocare spazio di archiviazione nella console VMM. Se si sta già eseguendo la replica, è possibile ignorare questo passaggio.
**Rete** | Configurare il mapping di rete per verificare che tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete di Azure possono connettersi le une alle altre, indipendentemente dal piano di ripristino in cui si trovano. Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali. Se non si configura il mapping di rete, solo le macchine di cui si esegue il failover nello stesso piano di ripristino possono connettersi. | ND | <br/><br/>Configurare il mapping di rete per verificare che le macchine virtuali siano connesse a reti appropriate dopo il failover e che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V. Se non si configura il mapping di rete, le macchine replicate non verranno connesse ad alcuna rete VM dopo il failover. | [Ulteriori informazioni](site-recovery-network-mapping) sul mapping di rete. <br/><br/> Per configurare il mapping di rete con VMM, sarà necessario verificare che le reti VM e logiche VMM siano configurate correttamente. [Altre informazioni](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) e [Reti VM](https://technet.microsoft.com/library/jj721575.aspx). Sono disponibili anche altre [considerazioni sulla rete per VMM](site-recovery-network-design.md/#vmm-design).  
**Provider e agenti** | Durante la distribuzione si installerà il provider di Azure Site Recovery nei server VMM. Nei server Hyper-V nei cloud VMM verrà installato l'agente Servizi di ripristino di Azure. | Durante la distribuzione verranno installati sia il provider Azure Site Recovery che l'agente Servizi di ripristino di Azure nel cluster o nel server host Hyper-V.| Durante la distribuzione si installerà il provider di Azure Site Recovery nei server VMM. Nei server Hyper-V nei cloud VMM verrà installato l'agente Servizi di ripristino di Azure. | La connessione dei provider e degli agenti a Site Recovery viene effettuata tramite Internet, mediante una connessione HTTPS crittografata. Non è necessario aggiungere eccezioni del firewall o creare un proxy specifico per la connessione dei provider, ma, per usare un proxy personalizzato, è consigliabile consentire questi URL attraverso il firewall prima di iniziare le distribuzione: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net **Connettività Internet** | Solo i server VMM hanno bisogno di una connessione Internet | Solo i server host Hyper-V hanno bisogno di una connessione Internet | Solo i server VMM hanno bisogno di una connessione Internet | Non è necessario installare nulla nelle macchine virtuali, che non si connettono direttamente a Internet.



## Proteggere macchine virtuali VMware o server fisici

Per proteggere le macchine virtuali VMware o i server fisici Windows/Linux, sono disponibili due scelte a livello di distribuzione. È possibile replicarli in Azure o in un centro dati secondario. Esistono requisiti diversi per ogni distribuzione.

**Requisito** | **Replicare VM VMware/server fisici in Azure** | **Replicare VM VMware/server fisici in un sito secondario**  
---|---|--- 
**Sito primario** | **Server di elaborazione**: un server Windows dedicato (fisico o virtuale) | **Server di elaborazione**: un server Windows dedicato (macchina virtuale fisica o VMware)<br/><br/>  
**Sito secondario locale** | ND | **Server di configurazione**: un server Windows dedicato (fisico o virtuale) <br/><br/> **Server di destinazione master**: un server dedicato (fisico o virtuale). Eseguire la configurazione con Windows per proteggere computer Windows o con Linux per proteggere computer Linux.
**Azzurro** | **Sottoscrizione**: sarà necessaria una sottoscrizione per il servizio Site Recovery. Informazioni sui [prezzi](pricing/details/site-recovery/) <br/><br/> **Account di archiviazione**: sarà necessario un account di archiviazione con la replica geografica abilitata. L'account dovrà trovarsi nella stessa area dell'insieme di credenziali di Site Recovery ed essere associato alla stessa sottoscrizione. [Altre informazioni](../storage/storage-introduction.md).<br/><br/> **Server di configurazione**: sarà necessario configurare il server di configurazione come VM di Azure <br/><br/> **Server di destinazione master**: sarà necessario configurare il server di destinazione master come VM di Azure <br/><br/> Eseguire la configurazione con Windows per proteggere computer Windows o con Linux per proteggere computer Linux.<br/><br/> **Rete virtuale di Azure**: sarà necessaria una rete virtuale di Azure in cui saranno distribuiti il server di configurazione e il server di destinazione master. Dovrà trovarsi nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali di Azure Site Recovery. | ND  
**Macchine virtuali/server fisici** | Almeno una macchina virtuale VMware o un server Windows/Linux fisico.<br/><br/>Durante la distribuzione in ogni computer verrà installato il servizio Mobility| Almeno una VM VMware o un server Windows/Linux fisico.<br/><br/> Durante la distribuzione in ogni computer viene installato Unified Agent.




## Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Sarà necessario verificare che le macchine virtuali locali da proteggere siano conformi ai requisiti di Azure.


**Funzionalità** | **Supporto** | **Dettagli**
---|---|---
Sistema operativo host Hyper-V | Windows Server 2012 R2 | Il controllo dei prerequisiti avrà esito negativo se non supportato
Sistema operativo di hypervisor VMware | Esecuzione di un sistema operativo supportato | [Dettagli](site-recovery-vmware-to-azure.md/#before-you-start) 
Sistema operativo guest | Per Hyper-V nella replica di Azure, Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure.md/#before-you-start) di Windows e Linux | Il controllo dei prerequisiti avrà esito negativo se non supportato. 
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
Tipo di macchina virtuale | <p>Prima generazione</p> <p>Seconda generazione - Windows</p> | È supportata una macchina virtuale di seconda generazione con disco del sistema operativo di base che include uno o due volumi di dati in formato VHDX inferiore a 300 GB. Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni del disco dati**: se si esegue la replica ad Azure è possibile avere fino a 32 dischi dati in una macchina virtuale, ciascuno con un massimo di 1 TB. È possibile replicare in modo efficiente una macchina virtuale di ~32 TB ed eseguirne il failover.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi Azure**: ogni sottoscrizione ad Azure include un insieme di limiti predefiniti su core, servizi cloud e via di seguito. Si consiglia di eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: leggere le informazioni sullo [strumento di pianificazione della capacità](http://www.microsoft.com/download/details.aspx?id=39057) se si replicano VM Hyper-V.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
	- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
	- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
	- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta. Inoltre, se si esegue la replica a Site Recovery di Azure utilizzando l'agente do backup di Azure, è possibile configurare la limitazione per tale agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: se si desidera misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare qual è il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.





## Passaggi successivi

Dopo aver esaminato queste procedure consigliate, è possibile avviare la distribuzione di Site Recovery:

- [Configurare la protezione tra un sito VMM locale e Azure](site-recovery-vmm-to-azure.md)
- [Configurare la protezione tra un sito Hyper-V locale e Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configurare la protezione tra due siti VMM locali](site-recovery-vmm-to-vmm.md)
- [Configurare la protezione tra due siti VMM locali con SAN](site-recovery-vmm-san.md)
- [Configurare la protezione con un singolo server VMM](site-recovery-single-vmm.md)
 

<!---HONumber=AcomDC_1210_2015-->