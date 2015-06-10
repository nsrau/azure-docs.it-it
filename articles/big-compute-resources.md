<properties
   pageTitle="Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)"
   description="Questo articolo elenca risorse tecniche per consentire l'esecuzione di carichi di lavoro paralleli, batch e HPC su larga scala in Azure."
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
   ms.date="04/28/2015"
   ms.author="danlep"/>

# Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)
Questa guida alle risorse tecniche consente di eseguire carichi di lavoro paralleli, batch e HPC su larga scala in Azure. La vasta gamma di servizi Azure consente di estendere il batch esistente o i carichi di lavoro HPC nel cloud Azure o creare nuove soluzioni Big Compute in Azure.

## Opzioni di soluzioni


Informazioni sulle opzioni Big Compute in Azure e sulla scelta dell'approccio più adatto a soddisfare le esigenze del carico di lavoro e dell'azienda.

* [Panoramica](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Video: Big Compute nel cloud con Azure e HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) (anteprima) è un servizio di piattaforma che semplifica l'abilitazione cloud delle applicazioni ed esegue processi senza impostare e gestire un cluster e la pianificazione dei processi. Usare l'SDK per integrare le applicazioni client con Azure Batch in una varietà di linguaggi, organizzare i dati in Azure e creare le pipeline di esecuzione del processo.

* Iscriversi per l'[anteprima](http://azure.microsoft.com/services/preview/)

* [Documentazione](http://azure.microsoft.com/documentation/services/batch/)

* [Informazioni di riferimento sulle API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Forum di Batch](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Esercitazione: Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md)

* [Video: Introduzione ad Azure Batch](http://azure.microsoft.com/documentation/videos/introduction-to-azure-batch-with-mark-scurrell/)

* [Video: Demo del servizio Azure Batch di Mark Russinovich](http://azure.microsoft.com/documentation/videos/azure-batch-demo-blender/)

* [Video: Creare un servizio Batch](http://azure.microsoft.com/documentation/videos/azure-batch-build-a-batch-service/)

## Soluzioni cluster di HPC

Eseguire la migrazione o estendere il cluster HPC Windows esistente per l'esecuzione di carichi di lavoro con utilizzo intensivo di calcolo in Azure.

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) è una soluzione gratuita di programmazione dei processi e di gestione del cluster Microsoft per tecnologie HPC basate su cloud, ibride e locali.

* [Potenziare Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack nelle macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Esercitazione: Configurare un cluster ibrido con HPC Pack in Azure](cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Forum di Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) è un'implementazione Microsoft dello standard Message Passing Interface per lo sviluppo e l'esecuzione di applicazioni parallele sulla piattaforma Windows.


* [Scaricare MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Informazioni di riferimento su MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Forum di MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Istanze con utilizzo intensivo di calcolo

Azure offre una [gamma di dimensioni](https://msdn.microsoft.com/library/azure/dn197896.aspx), tra cui [istanze A8, A9, A10 e A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), per eseguire carichi di lavoro Batch e HPC.


* [Istanze A8 e A9: Avvio rapido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Eseguire applicazioni MPI in istanze A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Progetti dell'architettura

* [Elaborazione su larga scala - Servizi finanziari](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) illustra come rendere operativi e orchestrare l'analisi dei dati e i calcoli su larga scala nel cloud per la gestione dei rischi, la creazione di report e le simulazioni.

## Esempi e demo

* [Esempi di codice di Azure Batch](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch)

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

<!---HONumber=58-->