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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


In questo articolo vengono presentate informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Le funzionalità principali di queste istanze includono:

* **Hardware ad alte prestazioni**: l'hardware del data center di Azure che esegue queste istanze è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, incluse applicazioni cluster HPC (High-Performance Computing), modellazione e simulazioni.

* **Connessione di rete RDMA per applicazioni MPI**: impostare le istanze A8 e A9 per la comunicazione con altre istanze A8 e A9 in Azure tramite una rete a bassa latenza e velocità effettiva elevata, basata sulla tecnologia di accesso diretto a memoria remota (RDMA). Questa funzionalità consente di potenziare le prestazioni di determinate applicazioni MPI (Message Passing Interface) Linux e Windows.

* **Supporto per cluster Linux e Windows HPC** : distribuire il software di gestione dei cluster e di pianificazione dei processi nelle istanze A8, A9, A10 e A11 in Azure per creare un cluster HPC autonomo o per aggiungere capacità a un cluster locale.

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


>[AZURE.IMPORTANT]Vedere [Accesso alla rete RDMA](#access-the-rdma-network) in questo articolo per requisiti aggiuntivi per applicazioni MPI Linux e Windows per l'accesso alla rete RDMA.

Le istanze A10 e A11 dispongono di una singola scheda di rete Ethernet a 10 Gbps che si connette ai servizi di Azure e a Internet.

## Considerazioni sulla distribuzione

* **Account Azure**: se si intende distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento a consumo o altre opzioni di acquisto. È anche possibile usare la sottoscrizione MSDN. Vedere [Benefici di Azure per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/) è possibile utilizzare solo un numero limitato di core di calcolo di Azure.

* **Quota di core**: può essere necessario aumentare la quota di core nella sottoscrizione di Azure dal valore predefinito di 20 core per sottoscrizione (se si usa il modello di distribuzione classico) o 20 core per area (se si usa il modello di distribuzione di Gestione risorse di Azure). Per richiedere un aumento della quota, aprire un ticket di supporto gratuito, come descritto in [Informazioni sui limiti di Azure e su come aumentarli](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Le quote di Azure sono limiti di credito e non garanzie di capacità. Viene addebitato solo l'uso dei core effettivamente impiegati.

* **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molti scenari IaaS potrebbe essere tuttavia necessaria almeno una rete virtuale di Azure basata su cloud. Per l'accesso alle risorse locali, potrebbe essere necessaria anche una connessione da sito a sito, ad esempio un server licenze per le applicazioni. È necessario creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una macchina virtuale A8, A9, A10 o A11 a una rete virtuale in un gruppo di affinità non è supportata.

* **Servizio cloud o set di disponibilità**: per connettersi tramite la rete RDMA, le istanze A8 e A9 devono essere distribuite nello stesso servizio cloud (se si usa il modello di distribuzione classico) o nello stesso set di disponibilità (se si usa il modello di distribuzione di Gestione risorse di Azure).

## Considerazioni sull'uso di HPC Pack

### Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è una soluzione gratuita di Microsoft per la gestione di cluster HPC e dei processi per Windows. La versione più recente di HPC Pack 2012 R2 supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. Per ulteriori informazioni, vedere[Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md)

### Considerazioni per HPC Pack e Windows

HPC Pack non è necessario per l'utilizzo delle istanze A8, A9, A10 e A11 con Windows Server, ma è uno strumento consigliato per la creazione di cluster Windows HPC in Azure. Nel caso delle istanze A8 e A9, HPC Pack è lo strumento più efficiente per eseguire applicazioni Windows MPI che accedono alla rete RDMA in Azure. HPC Pack include un ambiente di runtime per l'implementazione Microsoft dell'interfaccia MPI (MS-MPI, Message Passing Interface) per Windows.

Per ulteriori informazioni ed elenchi di controllo per utilizzare le istanze con utilizzo intensivo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

## Accesso alla rete RDMA

### Accedere da macchine virtuali Linux A8 e A9

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Linux. Attualmente Azure Linux RDMA è supportato solo con [Intel MPI Library 5](https://software.intel.com/it-IT/intel-mpi-library/).

>[AZURE.NOTE] Attualmente, i driver di Azure Linux RDMA non sono disponibili per l'installazione tramite estensioni del driver. Sono disponibili solo utilizzando l'immagine SLES 12 abilitata per RDMA da Azure Marketplace.

La tabella seguente riepiloga i prerequisiti per le applicazioni MPI Linux per l'accesso alla rete RDMA in cluster di nodi di calcolo (IaaS). Vedere[Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md)per la procedura di configurazione e le opzioni di distribuzione.

Prerequisito | Macchine virtuali (IaaS)
------------ | -------------
Sistema operativo | Immagine SLES 12 HPC di Azure Marketplace
MPI | Intel MPI Library 5

### Accedere da istanze A8 e A9 di Windows

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Windows che usano l'interfaccia Microsoft Network Direct per la comunicazione tra istanze.

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

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Servizi cloud - Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/).
* Per distribuire e configurare un cluster Linux con istanze A8 e A9 per accedere alla rete RDMA di Azure, vedere [Configurare un cluster RDMA di Linux per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).
* Per iniziare a distribuire e usare istanze A8 e A9 con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

<!---HONumber=AcomDC_0204_2016-->