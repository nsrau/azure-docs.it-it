<properties
   pageTitle="Big Compute: Risorse tecniche per Batch e HPC (High Performance Computing) | Microsoft Azure"
   description="In questo articolo vengono elencate le risorse tecniche per consentire l'esecuzione di carichi di lavoro paralleli, batch e HPC su larga scala in Azure."
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
   ms.date="07/14/2015"
   ms.author="danlep"/>

# Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)
Questa guida alle risorse tecniche consente di eseguire carichi di lavoro paralleli, batch e HPC su larga scala in Azure. La vasta gamma di servizi Azure consente di estendere il batch esistente o i carichi di lavoro HPC nel cloud Azure o creare nuove soluzioni Big Compute in Azure.

## Opzioni di soluzioni

Informazioni sulle opzioni Big Compute in Azure e sulla scelta dell'approccio più adatto a soddisfare le esigenze del carico di lavoro e dell'azienda.

* [Batch e soluzioni HPC](batch-hpc-solutions.md)

* [Video: Big Compute nel cloud con Azure e HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) è un servizio di piattaforma che semplifica l'abilitazione cloud delle applicazioni e l'esecuzione dei processi senza che sia necessario impostare e gestire un cluster e uno strumento di pianificazione dei processi. Usare l'SDK per integrare le applicazioni client con Azure Batch in una varietà di linguaggi, organizzare i dati in Azure e creare le pipeline di esecuzione del processo.

* [Documentazione](http://azure.microsoft.com/documentation/services/batch/)

* [Informazioni di riferimento sulle API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Forum di Batch](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Esercitazione: Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)

* [Video su Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluzioni cluster di HPC

Distribuire o estendere il cluster HPC Windows o Linux esistente in Azure per eseguire carichi di lavoro a elevato utilizzo di calcolo.

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) è una soluzione gratuita di programmazione dei processi e di gestione del cluster Microsoft per tecnologie HPC basate su cloud, ibride e locali.

* [Potenziare Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack nelle macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Esercitazione: Configurare un cluster ibrido con HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Forum di Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluzioni cluster Linux
Usare questi modelli di Gestione risorse di Azure per distribuire cluster HPC Linux.

* [Avviare un cluster SLURM](http://azure.microsoft.com/documentation/templates/slurm/) e [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Avviare un cluster Torque](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) è un'implementazione Microsoft dello standard Message Passing Interface per lo sviluppo e l'esecuzione di applicazioni parallele sulla piattaforma Windows.


* [Scaricare MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informazioni di riferimento su MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum di MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Istanze con utilizzo intensivo di calcolo

Azure offre una [gamma di dimensioni](../virtual-machines/virtual-machines-size-specs.md), tra cui [Istanze A8, A9, A10 e A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) a elevato utilizzo di calcolo, per eseguire carichi di HPC in Linux e Windows.

* [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Istanze A8 e A9: Avvio rapido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Eseguire applicazioni MPI in istanze A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Progetti dell'architettura

* [Elaborazione su larga scala - Servizi finanziari](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) illustra come rendere operativi e orchestrare l'analisi dei dati e i calcoli su larga scala nel cloud per la gestione dei rischi, la creazione di report e le simulazioni.

## Esempi e demo

* [Esempi di codice di Azure Batch](https://github.com/Azure/azure-batch-samples)

* [Esempio di Blender per Batch Apps](https://github.com/Azure/azure-batch-apps-blender) e [post di blog](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Servizi Azure correlati

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Macchine virtuali](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Servizi cloud](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Servizi multimediali](http://azure.microsoft.com/documentation/services/media-services/)



## Passaggi successivi

* Per gli annunci più recenti, vedere il [blog del team di Microsoft HPC e Batch](http://blogs.technet.com/b/windowshpc/) e il [blog di Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Vedere anche le [novità di Batch](http://azure.microsoft.com/updates/?service=batch) o eseguire la sottoscrizione al [feed RSS](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=July15_HO4-->