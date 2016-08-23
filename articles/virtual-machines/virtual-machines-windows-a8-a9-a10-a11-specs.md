<properties
 pageTitle="Informazioni sulle dimensioni di VM A8, A9, A10, A11 e Windows | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle misure a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure per VM e servizi cloud Windows."
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
 ms.date="08/04/2016"
 ms.author="danlep"/>  

# Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo

Informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Questo articolo illustrato l'uso di queste istanze per le VM Windows. Questo articolo è disponibile anche per le [VM Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

All'interno di un singolo servizio cloud, set di disponibilità o pool di Azure Batch le istanze A8 e A9 possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Windows che usano l'interfaccia Microsoft Network Direct per la comunicazione tra istanze.

L'elenco seguente include i prerequisiti delle applicazioni MPI per l'accesso alla rete RDMA in macchine virtuali di Windows, servizi cloud e pool di Azure Batch delle istanze A8 o A9. Per scenari di distribuzione tipici, vedere [Configurare un cluster RDMA Windows con HPC Pack e istanze di dimensioni A8 e A9 per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).


Prerequisito | Macchine virtuali | Servizi cloud o pool di Batch 
---------- | ------------ | ------------- 
Sistema operativo | Windows Server 2012 R2 o Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 o la famiglia del sistema operativo guest Windows Server 2008 R2. 
MPI | MS-MPI 2012 R2 o versione successiva o Intel MPI Library 5 | MS-MPI 2012 R2 o versione successiva o Intel MPI Library 5 


>[AZURE.NOTE]Per le macchine virtuali di dimensioni A8 e A9 è necessario aggiungere alle VM l'estensione HpcVmDrivers per installare i driver di dispositivo di rete di Windows necessari per la connettività RDMA. In base al metodo di distribuzione, l'estensione HpcVmDrivers può essere aggiunta automaticamente a una VM di dimensioni A8 o A9 oppure può essere necessario aggiungerla manualmente. Per aggiungere l'estensione manualmente, vedere [Gestire le estensioni delle macchine virtuali](virtual-machines-windows-classic-manage-extensions.md).

## Considerazioni per HPC Pack e Windows

HPC Pack non è necessario per l'uso delle istanze A8, A9, A10 e A11 con Windows Server, ma può essere uno strumento utile per l'esecuzione di applicazioni MPI basate su Windows che accedono alla rete RDMA in Azure. HPC Pack 2012 R2 e versioni successive include un ambiente di runtime per l'implementazione Microsoft di Message Passing Interface (MS-MPI) per Windows che può usare la rete RDMA di Azure quando viene distribuito nelle istanze A8 e A9.

Per ulteriori informazioni ed elenchi di controllo per utilizzare le istanze con utilizzo intensivo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Servizi cloud - Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/).

* Per informazioni sulle funzionalità di archiviazione e dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-linux-sizes.md).

* Per iniziare a distribuire e usare istanze A8 e A9 con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Per informazioni sull'uso delle istanze A8 e A9 per eseguire applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0810_2016-->