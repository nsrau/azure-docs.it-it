<properties
   pageTitle="Risorse per carichi di lavoro batch e HPC nel cloud | Microsoft Azure"
   description="Questo articolo fornisce un elenco delle risorse tecniche che permettono di eseguire carichi di lavoro paralleli, batch e HPC (High Performance Computing) su larga scala in Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/22/2016"
   ms.author="danlep"/>

# Big Compute in Azure: risorse tecniche per Batch e High Performance Computing 
Si tratta di una guida alle risorse tecniche che permettono di eseguire carichi di lavoro paralleli, batch e HPC (High Performance Computing) su larga scala in Azure. La vasta gamma di servizi Azure consente di estendere il batch esistente o i carichi di lavoro HPC nel cloud Azure o creare nuove soluzioni Big Compute.

## Opzioni di soluzioni

Informazioni sulle opzioni Big Compute in Azure e sulla scelta dell'approccio più adatto a soddisfare le esigenze del carico di lavoro e dell'azienda.

* [Batch e soluzioni HPC](batch-hpc-solutions.md)

* [Video: Big Compute nel cloud con Azure e HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) è un servizio piattaforma che semplifica l'abilitazione cloud delle applicazioni Linux e Windows e l'esecuzione dei processi senza che sia necessario impostare e gestire un cluster e un'utilità di pianificazione dei processi. Usare l'SDK per integrare le applicazioni client con Azure Batch in vari linguaggi, organizzare i dati in Azure e creare le pipeline di esecuzione del processo.

* [Documentazione](https://azure.microsoft.com/documentation/services/batch/)

* Informazioni di riferimento su [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) e API [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* Informazioni di riferimento sulla [libreria Batch management .NET](https://msdn.microsoft.com/library/mt463120.aspx)

* [Esercitazione: Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) e al [client Python di Batch](batch-python-tutorial.md)

* [Forum di Batch](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurebatch)

* [Video su Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluzioni cluster di HPC

Distribuire o estendere il cluster HPC Windows o Linux esistente in Azure per eseguire carichi di lavoro a elevato utilizzo di calcolo.

### Microsoft HPC Pack

HPC Pack è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server, in grado di eseguire carichi di lavoro sia di Windows che di Linux HPC.

* [Scaricare HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [Documentazione](https://technet.microsoft.com/library/jj899572.aspx)


* Opzioni cluster per HPC Pack per [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) e [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) in Azure

* [Potenziare le istanze del ruolo di lavoro di Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Potenziare Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Forum di Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluzioni cluster Linux e OSS

Usare questi modelli di Azure per distribuire cluster HPC Linux.

* [Avviare un cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Avviare un cluster Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Intel Cloud Edition per software Lustre - Valutazione](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) è un'implementazione Microsoft dello standard Message Passing Interface per lo sviluppo e l'esecuzione di applicazioni parallele sulla piattaforma Windows. La versione più recente è MS-MPI v7.


* [Scaricare MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informazioni di riferimento su MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum di MPI](https://social.microsoft.com/Forums/it-IT/home?forum=windowshpcmpi)

## Istanze a elevato uso di calcolo

Azure offre [varie dimensioni di VM](../virtual-machines/virtual-machines-windows-sizes.md), tra cui istanze [a elevato uso di calcolo](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) che consentono di connettersi a una rete RDMA di back-end per eseguire carichi di lavoro HPC Linux e Windows.


* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)

* [Configurare un cluster di Windows RDMA con Microsoft HPC Pack per eseguire applicazioni MPI](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)



## Esempi e demo

* [Esempi di codice di Azure Batch C# e Python](https://github.com/Azure/azure-batch-samples)

* Kit di strumenti [Batch Shipyard](https://azure.github.io/batch-shipyard/) per semplificare la distribuzione dei carichi di lavoro portuali di tipo batch

* [Versione di test di SUSE Linux Enterprise Server per HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## Servizi Azure correlati

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Set di scalabilità di macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)

* [Servizi cloud](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Servizio app](https://azure.microsoft.com/documentation/services/app-service/)

* [Servizi multimediali](https://azure.microsoft.com/documentation/services/media-services/)

* [Funzioni](https://azure.microsoft.com/documentation/services/functions/)

## Progetti dell'architettura

* [Orchestrazione di HPC e dati con Azure Batch e Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) e [articolo](../data-factory/data-factory-data-processing-using-batch.md)

## Soluzioni di settore

* [Banche e mercati finanziari](https://finance.azure.com/)

* [Simulazioni di progettazione](https://simulation.azure.com/)

## Casi di successo dei clienti

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



## Passaggi successivi

* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vedere anche le [novità di Batch](https://azure.microsoft.com/updates/?service=batch) o eseguire la sottoscrizione al [feed RSS](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0928_2016-->