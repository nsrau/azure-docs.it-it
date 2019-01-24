---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 01/15/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7a136f34a7dd9fada23a4225b60223220c92c665
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440521"
---
Le organizzazioni hanno esigenze di elaborazione su vasta scala. Questi carichi di lavoro di Big Compute includono progettazione tecnica e analisi, calcoli di valutazione dei rischi finanziari, rendering delle immagini, modellazione complessa, simulazioni Monte Carlo e altri ancora. 

Usare il cloud di Azure per eseguire in modo efficiente carichi di lavoro Linux e Windows a elevato utilizzo di calcolo, dai processi batch paralleli alle tradizionali simulazioni HPC. Eseguire i carichi di lavoro HPC e batch nell'infrastruttura di Azure con una scelta personalizzata di servizi di calcolo, gestori grid, soluzioni del Marketplace e applicazioni ospitate da fornitori (SaaS). Azure offre soluzioni flessibili per distribuire il lavoro e aumentare le risorse fino a migliaia di VM o core, quindi ridurle quando sono necessarie meno risorse. 

## <a name="solution-options"></a>Soluzioni disponibili

* **Soluzioni fai da te**
    * Configurare l'ambiente cluster nelle macchine virtuali di Azure o nel [set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Trasferire un cluster locale in modalità lift-and-shift oppure distribuire un nuovo cluster in Azure per ottenere capacità aggiuntiva. 
    * Usare i modelli di Azure Resource Manager per distribuire [gestori di carichi di lavoro](#workload-managers), infrastruttura e [applicazioni](#hpc-applications). 
    * Scegliere [dimensioni di VM HPC e GPU](#hpc-and-gpu-sizes) che includono hardware specializzato e connessioni di rete per carichi di lavoro MPI o GPU. 
    * Aggiungere [archiviazione ad alte prestazioni](#hpc-storage) per carichi di lavoro con elevato numero di operazioni di I/O.
* **Soluzioni ibride**
    * Estendere la soluzione locale per l'offload dei carichi di lavoro di picco nell'infrastruttura di Azure
    * Usare il calcolo cloud su richiesta con il [gestore del carico di lavoro](#workload-manager) esistente.
    * Sfruttare [dimensioni di VM HPC e GPU](#hpc-and-gpu-sizes) per carichi di lavoro MPI o GPU.
* **Soluzioni di Big Compute distribuite come servizio**
    * Sviluppare soluzioni e flussi di lavoro personalizzati di Big Compute con [Azure CycleCloud](#azure-cyclecloud) e i [Azure Batch](#azure-batch) e i [servizi di Azure](#related-azure-services) correlati.
    * Eseguire le soluzioni di progettazione e simulazione abilitate per Azure di fornitori come [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) e [Cycle Computing](https://cyclecomputing.com/) (ora [rilevata da Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Usare un [supercomputer Cray](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) come servizio ospitato in Azure.
* **Soluzioni del Marketplace**
    * Usare la gamma di [applicazioni HPC](#hpc-applications) e [soluzioni](#marketplace-solutions) offerta in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 
    
Le sezioni seguenti offrono altre informazioni sulle tecnologie di supporto, oltre a collegamenti per materiale sussidiario.

## <a name="marketplace-solutions"></a>Soluzioni del Marketplace

Per immagini e soluzioni di VM Linux e Windows progettate per HPC, visitare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Tra gli esempi sono inclusi:

* [HPC basato su RogueWave CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview)
* [SUSE Linux Enterprise Server per HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [VM per l'analisi scientifica dei dati di Azure per Windows e Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

## <a name="hpc-applications"></a>Applicazioni HPC

Eseguire applicazioni HPC personalizzate o commerciali in Azure. Per alcuni esempi in questa sezione è disponibile un benchmark per una scalabilità efficiente con altre VM o core di calcolo. Visitare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) per le soluzioni pronte per la distribuzione.

> [!NOTE]
> Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo. Può essere necessario un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.

### <a name="engineering-applications"></a>Applicazioni tecniche

* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a>Grafica e rendering

* [Le applicazioni di rendering](../articles/batch/batch-rendering-service.md) in Azure Batch, tra cui Autodesk Maya, 3ds Max, Arnold, Chaos Group V-Ray e Blender

### <a name="ai-and-deep-learning"></a>AI e apprendimento avanzato

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Macchina virtuale per l'apprendimento avanzato](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Soluzioni di Batch Shipyard per l'apprendimento avanzato](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="hpc-and-gpu-vm-sizes"></a>Dimensioni di VM GPU e HPC

Azure offre una serie di dimensioni per le VM [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), incluse le dimensioni progettate per i carichi di lavoro a elevato utilizzo di calcolo. Le VM H16r e H16mr, ad esempio, possono connettersi a una rete RDMA back-end a velocità effettiva elevata. Questa rete cloud può migliorare le prestazioni delle applicazioni parallele strettamente associate in esecuzione in [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) o Intel MPI. 

Le VM serie N sono dotate di GPU con tecnologia NVIDIA progettate per applicazioni a elevato utilizzo di calcolo o di grafica, inclusi apprendimento e visualizzazione basati sull'intelligenza artificiale (AI). 

Altre informazioni:

* Dimensioni High Performance Computing per VM [Linux](../articles/virtual-machines/linux/sizes-hpc.md) e [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Dimensioni con supporto per GPU per VM [Linux](../articles/virtual-machines/linux/sizes-gpu.md) e [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

## <a name="azure-cyclecloud"></a>Azure CycleCloud

Gestisci in modo semplice ed efficace i carichi di lavoro comuni, creando e ottimizzando al tempo stesso cluster HPC sulle macchine virtuali di Azure con [Azure CycleCloud](https://docs.microsoft.com/azure/cyclecloud/overview).

È possibile passare agli argomenti seguenti:

* [Installare e configurare CycleCloud con un modello di Resource Manager](https://docs.microsoft.com/azure/cyclecloud/quickstart-install-cyclecloud)

* [Configurare manualmente CycleCloud](https://docs.microsoft.com/azure/cyclecloud/installation)

## <a name="azure-batch"></a>Azure Batch

[Batch](../articles/batch/batch-technical-overview.md) è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su un pool di macchine virtuali gestito e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi. 

I fornitori o sviluppatori di SaaS possono usare gli strumenti e gli SDK di Batch per integrare le applicazioni HPC o i carichi di lavoro dei contenitori con Azure, gestire temporaneamente i dati in Azure e creare pipeline per l'esecuzione di processi. 

È possibile passare agli argomenti seguenti:

* [Introduzione allo sviluppo con Batch](../articles/batch/quick-run-dotnet.md)
* [Usare gli esempi di codice per Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Usare le macchine virtuali con priorità bassa in Batch](../articles/batch/batch-low-pri-vms.md)
* [Eseguire carichi di lavoro HPC inclusi in contenitori con Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Eseguire carichi di lavoro R paralleli in Batch](https://github.com/Azure/doAzureParallel)
* [Eseguire processi Spark su richiesta in Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Gestori del carico di lavoro

Di seguito sono riportati esempi di cluster e gestori del carico di lavoro eseguibili nell'infrastruttura di Azure. Creare cluster autonomi nelle macchine virtuali di Azure oppure eseguire il potenziamento in macchine virtuali di Azure da un cluster locale. 
* [Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 

## <a name="hpc-storage"></a>Archiviazione HPC

I carichi di lavoro Batch e HPC su larga scala hanno richieste di archiviazione dati e di accesso che superano le capacità dei tradizionali file system cloud. 

Altre informazioni:

* [Avere vFXT per Azure](../articles/avere-vfxt/avere-vfxt-overview.md) 
* [File system virtuali paralleli in Azure](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/) inclusi [Lustre](http://lustre.org/) e [BeeGFS](http://www.beegfs.com/content/).

## <a name="related-azure-services"></a>Servizi Azure correlati

Le macchine virtuali di Azure, i set di scalabilità di macchine virtuali, Batch e i servizi di calcolo correlati sono alla base della maggior parte delle soluzioni HPC di Azure. La soluzione può tuttavia sfruttare molti servizi di Azure correlati. Ecco un elenco parziale:

### <a name="storage"></a>Archiviazione

* [Archivio BLOB, tabelle e code](../articles/storage/storage-introduction.md)
* [Archiviazione file](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dati e analisi

* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Storage Gen1](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [Database SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>Intelligenza artificiale e Machine Learning

* [Servizio Machine Learning](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Rete

* [Rete virtuale](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contenitori

* [Servizio Azure Kubernetes](../articles/aks/intro-kubernetes.md)
* [Registro Container](../articles/container-registry/container-registry-intro.md)
* [Istanze di Container](../articles/container-instances/container-instances-overview.md)

## <a name="customer-stories"></a>Casi di successo dei clienti

Esempi di clienti che hanno risolto problemi aziendali con le soluzioni HPC di Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&amp;C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://customers.microsoft.com/story/metlife-insurance-azure-cloud-services-windows-server-analytics-platform-server-en)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [NeuroInitiative](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
* [Schlumberger](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle soluzioni Azure per [High Performance Computing](https://azure.microsoft.com/solutions/high-performance-computing/), [rendering](https://azure.microsoft.com/solutions/big-compute/rendering/), [servizi bancari e mercati finanziari](https://finance.azure.com/) e [genomica](https://enterprise.microsoft.com/industries/health/genomics/).

* Altre informazioni sulle [architetture delle soluzioni Big Compute](https://azure.microsoft.com/solutions/architecture/?solution=high-performance-computing) in Azure.
