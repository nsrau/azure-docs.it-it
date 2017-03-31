---
title: Risorse per batch e HPC nel cloud di Azure | Microsoft Docs
description: Questo articolo fornisce un elenco delle risorse tecniche che permettono di eseguire carichi di lavoro paralleli, batch e HPC (High Performance Computing) su larga scala in Azure.
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 671d5c3e88585d7874dbfb0b03adc3cd9374ceca
ms.lasthandoff: 03/27/2017


---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Big Compute in Azure: risorse tecniche per Batch e High Performance Computing
Si tratta di una guida alle risorse tecniche che permettono di eseguire carichi di lavoro paralleli, batch e HPC (High Performance Computing) su larga scala in Azure. La vasta gamma di servizi Azure consente di estendere il batch esistente o i carichi di lavoro HPC nel cloud Azure o creare nuove soluzioni Big Compute.

## <a name="solutions-options"></a>Opzioni di soluzioni
Informazioni sulle opzioni Big Compute in Azure e sulla scelta dell'approccio più adatto a soddisfare le esigenze del carico di lavoro e dell'azienda.

* [Batch e soluzioni HPC](batch-hpc-solutions.md)
* [Video: Big Compute nel cloud con Azure e HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Azure Batch
[Batch](https://azure.microsoft.com/services/batch/) è un servizio piattaforma che semplifica l'abilitazione cloud delle applicazioni Linux e Windows e l'esecuzione dei processi senza che sia necessario impostare e gestire un cluster e un'utilità di pianificazione dei processi. Usare l'SDK per integrare le applicazioni client con Azure Batch in vari linguaggi, organizzare i dati in Azure e creare le pipeline di esecuzione del processo.

* [Documentazione](https://azure.microsoft.com/documentation/services/batch/)
* Informazioni di riferimento su [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) e API [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
* [libreria Batch management .NET](https://msdn.microsoft.com/library/mt463120.aspx) 
* [Esercitazione: Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) e al [client Python di Batch](batch-python-tutorial.md)
* [Forum di Batch](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Video su Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>Soluzioni cluster di HPC
Distribuire o estendere il cluster HPC Windows o Linux esistente in Azure per eseguire carichi di lavoro a elevato utilizzo di calcolo.  

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
HPC Pack è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server, in grado di eseguire carichi di lavoro sia di Windows che di Linux HPC.  

* [Scaricare HPC Pack 2016](https://www.microsoft.com/download/details.aspx?id=54507)
* [Scaricare HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)
* [Documentazione](https://technet.microsoft.com/library/jj899572.aspx)
* Opzioni per il cluster HPC Pack in Azure: [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
* [Potenziare le istanze del ruolo di lavoro di Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Potenziare Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Forum di Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Soluzioni cluster Linux e OSS
Usare questi modelli di Azure per distribuire cluster HPC Linux.

* [Avviare un cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) e un [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Avviare un cluster Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Modelli di griglia di calcolo con PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>Archiviazione HPC
* [File system paralleli per l'archiviazione HPC in Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [Intel Cloud Edition per software Lustre - Valutazione](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [Modello BeeGFS su CentOS 7.2](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) è un'implementazione Microsoft dello standard Message Passing Interface per lo sviluppo e l'esecuzione di applicazioni parallele sulla piattaforma Windows.

* [Scaricare MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [Informazioni di riferimento su MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)
* [Forum di MPI](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>Istanze a elevato uso di calcolo
Azure offre [varie dimensioni di macchine virtuali](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), tra cui istanze [a elevato uso di calcolo serie H](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) che consentono di connettersi a una rete RDMA di back-end per eseguire carichi di lavoro HPC Linux e Windows. 

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurare un cluster di Windows RDMA con Microsoft HPC Pack per eseguire applicazioni MPI](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Per carichi di lavoro con elevato uso della GPU, vedere [NC e NV sizes](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) (Dimensioni NC e NV).

## <a name="samples-and-demos"></a>Esempi e demo
* [Esempi di codice di Azure Batch C# e Python](https://github.com/Azure/azure-batch-samples)
* Toolkit [Batch Shipyard](https://azure.github.io/batch-shipyard/) per semplificare la distribuzione dei carichi di lavoro Docker di tipo batch in Azure Batch
* Pacchetto [doAzureParallel](http://www.github.com/Azure/doAzureParallel) R, basato su Azure Batch
* [Versione di test di SUSE Linux Enterprise Server per HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## <a name="related-azure-services"></a>Servizi Azure correlati
* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Set di scalabilità di macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Servizi cloud](https://azure.microsoft.com/documentation/services/cloud-services/)
* [Servizio app](https://azure.microsoft.com/documentation/services/app-service/)
* [Servizi multimediali](https://azure.microsoft.com/documentation/services/media-services/)
* [Funzioni](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>Progetti dell'architettura
* [Orchestrazione di HPC e dati con Azure Batch e Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) e [articolo](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>Soluzioni di settore
* [Banche e mercati finanziari](https://finance.azure.com/)
* [Simulazioni di progettazione](https://simulation.azure.com/) 

## <a name="customer-stories"></a>Casi di successo dei clienti
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>Passaggi successivi
* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vedere anche le [novità di Batch](https://azure.microsoft.com/updates/?service=batch) o eseguire la sottoscrizione al [feed RSS](https://azure.microsoft.com/updates/feed/?service=batch).


