<properties
   pageTitle="Risorse per carichi di lavoro batch e HPC nel cloud | Microsoft Azure"
   description="In questo articolo vengono elencate le risorse tecniche per consentire l'esecuzione di carichi di lavoro paralleli, batch e HPC (high performance computing) su larga scala in Azure."
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
   ms.date="01/26/2016"
   ms.author="danlep"/>

# Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)
Questa guida alle risorse tecniche consente di eseguire carichi di lavoro paralleli, batch e HPC su larga scala in Azure. La vasta gamma di servizi Azure consente di estendere il batch esistente o i carichi di lavoro HPC nel cloud Azure o creare nuove soluzioni Big Compute in Azure.

## Opzioni di soluzioni

Informazioni sulle opzioni Big Compute in Azure e sulla scelta dell'approccio più adatto a soddisfare le esigenze del carico di lavoro e dell'azienda.

* [Batch e soluzioni HPC](batch-hpc-solutions.md)

* [Video: Big Compute nel cloud con Azure e HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) è un servizio di piattaforma che semplifica l'abilitazione cloud delle applicazioni e l'esecuzione dei processi senza che sia necessario impostare e gestire un cluster e uno strumento di pianificazione dei processi. Usare l'SDK per integrare le applicazioni client con Azure Batch in una varietà di linguaggi, organizzare i dati in Azure e creare le pipeline di esecuzione del processo.

* [Documentazione](https://azure.microsoft.com/documentation/services/batch/)

* Informazioni di riferimento su [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) e sull'API [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* [Esercitazione: Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)

* [Forum di Batch](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurebatch)

* [Video su Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluzioni cluster di HPC

Distribuire o estendere il cluster HPC Windows o Linux esistente in Azure per eseguire carichi di lavoro a elevato utilizzo di calcolo.

### Microsoft HPC Pack

HPC Pack è la soluzione HPC gratuita di Microsoft basata sulle tecnologie di Microsoft Azure e Windows Server, in grado di eseguire carichi di lavoro sia di Windows che di Linux HPC.

* [Scaricare HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [Documentazione](https://technet.microsoft.com/library/jj899572.aspx)


* [Opzioni per cluster HPC con Microsoft HPC Pack in Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Potenziare le istanze di lavoro di Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Potenziare Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Forum di Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluzioni cluster Linux e OSS

Usare questi modelli di Guida introduttiva di Azure per distribuire cluster HPC Linux con strumenti open source.

* [Avviare un cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Avviare un cluster Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) è un'implementazione Microsoft dello standard Message Passing Interface per lo sviluppo e l'esecuzione di applicazioni parallele sulla piattaforma Windows.


* [Scaricare MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informazioni di riferimento su MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum di MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Istanze con utilizzo intensivo di calcolo

Azure offre una [gamma di dimensioni](../virtual-machines/virtual-machines-size-specs.md), tra cui istanze [A8 e A9](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) a elevato utilizzo di calcolo in grado di connettersi a una rete RDMA back-end, per eseguire carichi di lavoro Linux e Windows HPC.

* [Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Configurare un cluster di Windows RDMA con Microsoft HPC Pack per eseguire applicazioni MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Progetti dell'architettura

* [Orchestrazione di HPC e dati con Azure Batch e Data factory di Azure](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) e [articolo](../data-factory/data-factory-data-processing-using-batch.md)

## Esempi e demo

* [Esempi di codice di Azure Batch](https://github.com/Azure/azure-batch-samples)

## Servizi Azure correlati

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Servizi cloud](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Servizi multimediali](https://azure.microsoft.com/documentation/services/media-services/)



## Passaggi successivi

* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vedere anche le [novità di Batch](https://azure.microsoft.com/updates/?service=batch) o eseguire la sottoscrizione al [feed RSS](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0204_2016-->