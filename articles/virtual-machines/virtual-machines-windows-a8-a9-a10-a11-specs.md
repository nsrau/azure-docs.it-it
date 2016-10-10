<properties
 pageTitle="Informazioni sulle macchine virtuali a elevato utilizzo di calcolo con Windows | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle dimensioni di Azure serie H e A8, A9, A10 ed A11 a elevato utilizzo di calcolo per VM e servizi cloud Windows"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo

Informazioni generali e considerazioni sull'uso delle nuove istanze di Azure serie H e delle precedenti istanze A8, A9, A10 e A11, note anche come istanze *a elevato utilizzo di calcolo*. Questo articolo illustrato l'uso di queste istanze per le VM Windows. Questo articolo è disponibile anche per le [VM Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

È possibile creare cluster di istanze di Windows Server con supporto per RDMA e distribuire una delle implementazioni MPI supportate per sfruttare i vantaggi offerti dalla rete RDMA di Azure. Questa rete a bassa latenza e velocità effettiva elevata è riservata esclusivamente al traffico MPI.

* **Sistema operativo**
    * **Macchine virtuali**: Windows Server 2012 R2, Windows Server 2012
    * **Servizi cloud**: famiglia di sistemi operativi guest Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2

* **MPI**: Microsoft MPI (MS-MPI) 2012 R2 o versione successiva, Intel MPI Library 5.x

Le implementazioni MPI supportate usano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze. Per le opzioni distribuzione e per procedure di configurazione di esempio, vedere [Configurare un cluster RDMA Windows con HPC Pack per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).


>[AZURE.NOTE]Nelle VM a elevato utilizzo di calcolo con supporto per RDMA è necessario aggiungere l'estensione HpcVmDrivers per installare i driver di dispositivo di rete di Windows necessari per la connettività RDMA. Nella maggior parte delle distribuzioni l'estensione HpcVmDrivers viene aggiunta automaticamente. Se è necessario aggiungere l'estensione in modo autonomo, vedere [Gestire le estensioni delle macchine virtuali](virtual-machines-windows-classic-manage-extensions.md).

## Considerazioni per HPC Pack e Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la soluzione Microsoft gratuita per la gestione di cluster e processi, non è necessaria per l'uso di istanze a elevato utilizzo di calcolo con Windows Server. Rappresenta tuttavia un'opzione per creare un cluster di elaborazione in Azure per l'esecuzione di applicazioni MPI basate su Windows e altri carichi di lavoro HPC. HPC Pack 2012 R2 e versioni successive include un ambiente di runtime per MS-MPI che può usare la rete RDMA di Azure quando viene distribuito in macchine virtuali con supporto per RDMA.

Per ulteriori informazioni ed elenchi di controllo per utilizzare le istanze con utilizzo intensivo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle dimensioni a elevato utilizzo di calcolo, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Servizi cloud - Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/).

* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

* Per iniziare a distribuire e usare istanze a elevato utilizzo di calcolo con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Per informazioni sull'uso delle istanze A8 e A9 per eseguire applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0928_2016-->