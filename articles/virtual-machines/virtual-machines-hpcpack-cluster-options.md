<properties
 pageTitle="Opzioni cluster HPC Pack nel cloud | Microsoft Azure"
 description="Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un cluster high performance computing (HPC) nel cloud di Azure."
 services="virtual-machines,cloud-services"
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
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Opzioni per creare e gestire un cluster di calcolo ad elevate prestazioni (HPC) in Azure con Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo si applica a una risorsa creata con il modello di distribuzione di gestione delle risorse o il modello di distribuzione classico.

È possibile sfruttare i servizi dell'infrastruttura e di calcolo di Microsoft HPC Pack e Azure per creare e gestire un cluster high performance computing (HPC)basato su cloud. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server e supporta i carichi di lavoro sia di Windows che di Linux HPC. Un cluster di HPC Pack basato su cloud fornisce a un amministratore cluster o a un fornitore di software indipendente (ISV) una piattaforma flessibile e scalabile per l'esecuzione di applicazioni complesse, riducendo l'investimento in un'infrastruttura cluster di elaborazione locale.

<!-- Take advantage of automated tools for HPC Pack cluster deployment in Azure VMs, including an HPC Pack image that can be used with either Azure quickstart templates or Azure PowerShell scripts, or deploy your cluster manually in the Azure portal.-->


## Eseguire un cluster HPC Pack nelle VM di Azure


### Immagini di macchina virtuale di Azure

* [HPC Pack su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo di calcolo HPC Pack su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nodo di calcolo HPC Pack con Excel su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Modelli di Guida introduttiva di Azure

* [Creare un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [Creare un cluster HPC con i nodi di elaborazione di Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [Creare un cluster HPC con un'immagine di nodo di calcolo personalizzata](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Script di distribuzione di PowerShell

* [Creare un cluster HPC con lo script di distribuzione IaaS di HPC Pack](virtual-machines-hpcpackcluster-powershell-script.md)

### Esercitazioni

* [Esercitazione: Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [Esercitazione: Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [Esercitazione: Introduzione all’uso di un cluster HPC Pack in Azure per l’esecuzione di carichi di lavoro di Excel e SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### Distribuzione manuale nel portale di Azure



* [Configurare il nodo head di un cluster HPC Pack in una VM di Azure](virtual-machines-hpcpack-cluster-headnode.md)

### Gestione dei cluster

* [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-hpcpack-cluster-node-manage.md)

* [Aggiungere nodi di "potenziamento" di Azure a un nodo head HPC Pack in Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Aumentare e ridurre le risorse di calcolo di Azure in un cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Inviare processi a un cluster HPC Pack in Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)



## Aggiungere nodi di ruolo di lavoro a un cluster HPC Pack


* [Potenziare Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Esercitazione: Configurare un cluster ibrido con HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Aggiungere nodi di "potenziamento" di Azure a un nodo head HPC Pack in Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Aumentare e ridurre le risorse di calcolo di Azure in un cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)


## Creare cluster RDMA per carichi di lavoro MPI

* [Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI](virtual-machines-windows-hpcpack-cluster-rdma.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=Oct15_HO1-->