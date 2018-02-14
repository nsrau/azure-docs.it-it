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
    * Sviluppare soluzioni e flussi di lavoro di Big Compute con [Azure Batch](#azure-batch) e i [servizi di Azure](#related-azure-services) correlati.
    * Eseguire le soluzioni di progettazione e simulazione abilitate per Azure di fornitori come [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) e [Cycle Computing](https://cyclecomputing.com/) (ora [rilevata da Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
* **Soluzioni del Marketplace**
    * Usare la gamma di [applicazioni HPC](#hpc-applications) e [soluzioni](#marketplace-solutions) offerta in [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


Le sezioni seguenti offrono altre informazioni sulle tecnologie di supporto, oltre a collegamenti per materiale sussidiario.



## <a name="marketplace-solutions"></a>Soluzioni del Marketplace

Per immagini e soluzioni di VM Linux e Windows progettate per HPC, visitare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Tra gli esempi sono inclusi:

* [HPC basato su RogueWave CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server per HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [VM per l'analisi scientifica dei dati di Azure per Windows e Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition per software Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Applicazioni HPC

Eseguire applicazioni HPC personalizzate o commerciali in Azure. Per alcuni esempi in questa sezione è disponibile un benchmark per una scalabilità efficiente con altre VM o core di calcolo. Visitare [Azure Marketplace](https://marketplace.azure.com) per le soluzioni pronte per la distribuzione.

> [!NOTE]
> Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo. Può essere necessario un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.

### <a name="engineering-applications"></a>Applicazioni tecniche


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Grafica e rendering

* [Autodesk Maya, 3ds Max e Arnold](../articles/batch/batch-rendering-service.md) in Azure Batch 

### <a name="ai-and-deep-learning"></a>AI e apprendimento avanzato

* Evento di formazione [Intelligenza artificiale per Batch](../articles/batch-ai/overview.md) per i modelli di apprendimento avanzato
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Macchina virtuale per l'apprendimento avanzato](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Soluzioni di Batch Shipyard per l'apprendimento avanzato](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Dimensioni di VM GPU e HPC
Azure offre una serie di dimensioni per le VM [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), incluse le dimensioni progettate per i carichi di lavoro a elevato utilizzo di calcolo. Le VM H16r e H16mr, ad esempio, possono connettersi a una rete RDMA back-end a velocità effettiva elevata. Questa rete cloud può migliorare le prestazioni delle applicazioni parallele strettamente associate in esecuzione in [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) o Intel MPI. 

Le VM serie N sono dotate di GPU con tecnologia NVIDIA progettate per applicazioni a elevato utilizzo di calcolo o di grafica, inclusi apprendimento e visualizzazione basati sull'intelligenza artificiale (AI). 

Altre informazioni:

* Dimensioni High Performance Computing per VM [Linux](../articles/virtual-machines/linux/sizes-hpc.md) e [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Dimensioni con supporto per GPU per VM [Linux](../articles/virtual-machines/linux/sizes-gpu.md) e [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

È possibile passare agli argomenti seguenti:

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurare un cluster di Windows RDMA con Microsoft HPC Pack per eseguire applicazioni MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Usare VM a elevato utilizzo di calcolo in pool di Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su un pool di macchine virtuali gestito e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi. 

I fornitori o sviluppatori di SaaS possono usare gli strumenti e gli SDK di Batch per integrare le applicazioni HPC o i carichi di lavoro dei contenitori con Azure, gestire temporaneamente i dati in Azure e creare pipeline per l'esecuzione di processi. 

È possibile passare agli argomenti seguenti:

* [Introduzione allo sviluppo con Batch](../articles/batch/quick-run-dotnet.md)
* [Usare gli esempi di codice per Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Usare le macchine virtuali con priorità bassa in Batch](../articles/batch/batch-low-pri-vms.md)
* [Eseguire carichi di lavoro HPC inclusi in contenitori con Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Usare il linguaggio R con Batch](https://github.com/Azure/doAzureParallel)
* [Eseguire Azure Distributed Data Engineering Toolkit in Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Gestori del carico di lavoro

Di seguito sono riportati esempi di cluster e gestori del carico di lavoro eseguibili nell'infrastruttura di Azure. Creare cluster autonomi nelle macchine virtuali di Azure oppure eseguire il potenziamento in macchine virtuali di Azure da un cluster locale. 
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx): vedere le opzioni per l'esecuzione nelle macchine virtuali [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 



## <a name="hpc-storage"></a>Archiviazione HPC

I carichi di lavoro Batch e HPC su larga scala hanno richieste di archiviazione dati e di accesso che superano le capacità dei tradizionali file system cloud. Implementare soluzioni di file system parallele in Azure, ad esempio [Lustre](http://lustre.org/) e [BeeGFS](http://www.beegfs.com/content/).

Altre informazioni:

* [File system paralleli per l'archiviazione HPC in Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* Soluzioni di archiviazione cloud a prestazioni elevate di [Avere](http://www.averesystems.com/about-us/about-avere) (ora [associata a Microsoft](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))


## <a name="related-azure-services"></a>Servizi Azure correlati

Le macchine virtuali di Azure, i set di scalabilità di macchine virtuali, Batch e i servizi di calcolo correlati sono alla base della maggior parte delle soluzioni HPC di Azure. La soluzione può tuttavia sfruttare molti servizi di Azure correlati. Ecco un elenco parziale:

### <a name="storage"></a>Archiviazione

* [Archivio BLOB, tabelle e code](../articles/storage/storage-introduction.md)
* [Archiviazione file](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dati e analisi
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Database SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-cognitive-services"></a>Intelligenza artificiale e servizi cognitivi
* [Intelligenza artificiale per Batch](../articles/batch-ai/overview.md)

### <a name="networking"></a>Rete
* [Rete virtuale](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contenitori
* [Servizio contenitore](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Registro contenitori](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Casi di successo dei clienti

Di seguito sono riportati esempi di clienti che hanno risolto i problemi aziendali con le soluzioni HPC di Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&amp;C](https://customers.microsoft.com/story/axa-global-p-and-c)
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
* Altre informazioni sulle soluzioni Big Compute per [simulazione di progettazione](https://simulation.azure.com/), [rendering](https://simulation.azure.com/), [servizi bancari e mercati finanziari](https://finance.azure.com/) e [genomica](https://enterprise.microsoft.com/en-us/industries/health/genomics/).
* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Usare il servizio Azure [Batch](https://azure.microsoft.com/services/batch/) gestito e scalabile per eseguire carichi di lavoro a elevato utilizzo di calcolo, senza gestire l'infrastruttura sottostante [Altre informazioni](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)



