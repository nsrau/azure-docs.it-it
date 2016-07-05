<properties
 pageTitle="Opzioni cluster HPC Linux Pack nel cloud | Microsoft Azure"
 description="Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un cluster HPC (High Performance Computing) Linux nel cloud di Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="06/17/2016"
 ms.author="danlep"/>

# Opzioni per creare e gestire un cluster HPC in Azure con Microsoft HPC Pack

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

In questo articolo sono trattate le opzioni per l'uso di HPC Pack per l'esecuzione di carichi di lavoro di Linux. Esistono anche opzioni per l'esecuzione di [carichi di lavoro di Windows HPC con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Eseguire un cluster HPC Pack nelle VM di Azure

### Modelli di Azure


* (Marketplace) [Cluster HPC Pack per carichi di lavoro Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Guida introduttiva) [Creare un cluster HPC con nodi di calcolo Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### Script di distribuzione di PowerShell

* [Creare un cluster HPC Linux con lo script di distribuzione IaaS di HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Esercitazioni

* [Esercitazione: Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Esercitazione: Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Esercitazione: Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Esercitazione: Eseguire STAR-CCM+ con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Gestione dei cluster

* [Inviare processi a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Creare cluster RDMA per carichi di lavoro MPI

* [Esercitazione: Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0622_2016-->