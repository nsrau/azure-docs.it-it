Le organizzazioni hanno esigenze di elaborazione su vasta scala. Questi carichi di lavoro di Big Compute includono progettazione tecnica e analisi, calcoli di valutazione dei rischi finanziari, rendering delle immagini, modellazione complessa, simulazioni Monte Carlo e altri ancora. 

Con Azure, è possibile eseguire i carichi di lavoro HPC (High Performance Computing) e di Batch in cluster di macchine virtuali di Azure, usando i servizi di infrastruttura di Azure o i servizi gestiti preferiti. Azure offre la flessibilità necessaria per ridimensionare le risorse di calcolo fino a migliaia di VM o di core e quindi passare a un piano inferiore quando sono necessarie meno risorse. 

Le soluzioni HPC di Azure eseguono in modo efficiente carichi di lavoro Linux e Windows a elevato utilizzo di calcolo, dai processi batch paralleli alle tradizionali simulazioni HPC. Le principali applicazioni HPC sono supportate per l'esecuzione in VM di Azure ed è possibile sfruttare dimensioni e immagini di VM specializzate progettate per processi a elevato utilizzo di calcolo. 


## <a name="solution-architectures"></a>Architetture delle soluzioni

Le architetture delle soluzioni a elevato utilizzo di calcolo di esempio in Azure includono:

* Eseguire applicazioni HPC macchine virtuali o [set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md), usando un cluster Linux o Windows o un gestore di griglie di propria scelta [Altre informazioni](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* Creare soluzioni ibride che estendono un cluster HPC locale per ripartire i carichi di lavoro di picco nelle VM di Azure [Altre informazioni](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/)
* Usare il servizio Azure [Batch](https://azure.microsoft.com/services/batch/) gestito e scalabile per eseguire carichi di lavoro a elevato utilizzo di calcolo, senza gestire l'infrastruttura sottostante [Altre informazioni](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)

## <a name="hpc-cluster-and-grid-solutions"></a>Soluzioni cluster e griglia HPC
Distribuire o estendere strumenti di gestione del carico di lavoro HPC noti in macchine virtuali di Azure ed eseguire i carichi di lavoro a elevato utilizzo di calcolo. Le opzioni includono strumenti open source, ad esempio [Slurm workload manager](https://slurm.schedmd.com/), e gestori di cluster o griglie di Microsoft e altri editori.

### <a name="linux-and-oss-cluster-solutions"></a>Soluzioni cluster Linux e OSS
Usare i modelli di Azure Resource Manager per distribuire le soluzioni cluster HPC Linux in macchine virtuali o set di scalabilità di macchine virtuali, tra cui:

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

Vedere anche la raccolta di [modelli in 5 clic](https://github.com/tanewill/5clickTemplates).

### <a name="grid-managers"></a>Gestori di griglie
Partner Microsoft con gestori di griglie commerciali per rendere le soluzioni disponibili nelle VM di Azure. Tra gli esempi sono inclusi:

* [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
[HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server. HPC Pack offre diverse opzioni di esecuzione in VM [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

È possibile passare agli argomenti seguenti:

* [Creare un cluster HPC Pack 2016 in Azure](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [Burst to Azure with HPC Pack](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx) (Espansione in Azure con HPC Pack)




## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su un pool di macchine virtuali gestito e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi. 

Usare gli strumenti e gli SDK di Batch per integrare le applicazioni HPC o i carichi di lavoro dei contenitori con Azure, inserire temporaneamente i dati in Azure e creare pipeline per le esecuzioni di processi. È anche possibile usare la capacità delle VM di Azure in eccedenza (priorità bassa) a un [prezzo ridotto](https://azure.microsoft.com/pricing/details/batch/), diminuendo considerevolmente il costo di esecuzione di alcuni carichi di lavoro in Batch.

È possibile passare agli argomenti seguenti:

* [Introduzione allo sviluppo con Batch](../articles/batch/batch-dotnet-get-started.md)
* [Usare gli esempi di codice per Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Usare le VM con priorità bassa in Batch (anteprima)](../articles/batch/batch-low-pri-vms.md)
* [Eseguire carichi di lavoro HPC inclusi in contenitori con Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Usare il linguaggio R con Batch](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>Dimensioni di VM GPU e HPC
Azure offre una serie di dimensioni per le VM [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), incluse le dimensioni progettate per i carichi di lavoro a elevato utilizzo di calcolo. Le VM H16r e H16mr, ad esempio, possono connettersi a una rete RDMA back-end a velocità effettiva elevata. Questa rete cloud può migliorare le prestazioni delle applicazioni parallele strettamente associate in esecuzione in [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) o Intel MPI. Le VM serie N sono dotate di GPU con tecnologia NVIDIA progettate per applicazioni a elevato utilizzo di calcolo o di grafica, inclusi apprendimento e visualizzazione basati sull'intelligenza artificiale (AI). 

Altre informazioni:

* Dimensioni High Performance Computing per VM [Linux](../articles/virtual-machines/linux/sizes-hpc.md) e [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Dimensioni con supporto per GPU per VM [Linux](../articles/virtual-machines/linux/sizes-gpu.md) e [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

È possibile passare agli argomenti seguenti:

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurare un cluster di Windows RDMA con Microsoft HPC Pack per eseguire applicazioni MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Usare VM a elevato utilizzo di calcolo in pool di Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>Immagini HPC

Per le immagini di VM Linux e Windows progettate per HPC, visitare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Tra gli esempi sono inclusi:

* [HPC basato su RogueWave CentOS](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server per HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [VM per l'analisi scientifica dei dati di Azure per Windows e Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>Applicazioni HPC

Eseguire applicazioni HPC personalizzate o commerciali in Azure. Di alcune viene effettuato un benchmark per una scalabilità efficiente con altre VM o core di calcolo. Le sezioni seguenti sono esempi.

> [!IMPORTANT]
> Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo. Può essere necessario un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.

### <a name="engineering-applications"></a>Applicazioni tecniche


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafica e rendering

* [Autodesk Maya, 3ds Max e Arnold](../articles/batch/batch-rendering-service.md) in Azure Batch (anteprima)

### <a name="ai-and-deep-learning"></a>AI e apprendimento avanzato

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Deep learning toolkit for Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Soluzioni di Batch Shipyard per l'apprendimento avanzato](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>Partner di soluzioni

I partner che sviluppano o distribuiscono applicazioni Batch e HPC in Azure includono:

* [Cycle Computing](https://cyclecomputing.com/) (ora [acquisita da Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>Archiviazione HPC

I carichi di lavoro Batch e HPC su larga scala hanno richieste di archiviazione dati e di accesso che superano le capacità dei tradizionali file system cloud. È possibile implementare soluzioni di file system parallele in Azure, ad esempio [Lustre](http://lustre.org/) e [BeeGFS](http://www.beegfs.com/content/).

Altre informazioni:

* [File system paralleli per l'archiviazione HPC in Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Servizi Azure correlati

Le macchine virtuali di Azure, i set di scalabilità di macchine virtuali, Batch e i servizi di calcolo correlati sono alla base della maggior parte delle soluzioni HPC di Azure. La soluzione può tuttavia sfruttare molti servizi di Azure correlati. Ecco un elenco parziale:

### <a name="storage"></a>Archiviazione

* [Archivio BLOB, tabelle e code](../articles/storage/storage-introduction.md)
* [Archiviazione file](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dati e analisi
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) per cluster Hadoop in Azure
* [Data Factory](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Database SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Rete
* [Rete virtuale](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contenitori
* [Servizio contenitore](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Registro contenitori](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Casi di successo dei clienti

Di seguito sono riportati esempi di clienti che hanno risolto i problemi aziendali con le soluzioni HPC di Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle soluzioni Big Compute per [simulazione di progettazione](https://simulation.azure.com/), [rendering](https://simulation.azure.com/) e [servizi bancari e mercati finanziari](https://finance.azure.com/).
* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).


