<properties
 pageTitle="Informazioni sulle istanze A8 - A11 con Windows | Microsoft Azure"
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informazioni sull’uso delle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo con Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Windows che usano l'interfaccia Microsoft Network Direct per la comunicazione tra istanze.

Vedere la tabella seguente per i prerequisiti per applicazioni MPI per l'accesso alla rete RDMA in distribuzioni di servizi cloud (PaaS) o di macchine virtuali (IaaS) delle istanze A8 o A9. Per scenari di distribuzione comuni, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Prerequisito | Macchine virtuali (IaaS) | Servizi cloud (PaaS)
---------- | ------------ | -------------
Sistema operativo | Windows Server 2012 R2 o Windows Server 2012 | Windows Server 2012 R2, Windows Server 2012 o la famiglia del sistema operativo guest Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 o versioni successive, autonomo o installato mediante HPC Pack 2012 R2 o versioni successive<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 o versioni successive, installato tramite HPC Pack 2012 R2 o versioni successive<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Per gli scenari IaaS, è necessario aggiungere alle VM l'estensione HpcVmDrivers per installare i driver di Windows necessari per la connettività RDMA. In base al metodo di distribuzione, l’estensione HpcVmDrivers potrebbe essere aggiunta automaticamente a una VM di dimensione A8 o A9 o potrebbe essere necessario aggiungerla manualmente. Per aggiungere l'estensione, vedere l'articolo relativo alla [gestione delle estensioni delle VM](virtual-machines-windows-classic-manage-extensions.md).

## Considerazioni per HPC Pack e Windows

HPC Pack non è necessario per l'utilizzo delle istanze A8, A9, A10 e A11 con Windows Server, ma è uno strumento consigliato per l’esecuzione di applicazioni MPI basate su Windows che accedono alla rete RDMA in Azure. HPC Pack include un ambiente di runtime per l'implementazione Microsoft dell'interfaccia MPI (MS-MPI, Message Passing Interface) per Windows.

Per ulteriori informazioni ed elenchi di controllo per utilizzare le istanze con utilizzo intensivo di calcolo con HPC Pack in Windows Server, vedere [Configurare un cluster di Windows RDMA con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Macchine virtuali - Prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Servizi cloud - Prezzi](https://azure.microsoft.com/pricing/details/cloud-services/).

* Per iniziare a distribuire e usare istanze A8 e A9 con HPC Pack in Windows, vedere [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Per informazioni sull'utilizzo di istanze A8 e A9 per eseguire applicazioni MPI con Azure Batch, vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->