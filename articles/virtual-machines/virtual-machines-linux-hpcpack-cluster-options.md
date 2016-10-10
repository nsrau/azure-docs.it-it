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
 ms.date="09/26/2016"
 ms.author="danlep"/>  

# Opzioni con pacchetto HPC per creare e gestire un cluster HPC in Azure per i carichi di lavoro di Linux

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

In questo articolo sono trattate le opzioni per l'uso di HPC Pack per l'esecuzione di carichi di lavoro di Linux. Esistono anche opzioni per l'esecuzione di [carichi di lavoro di Windows HPC con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## Eseguire un cluster HPC Pack nelle VM di Azure

### Modelli di Azure


* (Marketplace) [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) (Cluster HPC Pack per carichi di lavoro di Linux)

* (Guida introduttiva) [Create an HPC cluster with Linux compute nodes](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn) (Creare un cluster HPC con nodi di calcolo Linux)


### Script di distribuzione di PowerShell

* [Creare un cluster HPC Linux con lo script di distribuzione IaaS di HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Esercitazioni

* [Esercitazione: Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Esercitazione: Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Esercitazione: Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Esercitazione: Eseguire STAR-CCM+ con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Gestione dei cluster

* [Inviare processi a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Job management in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx) (Gestione dei processi in HPC Pack)


## Creare cluster RDMA per carichi di lavoro MPI

* [Esercitazione: Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->