<properties
	pageTitle="Procedure consigliate per la distribuzione di Site Recovery"
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei server locali in Azure o in un data center secondario."
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
	ms.date="05/08/2015"
	ms.author="raynew"/>

# Procedure consigliate per la distribuzione di Site Recovery


<a id="overview" name="overview" href="#overview"></a>

## Informazioni sull'articolo

Nell'articolo, sono incluse le procedure consigliate che è necessario leggere e implementare prima di distribuire Azure Site Recovery. Se si sta cercando un'introduzione a Site Recovery e agli scenari di distribuzione correlati, leggere [Panoramica di Site Recovery](site-recovery-overview.md).

In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Preparazione di Azure

- **Account Azure**: sarà necessario disporre di un account [Microsoft Azure](http://azure.microsoft.com/). Nel caso in cui non sia disponibile, cominciare con una [versione di valutazione gratuita](pricing/free-trial/).
- Informazioni sui [prezzi](pricing/details/site-recovery/) per il servizio Site Recovery. 
- **Archiviazione di Azure**: se Site Recovery viene distribuito con la replica in Azure, sarà necessario disporre di un account di archiviazione Azure. È possibile configurarne uno durante la distribuzione o prepararne uno prima di iniziare. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. Leggere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md). 

## Macchine virtuali

Se si desidera eseguire la replica nell'archiviazione di Azure, tenere presente quanto segue:

- **Sistema operativo supportato**: è possibile distribuire Site Recovery per orchestrare la protezione delle macchine virtuali con un sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux.
- **Supporto di VHDX**: sebbene VHDX al momento non sia supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
- **Requisiti di Azure**: accertarsi che le macchine virtuali che si desidera proteggere siano conformi ai requisiti di Azure.

**Funzionalità della macchina virtuale** | **Supporto** | **Dettagli**
---|---|---
Sistema operativo host | Windows Server 2012 R2 | Il controllo dei prerequisiti avrà esito negativo se non supportato
Sistema operativo guest | <p>Windows Server 2008 R2 o versione successiva</p><p>Linux: Centos, openSUSE, SUSE, Ubuntu</p> | Il controllo dei prerequisiti avrà esito negativo se non supportato. Aggiornare il valore nella console VMM.
Architettura del sistema operativo guest | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni disco del sistema operativo | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Conteggio dischi del sistema operativo | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato. Aggiornare il valore nella console VMM
Conteggio dischi dati | 16 o meno \(il valore massimo è una funzione delle dimensioni della macchina virtuale creata; 16 = XL\) | Il controllo dei prerequisiti avrà esito negativo se non supportato
Dimensioni VHD dischi dati | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
Schede di rete | Sono supportate più schede |
Indirizzo IP statico | Supportato | Se la macchina virtuale principale utilizza un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure
Disco iSCSI | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
VHD condiviso | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
Disco FC | Non supportato | Il controllo dei prerequisiti avrà esito negativo se non supportato
Formato disco rigido| <p>VHD</p><p>VHDX</p> |
Nome macchina virtuale| Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery
Tipo di macchina virtuale | <p>Prima generazione</p> <p>Seconda generazione - Windows</p> | È supportata una macchina virtuale di seconda generazione con disco del sistema operativo di base che include uno o due volumi di dati in formato VHDX inferiore a 300 GB. Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## Server VMM

Site Recovery è in grado di orchestrare la replica per le macchine virtuali situate sui server host Hyper-V nei cloud System Center Virtual Machine Manager \(VMM\), come segue: replica da un server VMM locale ad Azure \(tramite la replica Hyper-V\), replica a un sito secondario locale \(tramite la replica Hyper-V\). È consigliabile eseguire la distribuzione con un server VMM in un sito primario e uno nel sito secondario. Tuttavia se necessario, è possibile [distribuire un singolo server VMM](site-recovery-single-vmm.md) per entrambi i siti. Replica in un sito locale secondario \(tramite SAN\). È necessario un datacenter primario e secondario con un server VMM in ciascun sito. Se si desidera distribuire VMM con Site Recovery, sarà necessario configurare l'infrastruttura VMM. Se non si dispone di un server VMM, leggere ulteriori informazioni [qui](site-recovery-hyper-v-site-to-azure.md).


### Verificare la versione VMM

**Versione di VMM supportata** | **Scenario di Site Recovery supportato** | **Host Hyper-V supportato \(origine/destinazione\)** | **Provider/agente supportato**
---|---|---|---
VMM in System Center 2012 R2 con almeno l'aggiornamento cumulativo 5 \(in pratica solo nel cluster\) | Protezione da sito locale a sito locale con replica SAN | Almeno Windows Server 2012 con gli aggiornamenti più recenti | Versione più recente del provider Azure Site Recovery \(installazione nei server VMM\) |
VMM in System Center 2012 R2 \(scelta consigliata\) \(cluster o autonomo\) | <p>Protezione da sito locale a sito locale con replica Hyper-V</p> <p>Protezione da sito locale ad Azure con replica Hyper-V</p> | <p>Almeno Windows Server 2012 con gli aggiornamenti più recenti</p><p>Windows Server 2012 R2 \(origine, non applicabile per la destinazione\)</p> | <p>Versione più recente del provider Azure Site Recovery \(installazione nei server VMM\)</p> <p>Versione più recente di Agente di Servizi di ripristino di Azure \(installazione nei server host Hyper-V per la replica esclusivamente in Azure\)</p>
VMM in System Center 2012 SP1 con aggiornamento cumulativo più recente \(cluster o autonomo\) | Protezione da sito locale a sito locale | Windows Server 2012 con gli aggiornamenti più recenti | Versione più recente del provider Azure Site Recovery \(installazione nei server VMM\) |

### Configurare l'infrastruttura cloud VMM

A prescindere dallo scenario VMM che si desidera distribuire con Site Recovery, sarà necessario creare almeno due cloud privati \(uno nel server VMM di origine e uno nel server di destinazione\) in VMM per ottenere i vantaggi del modello di cloud privato e al contempo definire, gestire e accedere al cloud e alle relative risorse sottostanti tramite la console VMM. Un cloud VMM raccoglie e presenta un set di risorse aggregato, il cui uso è limitato dalle impostazioni di capacità del cloud e dalle quote dei ruoli. Gli utenti self-service possono gestire e utilizzare le risorse di un cloud senza comprenderne a fondo l'infrastruttura sottostante. È possibile aggiungere facilmente risorse per ridurre o aumentare l'elasticità del cloud. Se è necessario configurare i cloud, utilizzare le seguenti risorse:


- [Novità del cloud privato con System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 e i cloud](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
- [Configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [Creazione di un cloud privato in VMM](https://technet.microsoft.com/it-it/library/jj860425.aspx)
- [Procedura dettagliata: creazioni di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).




## Provider e agenti

I provider e gli agenti vengono installati su server locali in modo che possano connettersi a Site Recovery in Azure. La connessione viene effettuata tramite Internet, tramite una connessione HTTPS crittografata.

- **Requisiti del provider**: durante la distribuzione, verranno installati alcuni componenti nei server locali:
	- **Nei server VMM**: verrà installato il provider di Azure Site Recovery nei server VMM che si desidera registrare nell'insieme di credenziali.
	- **Nei server host Hyper-V che si trovano in un cloud VMM**: verrà installato l'agente Servizi di ripristino di Azure.
	- **Nei server host Hyper-V senza server VMM**: verranno installati il provider Azure Site Recovery e l'agente Servizi di ripristino di Azure in ciascun server host Hyper-V o nodo cluster.
-  **Installazione del provider**: al momento dell'installazione del provider e dell'agente prestare attenzione a quanto indicato di seguito:
	-  È necessario installare la stessa versione del provider in tutti i server in un insieme di credenziali di Site Recovery. L'utilizzo di versioni diverse all'interno di un singolo insieme di credenziali non è supportato.
	-  Se si desidera replicare in Azure dai server Hyper-V presenti in un cloud VMM, nel server VMM deve essere in esecuzione System Center 2012 R2. Se si desidera replicare in un datacenter secondario, nel VMM deve essere in esecuzione System Center 2012 con SP1 o R2.
- **Proxy**: non è necessario aggiungere le eccezioni firewall o creare un proxy specifico. Se si desidera utilizzare un proxy personalizzato per la connessione del provider, prima di iniziare la distribuzione è necessario effettuare le seguenti operazioni:

	- Configurare il server proxy personalizzato prima di installare il provider.
	- Consentire i seguenti URL attraverso il firewall:
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- Se si distribuisce Site Recovery con VMM e si utilizza un proxy personalizzato, verrà creato automaticamente un account RunAs VMM \(DRAProxyAccount\) utilizzando le credenziali proxy specificate nelle impostazioni del proxy personalizzato nel portale di Site Recovery. È necessario configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione.



## Connettività locale

- **Connettività dei provider**: i provider e gli agenti vengono installati su server locali in modo che possano connettersi a Site Recovery. La connessione a Site Recovery viene effettuata tramite Internet, mediante una connessione HTTPS crittografata. Non è necessario aggiungere le eccezioni firewall o creare un proxy specifico.
- **Connettività Internet**: è necessaria la connettività Internet del server, come indicato di seguito:
	- Se si esegue la protezione delle macchine virtuali nei server host Hyper-V in un cloud VMM, solo il server VMM richiede una connessione a Internet.
	- Se si esegue la protezione delle macchine virtuali nei server host Hyper-V senza server VMM, solo i server host Hyper-V richiedono una connessione a Internet.
	- Non è necessaria alcuna connettività Internet dalle macchine virtuali che si desidera proteggere.
- **VPN da sito a sito**: non è necessaria una connessione VPN da sito a sito per connettersi a Site Recovery. Tuttavia, se si dispone di una connessione da sito a sito, sarà possibile accedere alle macchine virtuali di cui è stato effettuato il failover come avveniva prima del failover. Si noti che durante la replica in Azure verrà configurata una rete VPN da sito a sito tra il sito locale e una rete di Azure specifica. Non è utilizzata per il traffico di replica crittografato. Si passa tramite Internet all'account di archiviazione di Azure nella sottoscrizione.
- **Connettività dopo il failover**: per assicurarsi che gli utenti siano in grado di connettersi alle macchine virtuali dopo il failover in Azure, effettuare le seguenti operazioni:
	- Abilitare RDP per le macchine virtuali prima del failover.
	- Dopo il failover utilizzare una connessione da sito a sito, in modo da connettersi a esse come in precedenza o abilitare l'endpoint RDP per la macchina.

## Archiviazione

- **Account di archiviazione di Azure**: se si esegue la replica in Azure sarà necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. Per ulteriori informazioni, leggere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).
- **Mapping dell'archiviazione**: se si esegue la replica delle macchine virtuali nei server VMM locali, è possibile configurare il mapping per assicurarsi che le macchine virtuali siano connesse all'archiviazione in modo ottimale dopo il failover. Durante la replica tra due siti VMM locali, per impostazione predefinita la macchina virtuale di replica verrà archiviata nel percorso indicato nel server host Hyper-V di destinazione. È possibile configurare il mapping tra classificazioni di archiviazione VMM nei server VMM di origine e di destinazione. Se si desidera utilizzare questa funzionalità, assicurarsi di disporre delle classificazioni di archiviazione configurate prima di iniziare la distribuzione. [Ulteriori informazioni](site-recovery-storage-mapping.md) sul mapping dell'archiviazione.
- **SAN**: se si desidera eseguire la replica tra due siti locali con la replica SAN, notare quanto indicato di seguito:
	- È possibile replicare solo le macchine virtuali Hyper-V in un datacenter secondario con la replica SAN. Non è possibile eseguire la replica in Azure.
	- È possibile utilizzare un ambiente SAN esistente. Non è necessario apportare modifiche agli array SAN.
	- È necessario verificare che l'array SAN sia [supportato](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
	- Per la distribuzione SAN sono necessari due server VMM nei siti di origine e di destinazione.
	- Assicurarsi che nei cluster host Hyper-V sia in esecuzione Windows Server 2012 o 2012 R2. [Ulteriori informazioni](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) sui sistemi operativi guest supportati dalle diverse versioni di Hyper-V.
	- È necessario individuare e classificare l'archiviazione SAN in VMM.
	- Se non si sta già eseguendo la replica, dopo l'individuazione è necessario creare delle LUN e allocare spazio di archiviazione nella console VMM. Se si sta già eseguendo la replica, è possibile ignorare questo passaggio.
	- Le istruzioni complete sono disponibili in questo [articolo](site-recovery-vmm-san.md).


## Reti

- **Informazioni sulle considerazioni relative alle reti**: [Ulteriori informazioni](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) sulle considerazioni e sulle procedure consigliate relative alle reti.

- **Configurare il mapping di rete**:il mapping di rete è un elemento importante quando nella distribuzione di VMM e Site Recovery. Tale mapping consente di posizionare in modo ottimale le macchine virtuali nei server host Hyper-V di destinazione e assicura che le macchine virtuali replicate vengano connesse alle reti appropriate dopo il failover. È possibile configurare il mapping di rete quando si esegue la replica in Azure o in un datacenter secondario:
	- **Mapping di rete ad Azure**: se si esegue la replica ad Azure, il mapping di rete consente di assicurare che tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete siano in grado di connettersi tra loro, indipendentemente dal piano di ripristino di cui fanno parte. Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali. Se non si configura il mapping di rete, solo le macchine di cui si esegue il failover nello stesso piano di ripristino possono connettersi.
	- **Mapping di rete al sito secondario**: se si esegue la replica a un sito VMM secondario, il mapping di rete garantisce che le macchine virtuali siano connesse alle reti appropriate dopo il failover e che le macchine virtuali di replica vengano posizionate in modo ottimale nei server host Hyper-V. Se non si configura il mapping di rete, le macchine replicate non verranno connesse ad alcuna rete VM.
	- [Ulteriori informazioni](site-recovery-network-mapping) sul mapping di rete.
- **Configurare reti VMM**:
	- Configurare reti logiche e reti correttamente in VMM. Ulteriori informazioni sulle [reti logiche](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) e sulle [reti VM](https://technet.microsoft.com/library/jj721575.aspx).
	- Assicurarsi che tutte le macchine virtuali nel server VMM di origine siano connesse a una rete VM.
	- Verificare che le reti VM siano collegate a una rete logica associata al cloud.
	- Se si esegue la replica in Azure, creare reti virtuali in Azure. Si noti che è possibile mappare più reti VM a una singola rete di Azure. Leggere [Attività di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/dn133795.aspx).

## Ottimizzazione delle prestazioni

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 127 GB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni del disco dati**: se si esegue la replica ad Azure è possibile avere fino a 32 dischi dati in una macchina virtuale, ciascuno con un massimo di 1 TB. È possibile replicare in modo efficiente una macchina virtuale di \~32 TB ed eseguirne il failover.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi Azure**: ogni sottoscrizione ad Azure include un insieme di limiti predefiniti su core, servizi cloud e via di seguito. Si consiglia di eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: per indicazioni, utilizzare [lo strumento di pianificazione della capacità per la replica Hyper-V](http://www.microsoft.com/it-it/download/details.aspx?id=39057).
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
	- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
	- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
	- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta. Inoltre, se si esegue la replica a Site Recovery di Azure utilizzando l'agente do backup di Azure, è possibile configurare la limitazione per tale agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: se si desidera misurare l'obiettivo del tempo di ripristino \(RTO\) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare qual è il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino \(RPO\) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.

## Failover
- **Interruzione sul sito primario**: se si esegue la replica da un data center locale a un altro e in entrambi i datacenter si verifica un'interruzione sul sito primario, eseguire un failover non pianificato dal portale di Site Recovery. Per eseguire il failover non è necessaria la connettività dal datacenter primario.
- **Conservare l'indirizzo IP dopo il failover a un sito secondario**: se si desidera mantenere l'indirizzo IP di una macchina virtuale di origine dopo il failover di questa a un datacenter secondario, attenersi alla procedura fornita [qui](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx).
- **Conservare l'indirizzo IP dopo il failover Azure**: è possibile specificare l'IP fornito a un VMM di cui è stato eseguito il failover, nella scheda Configura della macchina virtuale. Per ulteriori informazioni, vedere come [configurare le proprietà di rete della macchina virtuale](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines)
- **Conservare l'indirizzo IP pubblico**: se si desidera conservare un indirizzo IP pubblico dopo il failover a un sito secondario, Site Recovery non impedisce l'operazione se l'ISP la supporta. Non è possibile conservare un indirizzo IP pubblico dopo il failover ad Azure.
- **Conservare gli indirizzi interni non RFC in Azure**: è possibile mantenere lo spazio di indirizzi non RFC 1918 dopo il failover ad Azure.
- **Failover parziale al datacenter secondario**: se si esegue il failover di un sito parziale al datacenter secondario e si desidera riconnettersi al sito primario, è possibile utilizzare una VPN da sito a sito per la connessione di un'applicazione di failover sul sito secondario ai componenti dell'infrastruttura in esecuzione nel sito primario. Si noti che in caso di failover dell'intera subnet, è possibile conservare l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
- **Failover parziale ad Azure**: se si esegue il failover di un sito parziale ad Azure e si desidera riconnettersi al sito primario, è possibile utilizzare una VPN da sito a sito per la connessione di un'applicazione di failover in Azure ai componenti dell'infrastruttura in esecuzione nel sito primario. Si noti che in caso di failover dell'intera subnet, è possibile conservare l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
- **Conservare la lettera di unità**: se si desidera conservare la lettera di unità nelle macchine virtuali dopo il failover, è possibile impostare il criterio SAN per la macchina virtuale su **Attivo**. [Altre informazioni](https://support.microsoft.com/kb/3031135).
- **Routing delle richieste client dopo il failover ad Azure**: Site Recovery funziona con Gestione traffico di Azure per indirizzare le richieste client all'applicazione dopo il failover. È possibile utilizzare gli script nei piani di ripristino \(con Automazione di Azure\) per eseguire gli aggiornamenti DNS.

## Integrazione

- **Integrazione con altre tecnologie BCDR**: Site Recovery si integra con altre tecnologie di continuità aziendale e ripristino di emergenza \(BCDR\). Per la replica basata sull'applicazione, è consigliabile utilizzare SQL Server AlwaysOn per replicare le macchine virtuali che eseguono il database e la replica Hyper-V per le macchine virtuali front-end. AlwaysOn richiede licenze SQL Server Enterprise nel sito primario e secondario e una macchina virtuale Azure in esecuzione nella sottoscrizione. L'uso di Site Recovery nella replica incorporata è utile dove le applicazioni possono gestire un certo tempo di inattività e in questo caso si consiglia che la replica di database, applicazioni e macchine virtuali front-end sia gestita da Site Recovery. Questo approccio consente di risparmiare denaro relativamente alla versione di SQL Server necessaria, al numero di licenze e al costo di aggiornamento delle macchine virtuali in esecuzione in Azure in qualsiasi momento.

## Passaggi successivi

Dopo aver esaminato queste procedure consigliate, è possibile avviare la distribuzione di Site Recovery:

- [Configurare la protezione tra un sito VMM locale e Azure](site-recovery-vmm-to-azure.md)
- [Configurare la protezione tra un sito Hyper-V locale e Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configurare la protezione tra due siti VMM locali](site-recovery-vmm-to-vmm.md)
- [Configurare la protezione tra due siti VMM locali con SAN](site-recovery-vmm-san.md)
- [Configurare la protezione con un singolo server VMM](site-recovery-single-vmm.md)
 

<!---HONumber=58_postMigration-->