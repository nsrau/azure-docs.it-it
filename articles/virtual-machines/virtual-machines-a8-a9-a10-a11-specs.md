<properties
 pageTitle="Informazioni sulle istanze A8, A9, A10 e A11 | Microsoft Azure"
 description="Questo articolo presenta informazioni generali e considerazioni sull'uso delle istanze a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="06/09/2015"
 ms.author="danlep"/>

# Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo

Questo articolo presenta informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Le funzionalità chiave che caratterizzano queste istanze includono:

* **Hardware ad alte prestazioni**: l'hardware del data center di Azure che esegue queste istanze è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, incluse applicazioni cluster HPC (High-Performance Computing), modellazione e simulazioni.

* **Connessione di rete RDMA**: le istanze A8 e A9 possono comunicare in Azure tramite una rete a bassa latenza e velocità effettiva elevata, basata sulla tecnologia di accesso diretto a memoria remota (RDMA), che favorisce il miglioramento delle prestazioni per applicazioni MPI (Message Passing Interface) parallele. L'accesso RDMA attualmente è supportato solo per i servizi cloud e le macchine virtuali basate su Windows Server.

>[AZURE.NOTE]Le istanze A10 e A11 presentano le stesse ottimizzazioni delle prestazioni e le stesse specifiche delle istanze A8 e A9. Non includono però l'accesso alla rete RDMA in Azure. Sono progettate per applicazioni di elaborazione ad alte prestazioni che non richiedono la comunicazione costante e a bassa latenza tra nodi, note anche come applicazioni parametriche o parallele.

Analogamente alle [altre dimensioni di macchine virtuali di Azure](virtual-machines-size-specs.md), le istanze A8, A9, A10 e A11 supportano immagini di sistemi operativi Windows Server e Linux standard o personalizzati nelle macchine virtuali di Azure (IaaS) oppure versioni del sistema operativo guest Azure nei servizi cloud (PaaS).

## Specifiche

### CPU e memoria

Le istanze a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure presentano CPU multicore ad alta velocità ed elevate quantità di memoria, come illustrato nella tabella seguente.

Dimensione | CPU | Memoria
------------- | ----------- | ----------------
A8 e A10 | Intel® Xeon® E5-2670<br/>8 core da 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel® Xeon® E5-2670<br/>16 core da 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Per altre informazioni sui processori, incluse estensioni supportate del set di istruzioni, vedere il sito Web Intel.com.


### Schede di rete

Le istanze A8 e A9 dispongono di due schede di rete che si connettono alle due reti back-end di Azure seguenti.


Rete | Descrizione
-------- | -----------
Ethernet a 10 Gbps | Si connette ai servizi di Azure, come i servizi di archiviazione e la rete virtuale, e a Internet
Rete back-end a 32 Gbps che supporta RDMA | Permette le comunicazioni di applicazioni a latenza minima e velocità effettiva elevata tra le istanze presenti all'interno di un singolo servizio cloud


>[AZURE.IMPORTANT]L'accesso alla rete RDMA è attualmente abilitato solo tramite applicazioni che usano l'interfaccia Microsoft Network Direct. Per altre informazioni, vedere la sezione [Accedere alla rete RDMA](#access-the-RDMA-network) in questo articolo.


Le istanze A10 e A11 dispongono di una singola scheda di rete Ethernet a 10 Gbps che si connette ai servizi di Azure e a Internet.

## Considerazioni sulla sottoscrizione di Azure

* **Account Azure**: se si intende distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento a consumo o altre opzioni di acquisto. È anche possibile usare la sottoscrizione MSDN. Vedere [Benefici di Azure per sottoscrittori MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Con una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/) è possibile usare solo un numero limitato di core di calcolo di Azure.

* **Quota di core**: potrebbe essere necessario aumentare la quota di core della sottoscrizione di Azure rispetto alla quota predefinita di 20 core, che non è sufficiente per scenari con istanze da 8 o 16 core. Per i test iniziali potrebbe essere necessario richiedere un aumento della quota fino a 100 core. A tale scopo, aprire un ticket di supporto gratuito, come descritto nel blog relativo alle [informazioni sui limiti e gli aumenti di Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Le quote di Azure sono limiti di credito e non garanzie di capacità. Viene addebitato solo l'uso dei core effettivamente impiegati.

* **Gruppo di affinità**: un gruppo di affinità di Azure può aiutare a ottimizzare le prestazioni raggruppando servizi o macchine virtuali nello stesso data center di Azure. Per raggruppare le istanze a elevato utilizzo di calcolo, è consigliabile creare un nuovo gruppo di affinità in un'area in cui si intende distribuire le istanze. Come procedura consigliata, usare il gruppo di affinità solo per le istanze a elevato utilizzo di calcolo, non per istanze di dimensioni diverse.

* **Rete virtuale**: non è necessaria una rete virtuale di Azure per poter usare le istanze a elevato utilizzo di calcolo. Per molti scenari IaaS potrebbe essere tuttavia necessaria almeno una rete virtuale di Azure basata su cloud. Per l’accesso alle risorse locali, potrebbe essere necessaria anche una connessione da sito a sito. Prima di distribuire le istanze sarà necessario creare una nuova rete virtuale (di area). L'aggiunta di una macchina virtuale A8, A9, A10 o A11 a una rete virtuale in un gruppo di affinità non è supportata. Per altre informazioni, vedere [Creazione di una rete virtuale](https://msdn.microsoft.com/library/azure/dn631643.aspx) e l'articolo relativo alla [configurazione di una rete virtuale con una connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Considerazioni sull'uso di HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029), la soluzione Microsoft gratuita per la gestione di cluster e processi, non è necessario per l'uso delle istanze A8, A9, A10 e A11, ma è uno strumento consigliato per la creazione di cluster Windows HPC in Azure. Nel caso delle istanze A8 e A9, HPC Pack è lo strumento più efficiente per eseguire applicazioni Windows MPI che accedono alla rete RDMA in Azure. HPC Pack include un ambiente di runtime per l'implementazione Microsoft dell'interfaccia MPI (MS-MPI, Message Passing Interface) per Windows.

Per altre informazioni ed elenchi di controllo sull'uso delle istanze a elevato utilizzo di calcolo con HPC Pack, vedere [Istanze con utilizzo intensivo di calcolo A8 e A9: avvio rapido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Accedere alla rete RDMA

All'interno di un singolo servizio cloud, le istanze A8 e A9 possono accedere alla rete RDMA in Azure quando eseguono applicazioni MPI che usano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze. L'interfaccia Network Direct attualmente è supportata solo dall'implementazione Microsoft di MS-MPI per Windows. Le istanze A10 e A11 non includono l'accesso alla rete RDMA.

>[AZURE.NOTE]Analogamente ad altre istanze di Azure, le istanze A8 e A9 possono eseguire carichi di lavoro diversi dalle applicazioni MPI, usando i rispettivi core CPU, memoria e spazio su disco disponibili. In questi casi tuttavia le istanze non si connettono alla rete RDMA e dal punto di vista funzionale sono equivalenti alle istanze A10 e A11.


Vedere la tabella seguente per i prerequisiti per applicazioni MPI per l'accesso alla rete RDMA in distribuzioni di servizi cloud (PaaS) o di macchine virtuali (IaaS) delle istanze A8 o A9. Per informazioni sugli scenari di distribuzione tipici, vedere [Istanze con utilizzo intensivo di calcolo A8 e A9: avvio rapido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Prerequisito | Macchine virtuali (IaaS) | Servizi cloud (PaaS)
---------- | ------------ | -------------
Sistema operativo | Macchine virtuali Windows Server 2012 R2 o Windows Server 2012 | Famiglia del sistema operativo guest Windows Server 2012 R2 o Windows Server 2008 R2
MPI | MS-MPI 2012 R2 o versioni successive, autonomo o installato mediante HPC Pack 2012 R2 o versioni successive | MS-MPI 2012 R2 o versioni successive, installato mediante HPC Pack 2012 R2 o versioni successive


>[AZURE.NOTE]Per gli scenari IaaS, è necessario aggiungere alle macchine virtuali l'[estensione HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) per installare i driver di Windows necessari per la connettività RDMA.


## Altre informazioni importanti

* Non è possibile ridimensionare una macchina virtuale di Azure esistente specificando le dimensioni A8, A9, A10 o A11.

* Le istanze A8, A9, A10 e A11 attualmente non possono essere distribuite tramite un servizio cloud che fa parte di un gruppo di affinità esistente. Analogamente, un gruppo di affinità con un servizio cloud contenente istanze A8, A9, A10 e A11 non può essere usato per distribuzioni di istanze di altre dimensioni. Se si tentano queste distribuzioni, verrà visualizzato un messaggio di errore simile a `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Macchine virtuali - Prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Servizi cloud - Prezzi](http://azure.microsoft.com/pricing/details/cloud-services/).
* Per le informazioni di base sulla distribuzione e l'uso delle istanze A8 e A9 con HPC Pack, vedere [Istanze con utilizzo intensivo di calcolo A8 e A9: avvio rapido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) e [Eseguire applicazioni MPI nelle istanze con utilizzo intensivo di calcolo A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).
 

<!---HONumber=62-->