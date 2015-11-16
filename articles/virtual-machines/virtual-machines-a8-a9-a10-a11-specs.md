<properties
 pageTitle="Informazioni sulle istanze A8, A9, A10 e A11 | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle misure a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure per macchine virtuali e servizi cloud."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="10/30/2015"
 ms.author="danlep"/>

# Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


In questo articolo vengono presentate informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Le funzionalità principali di queste istanze includono:

* **Hardware ad alte prestazioni**: l'hardware del data center di Azure che esegue queste istanze è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, incluse applicazioni cluster HPC (High-Performance Computing), modellazione e simulazioni.

* **Connessione di rete RDMA per le applicazioni MPI**: se configurate con i driver di rete necessari, le istanze A8 e A9 possono comunicare in Azure con altre istanze A8 e A9 tramite una rete a bassa latenza e velocità effettiva elevata, basata sulla tecnologia di accesso diretto a memoria remota (RDMA). Questa funzionalità consente di potenziare le prestazioni delle applicazioni che usano implementazioni supportate di Linux o Windows MPI (Message Passing Interface).

* **Supporto per cluster Linux e Windows HPC** : distribuire il software di gestione dei cluster e di pianificazione dei processi nelle istanze A8, A9, A10 e A11 in Azure per creare un cluster HPC autonomo o per aggiungere capacità a un cluster locale. Analogamente alle altre dimensioni di VM di Azure, le istanze A8, A9, A10 e A11 supportano immagini di sistemi operativi Windows Server e Linux standard o personalizzati o modelli di Gestione risorse di Azure nelle VM di Azure (IaaS) oppure versioni del sistema operativo guest Azure nei servizi cloud (PaaS, solo per Windows Server).

>[AZURE.NOTE]Le istanze A10 e A11 presentano le stesse ottimizzazioni delle prestazioni e le stesse specifiche delle istanze A8 e A9. Non includono però l'accesso alla rete RDMA in Azure. Sono progettate per applicazioni di elaborazione ad alte prestazioni che non richiedono la comunicazione costante e a bassa latenza tra nodi, note anche come applicazioni parametriche o parallele.


## Specifiche

### CPU e memoria

Le istanze a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure presentano CPU multicore ad alta velocità ed elevati volumi di memoria, come illustrato nella tabella seguente.

Dimensione | CPU | Memoria
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 core da 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 core da 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Per altre informazioni sui processori, incluse estensioni supportate del set di istruzioni, vedere il sito Web Intel.com. Per informazioni dettagliate sulle capacità di archiviazione e i dischi delle VM, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md).

### Schede di rete

Le istanze A8 e A9 dispongono di due schede di rete che si connettono alle due reti back-end di Azure seguenti.


Rete | Descrizione
-------- | -----------
Ethernet a 10 Gbps | Si connette ai servizi di Azure, come il servizio di archiviazione e la rete virtuale di Azure, e a Internet.
Rete back-end a 32 Gbps che supporta RDMA | Permette le comunicazioni di applicazioni a bassa latenza e velocità effettiva elevata tra le istanze presenti all'interno di un singolo servizio cloud o set di disponibilità. Riservata per il solo traffico MPI.


>[AZURE.IMPORTANT]Nelle VM A8 e A9 che eseguono Linux, l'accesso alla rete RDMA è attualmente abilitato solo tramite applicazioni che utilizzano Azure Linux RDMA e Intel MPI Library 5 su SUSE Linux Enterprise Server 12 (SLES 12). Nelle istanze A8 e A9 che eseguono Windows Server, l'accesso alla rete RDMA è attualmente abilitato solo tramite applicazioni che utilizzano l'interfaccia Microsoft Network Direct. Per altre informazioni sui requisiti, vedere la sezione [Accedere alla rete RDMA](#access-the-rdma-network) in questo articolo.

Le istanze A10 e A11 dispongono di una singola scheda di rete Ethernet a 10 Gbps che si connette ai servizi di Azure e a Internet.

## Considerazioni sulla sottoscrizione

* **Account Azure**: se si intende distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento a consumo o altre opzioni di acquisto. È anche possibile usare la sottoscrizione MSDN. Vedere [Benefici di Azure per sottoscrittori MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Con una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/) è possibile utilizzare solo un numero limitato di core di calcolo di Azure.

* **Quota di core**: potrebbe essere necessario aumentare la quota di core nella sottoscrizione di Azure dal valore predefinito di 20 core per sottoscrizione (per le distribuzioni di Gestione servizi di Azure) o 20 core per area (per le distribuzioni di Gestione risorse di Azure), che non è sufficiente per molti scenari con istanze da 8 o 16 core. Per i test iniziali potrebbe essere necessario richiedere un aumento della quota a 100 core. A tale scopo, aprire un ticket di supporto gratuito, come descritto nelle [informazioni sui limiti e gli aumenti di Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Le quote di Azure sono limiti di credito e non garanzie di capacità. Viene addebitato solo l'uso dei core effettivamente impiegati.

* **Rete virtuale**: non è necessaria una rete virtuale di Azure per poter utilizzare le istanze a elevato utilizzo di calcolo. Per molti scenari IaaS potrebbe essere tuttavia necessaria almeno una rete virtuale di Azure basata su cloud. Per l'accesso alle risorse locali, potrebbe essere necessaria anche una connessione da sito a sito, ad esempio un server licenze per le applicazioni. Prima di distribuire le istanze sarà necessario creare una nuova rete virtuale (di area). L'aggiunta di una macchina virtuale A8, A9, A10 o A11 a una rete virtuale in un gruppo di affinità non è supportata. Per altre informazioni, vedere la [documentazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

* **Servizio cloud o set di disponibilità**: per connettersi tramite la rete RDMA, le istanze A8 e A9 devono essere distribuite nello stesso servizio cloud (per gli scenari IaaS con VM Linux o Windows in Gestione servizi di Azure o scenari PaaS con Windows Server) o nello stesso set di disponibilità (per VM Linux o Windows in Gestione risorse di Azure).

## Considerazioni sull'uso di HPC Pack

### Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è una soluzione gratuita di Microsoft per la gestione di cluster HPC e dei processi per Windows. A partire da HPC Pack 2012 R2 Update 2, HPC Pack supporta varie distribuzioni di Linux per l'esecuzione su nodi di calcolo distribuiti in macchine virtuali di Azure, gestite da un nodo head di Windows Server. Con la versione più recente di HPC Pack è possibile distribuire un cluster basato su Linux in grado di eseguire applicazioni MPI che accedono alla rete RDMA in Azure. Per altre informazioni, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md).

### Considerazioni per HPC Pack e Windows

HPC Pack non è necessario per l'utilizzo delle istanze A8, A9, A10 e A11 con Windows Server, ma è uno strumento consigliato per la creazione di cluster Windows HPC in Azure. Nel caso delle istanze A8 e A9, HPC Pack è lo strumento più efficiente per eseguire applicazioni Windows MPI che accedono alla rete RDMA in Azure. HPC Pack include un ambiente di runtime per l'implementazione Microsoft dell'interfaccia MPI (Message Passing Interface) per Windows.

Per altre informazioni ed elenchi di controllo per usare le istanze a elevato utilizzo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

## Accesso alla rete RDMA

### Accedere da macchine virtuali Linux A8 e A9

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per l’esecuzione di applicazioni MPI che utilizzano i driver RDMA Linux per la comunicazione tra le istanze. Attualmente Azure Linux RDMA è supportato solo con [Intel MPI Library 5](https://software.intel.com/it-IT/intel-mpi-library/).

>[AZURE.NOTE]Attualmente, i driver di Azure Linux RDMA non sono disponibili per l'installazione tramite estensioni del driver. Sono disponibili solo utilizzando l'immagine SLES 12 abilitata per RDMA da Azure Marketplace.

Vedere la tabella seguente per i prerequisiti per applicazioni MPI Linux per l'accesso alla rete RDMA in cluster di nodi di calcolo (IaaS). Per la procedura di configurazione e le opzioni di distribuzione, vedere [Configurare un cluster RDMA di Linux per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).

Prerequisito | Macchine virtuali (IaaS)
------------ | -------------
Sistema operativo | Immagine SLES 12 HPC di Azure Marketplace
MPI | Intel MPI Library 5

### Accedere da istanze A8 e A9 di Windows

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per l’esecuzione di applicazioni MPI che utilizzano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze. Le istanze A10 e A11 non includono l'accesso alla rete RDMA.

Vedere la tabella seguente per i prerequisiti per applicazioni MPI per l'accesso alla rete RDMA in distribuzioni di servizi cloud (PaaS) o di macchine virtuali (IaaS) delle istanze A8 o A9. Per scenari di distribuzione comuni, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).


Prerequisito | Macchine virtuali (IaaS) | Servizi cloud (PaaS)
---------- | ------------ | -------------
Sistema operativo | Windows Server 2012 R2 o Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 o la famiglia del sistema operativo guest Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 o versioni successive, autonomo o installato mediante HPC Pack 2012 R2 o versioni successive<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 o versioni successive, installato tramite HPC Pack 2012 R2 o versioni successive<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Per gli scenari IaaS, è necessario aggiungere alle VM l'estensione HpcVmDrivers per installare i driver di Windows necessari per la connettività RDMA. In base al metodo di distribuzione, l’estensione HpcVmDrivers potrebbe essere aggiunta automaticamente a una VM di dimensione A8 o A9 o potrebbe essere necessario aggiungerla manualmente. Per aggiungere l'estensione, vedere l'articolo relativo alla [gestione delle estensioni delle VM](virtual-machines-extensions-install.md).


## Altre informazioni importanti

* **Prezzi**: le dimensioni delle VM A8 - A11 sono disponibili solo per il piano tariffario Standard.

* **Ridimensionamento**: non è possibile ridimensionare un'istanza di dimensioni diverse da A8 - A11 in uno dei formati di istanza a elevato utilizzo di calcolo (A8 - 11) e non è possibile ridimensionare un'istanza a elevato utilizzo di calcolo in una dimensione a utilizzo di calcolo non elevato. Questo per via dell'hardware specializzato e delle ottimizzazioni delle prestazioni, che sono specifici delle istanze a elevato utilizzo di calcolo.

* **Spazio degli indirizzi della rete RDMA**: la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/12. Se si prevede di eseguire applicazioni MPI in istanze A8 e A9 in una rete virtuale di Azure, verificare che lo spazio degli indirizzi della rete virtuale non si sovrapponga alla rete RDMA.

## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere gli articoli relativi ai [prezzi delle macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/) e ai [prezzi dei servizi cloud](http://azure.microsoft.com/pricing/details/cloud-services/).
* Per distribuire e configurare un cluster Linux con istanze A8 e A9 per accedere alla rete RDMA di Azure, vedere [Configurare un cluster RDMA di Linux per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).
* Per iniziare a distribuire e usare istanze A8 e A9 con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

<!---HONumber=Nov15_HO2-->