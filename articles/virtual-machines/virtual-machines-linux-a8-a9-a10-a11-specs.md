<properties
 pageTitle="Informazioni sulle istanze A8 - A11 e Linux | Microsoft Azure"
 description="Informazioni generali e considerazioni sull'uso delle dimensioni a elevato utilizzo di calcolo A8, A9, A10 e A11 per VM Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Informazioni sull’uso delle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo con Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accesso alla rete RDMA

All'interno di un singolo servizio cloud o set di disponibilità, le istanze A8 e A9 possono accedere alla rete RDMA in Azure per eseguire applicazioni MPI Linux. In questo momento Azure Linux RDMA è supportato solo con [Intel MPI Library 5](https://software.intel.com/it-IT/intel-mpi-library/).

>[AZURE.NOTE] Attualmente, i driver di Azure Linux RDMA non sono disponibili per l'installazione tramite estensioni del driver. Sono disponibili solo utilizzando l'immagine SLES 12 abilitata per RDMA da Azure Marketplace.

La tabella seguente riepiloga i prerequisiti per le applicazioni MPI Linux per l'accesso alla rete RDMA in cluster di nodi di calcolo (IaaS). Vedere[Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)per la procedura di configurazione e le opzioni di distribuzione.

Prerequisito | Macchine virtuali (IaaS)
------------ | -------------
Sistema operativo | Immagine SLES 12 HPC di Azure Marketplace
MPI | Intel MPI Library 5

## Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è una soluzione gratuita di Microsoft per la gestione di cluster HPC e dei processi per Windows. La versione più recente di HPC Pack 2012 R2 supporta varie distribuzioni Linux per l'esecuzione su nodi di calcolo distribuiti in VM di Azure, gestite da un nodo head di Windows Server. I nodi di calcolo Linux distribuiti su VM A8 o A9 e che eseguono un'implementazione MPI supportata possono eseguire applicazioni MPI che accedono alla rete RDMA. Per iniziare, vedere[Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

## Passaggi successivi

* Per informazioni dettagliate sulla disponibilità e i prezzi delle istanze A8, A9, A10 e A11, vedere [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Per iniziare a distribuire e usare istanze A8 e A9 con RDMA su Linux, vedere [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0323_2016-->