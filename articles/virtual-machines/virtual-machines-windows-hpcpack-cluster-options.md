<properties
 pageTitle="Opzioni cluster HPC Pack Windows nel cloud | Microsoft Azure"
 description="Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un cluster high performance computing (HPC) Windows nel cloud di Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Opzioni per creare e gestire un cluster high performance computing (HPC) Windows in Azure con Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## Eseguire un cluster HPC Pack nelle VM di Azure

### Modelli di Azure

* (Marketplace) [Cluster HPC Pack per carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster HPC Pack per carichi di lavoro di Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Guida introduttiva) [Creare un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (Guida introduttiva) [Creare un cluster HPC con un'immagine di nodo di calcolo personalizzata](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Immagini di macchina virtuale di Azure

* [HPC Pack su Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo di calcolo HPC Pack su Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nodo di calcolo HPC Pack con Excel su Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### Script di distribuzione di PowerShell

* [Creare un cluster HPC con lo script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Esercitazioni

* [Esercitazione: Introduzione all’uso di un cluster HPC Pack in Azure per l’esecuzione di carichi di lavoro di Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### Distribuzione manuale nel portale di Azure

* [Configurare il nodo head di un cluster HPC Pack in una VM di Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### Gestione dei cluster

* [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [Aumentare e ridurre le risorse di calcolo di Azure in un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Inviare processi a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Aggiungere nodi di ruolo di lavoro a un cluster HPC Pack


* [Potenziare le istanze del ruolo di lavoro di Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Esercitazione: Configurare un cluster ibrido con HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Aggiungere nodi di "potenziamento" di Azure a un nodo head HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [Aumentare e ridurre le risorse di calcolo di Azure in un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## Integrazione con Azure Batch 

* [Potenziare Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## Creare cluster RDMA per carichi di lavoro MPI

* [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->