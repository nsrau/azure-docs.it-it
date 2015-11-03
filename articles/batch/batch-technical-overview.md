<properties
	pageTitle="Panoramica tecnica di Azure Batch | Microsoft Azure"
	description="Informazioni su concetti, flussi di lavoro e scenari del servizio Azure Batch per carichi di lavoro HPC e paralleli su larga scala"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/26/2015"
	ms.author="danlep"/>


# Panoramica tecnica di Azure Batch per carichi di lavoro paralleli e HPC su larga scala
Azure Batch semplifica l'esecuzione di applicazioni parallele e HPC (High Performance Computing) su larga scala e in modo efficiente nel cloud. È un servizio di piattaforma che pianifica l'esecuzione del lavoro caratterizzato da un utilizzo intensivo di calcolo su una raccolta gestita di macchine virtuali (nodi di calcolo) e può ridimensionare le risorse di calcolo per soddisfare le esigenze del processo. Con il servizio Batch si definiscono a livello di codice le risorse di calcolo di Azure e i processi batch su larga scala eseguiti su richiesta o in base a una pianificazione, senza dover configurare e gestire manualmente un cluster HPC, singole macchine virtuali, reti virtuali o un'utilità di pianificazione di processi.

## Casi d'uso

Batch è un servizio gestito per l'*elaborazione batch* o *batch computing* che esegue un volume elevato di attività simili per ottenere il risultato desiderato. Batch computing è un modello comune per le organizzazioni che elaborano, trasformano e analizzano elevate quantità di dati, sia in base a una pianificazione che su richiesta, con esempi in campi che spaziano dai servizi finanziari all'ingegneria.

Batch funziona bene con applicazioni o carichi di lavoro intrinsecamente paralleli (a volte definiti "imbarazzantemente paralleli"), che si prestano all'esecuzione come attività parallele su più computer. Vedere la figura 1.

![Attività parallele][parallel]

**Figura 1. Attività parallele in esecuzione su più computer**

Tra gli esempi sono inclusi:

* Modellazione dei rischi finanziari
* Rendering ed elaborazione di immagini
* Codifica e transcodifica multimediale
* Analisi delle sequenze genetiche
* Test di software

Batch può anche eseguire calcoli paralleli con un passaggio di riduzione alla fine e altri carichi di lavoro paralleli più complicati.

>[AZURE.NOTE]Attualmente Batch supporta solo i carichi di lavoro eseguiti su macchine virtuali basate su Windows Server. Inoltre, Batch non supporta attualmente le applicazioni MPI (Message Passing Interface).

Per un confronto tra Batch e altre soluzioni HPC in Azure, vedere [Batch e soluzioni HPC](batch-hpc-solutions.md).

## Sviluppo con Batch

Sviluppare con le API Batch per creare e gestire pool di nodi di calcolo e per pianificare i processi e le attività eseguite su di essi. Scrivere applicazioni client o front-end per l'esecuzione di processi e attività su richiesta, in base a una pianificazione o come parte di un flusso di lavoro più esteso gestito tramite strumenti come [Data factory di Azure](https://azure.microsoft.com/documentation/services/data-factory/).

Per altre informazioni su Batch, vedere [Nozioni di base delle API per Azure Batch](batch-api-basics.md).

### Account necessari

+ **Account e sottoscrizione di Azure**: se non si ha un account, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

+ **Account Batch**: usare il nome e l'URL di un account Batch e una chiave di accesso come credenziali quando si eseguono chiamate API in Batch. Tutte le risorse di Batch, ad esempio nodi di calcolo, pool, processi e attività, sono associate a un account Batch. Un modo per creare un account Batch e gestire le chiavi di accesso per l'account consiste nell'usare il [portale di anteprima di Azure](batch-account-create-portal.md).

+ **Account di archiviazione**: per la maggior parte degli scenari di Batch è necessario un account di archiviazione di Azure per archiviare i dati di input e output e gli script o gli eseguibili che vengono eseguiti nei nodi di calcolo. Per creare un account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

### Strumenti e librerie di sviluppo per Batch

Usare questi strumenti e librerie .NET con Visual Studio per sviluppare e gestire applicazioni Azure Batch.

+ [Libreria client .NET per Batch](http://www.nuget.org/packages/Azure.Batch/) (NuGet): sviluppare applicazioni client per eseguire processi con il servizio Batch.
+ [Libreria di gestione .NET per Batch](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet): gestire gli account Batch.
+ [Batch Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub): compilare questa applicazione GUI per sfogliare, accedere e aggiornare le risorse in un account Batch, inclusi processi e attività, nodi di calcolo e pool, oltre a file in un nodo di calcolo. Vedere il [post di blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Scenario: scalare orizzontalmente un carico di lavoro parallelo

Uno scenario comune con le API Batch comporta la scalabilità orizzontale del lavoro intrinsecamente parallelo, ad esempio il rendering delle immagini in un pool di nodi di calcolo fornendo fino a migliaia di core di calcolo.

La figura 2 mostra un flusso di lavoro che usa un'applicazione client .NET per Batch per eseguire un carico di lavoro parallelo con Batch.


![Flusso di lavoro degli elementi di lavoro][work_item_workflow]

**Figura 2. Scalare orizzontalmente un carico di lavoro parallelo in Batch**

1.	Caricare i file di input (ad esempio i dati o le immagini di origine) necessari per un processo in un account di archiviazione di Azure. Il servizio Batch carica i file nei nodi di calcolo durante l'esecuzione delle attività di processo.

2.	Caricare il file di programma o gli script che verranno eseguiti nei nodi di calcolo nell'account di archiviazione. Potrebbero essere inclusi file binari e i relativi assembly dipendenti. Il servizio Batch carica anche questi file nei nodi di calcolo durante l'esecuzione delle attività.

3.	Creare a livello di codice un pool di nodi di calcolo Batch nell'account Batch, specificando le proprietà, ad esempio le dimensioni e il sistema operativo che eseguono. È anche possibile definire come [aumentare o ridurre](batch-automatic-scaling.md) il numero di nodi nel pool in risposta al carico di lavoro. Quando si esegue un'attività, a quest'ultima viene assegnato un nodo dal pool.

4.	A livello di codice, definire un processo Batch per eseguire il carico di lavoro nel pool di nodi.

5.	Aggiungere attività al processo. Ciascuna attività usa il programma caricato per elaborare le informazioni presenti in un file caricato. A seconda del carico di lavoro, è possibile eseguire [più attività contemporaneamente](batch-parallel-node-tasks.md) in ogni nodo di calcolo per ottimizzare l'utilizzo delle risorse. Batch supporta anche [attività di preparazione e completamento del processo](batch-job-prep-release.md) specializzate per preparare i nodi di calcolo prima dell'esecuzione delle operazioni pianificate e per eseguire la pulizia in seguito.

6.	Eseguire il carico di lavoro del servizio Batch e monitorare l'avanzamento e i risultati. L'applicazione comunica tramite HTTPS con il servizio Batch. Per migliorare le prestazioni dell'applicazione durante il monitoraggio di un numero elevato di pool, processi e attività, sfruttare le [tecniche per eseguire query in modo efficiente](batch-efficient-list-queries.md) sul servizio Batch.






## Passaggi successivi

* Introduzione allo sviluppo della prima applicazione con la [libreria client .NET per Batch](batch-dotnet-get-started.md)
* Sfogliare gli esempi di codice Batch su [GitHub](https://github.com/Azure/azure-batch-samples)

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=Nov15_HO1-->