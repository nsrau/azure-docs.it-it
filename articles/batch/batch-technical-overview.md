<properties
	pageTitle="Nozioni di base sul servizio Azure Batch | Microsoft Azure"
	description="Informazioni sull'uso del servizio Azure Batch per carichi di lavoro HPC e paralleli su larga scala"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/20/2016"
	ms.author="marsma"/>

# Nozioni di base su Azure Batch

Azure Batch consente di eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. È un servizio di piattaforma che pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su una raccolta gestita di macchine virtuali e che può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi.

Il servizio Batch consente di definire le risorse di calcolo di Azure per eseguire le applicazioni in parallelo e su larga scala. È possibile eseguire processi su richiesta o pianificati e non è necessario creare, configurare e gestire manualmente un cluster HPC, singole macchine virtuali, reti virtuali o un'infrastruttura complessa di pianificazione di processi e attività.

## Casi d'uso di Batch

Batch è un servizio gestito di Azure usato per l'*elaborazione batch*, o *batch computing*, che esegue un volume elevato di attività simili per ottenere il risultato previsto. Il batch computing viene in genere usato dalle aziende che devono elaborare, trasformare e analizzare regolarmente volumi di dati di grandi dimensioni.

Batch funziona bene con applicazioni e carichi di lavoro intrinsecamente paralleli, a volte definiti "imbarazzantemente paralleli", che possono essere suddivisi facilmente in più attività ed eseguiti contemporaneamente in più computer.

![Attività parallele][1]<br/>

Di seguito sono riportati alcuni esempi di carichi di lavoro in genere elaborati con questa tecnica:

* Modellazione dei rischi finanziari
* Analisi dei dati climatici e idrologici
* Rendering, analisi ed elaborazione di immagini
* Codifica e transcodifica multimediale
* Analisi delle sequenze genetiche
* Analisi delle sollecitazioni in fase di progettazione
* Test di software

Batch può anche eseguire calcoli paralleli con un passaggio di riduzione alla fine e altri carichi di lavoro HPC più complessi, ad esempio applicazioni [MPI (Message Passing Interface)](batch-mpi.md).

Per un confronto tra Batch e altre soluzioni HPC in Azure, vedere [Soluzioni Batch e HPC nel cloud di Azure](batch-hpc-solutions.md).

## Sviluppo con Batch

La compilazione di soluzioni che usano Azure Batch per l'elaborazione di carichi di lavoro paralleli si esegue a livello di codice con le API Batch. Queste ultime permettono di creare e gestire pool di nodi di calcolo, o macchine virtuali, nonché di pianificare i processi e le attività da eseguire su tali nodi. Un'applicazione o un servizio client che viene creato usa le API Batch per comunicare con il servizio Batch. È possibile elaborare in modo efficiente carichi di lavoro su larga scala per l'azienda o fornire un servizio front-end ai clienti perché possano eseguire processi e attività, su richiesta o in base a una pianificazione, in un numero indefinito di nodi. È anche possibile usare il servizio Batch nell'ambito di un flusso di lavoro più ampio, gestito da strumenti come [Azure Data Factory][data_factory].

> [AZURE.TIP] Per approfondire l'API Batch e le relative funzionalità, vedere [Cenni preliminari sulle funzionalità di Azure Batch](batch-api-basics.md).

### Account di Azure necessari

Di seguito sono riportati gli account da usare in Microsoft Azure per lo sviluppo di soluzioni Batch.

- **Account e sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN][msdn_benefits] oppure iscriversi per ottenere un [account Azure gratuito][free_account]. Quando si crea un account viene creata una sottoscrizione predefinita.

- **Account Batch**: quando le applicazioni interagiscono con il servizio Batch, il nome dell'account, l'URL dell'account e una chiave di accesso vengono usati come credenziali. Tutte le risorse di Batch, ad esempio pool, nodi di calcolo, processi e attività, sono associate a un account Batch. È possibile [creare e gestire un account Batch](batch-account-create-portal.md) nel portale di Azure.

- **Account di archiviazione**: il servizio Batch include il supporto predefinito per l'uso di file in [Archiviazione di Azure][azure_storage]. Quasi tutti gli scenari di Batch usano Archiviazione di Azure per lo staging dei programmi eseguiti e i dati elaborati dalle attività e per l'archiviazione dei dati di output generati. Per creare un account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](./../storage/storage-create-storage-account.md).

### API di sviluppo per Batch

Le applicazioni e i servizi possono effettuare chiamate dirette all'API REST, usare una o più delle librerie client seguenti oppure una combinazione di entrambi per gestire le risorse di calcolo ed eseguire carichi di lavoro paralleli su larga scala usando il servizio Batch.

| API | Informazioni di riferimento sulle API | Scaricare | Esempi di codice |
| ----------------- | ------------- | -------- | ------------ |
| **Batch REST** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **Batch .NET** | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python** | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |

### Gestione delle risorse di Batch

Oltre a usare le API client, è possibile gestire le risorse nell'account Batch anche nei modi seguenti.

- [Cmdlet di PowerShell per Batch][batch_ps]\: i cmdlet di Azure Batch nel modulo di [Azure PowerShell](../powershell-install-configure.md) consentono di gestire le risorse Batch con PowerShell.

- [Interfaccia della riga di comando di Azure](../xplat-cli-install.md): l'interfaccia della riga di comando di Azure è un set di strumenti multipiattaforma che fornisce comandi della shell per l'interazione con molti servizi di Azure, incluso Batch.

- Libreria client di [gestione .NET per Batch](batch-management-dotnet.md): anche la libreria client di gestione .NET per Batch è disponibile in [NuGet][api_net_mgmt_nuget], e può essere usata per gestire gli account, le quote e i pacchetti dell'applicazione Batch a livello di codice. I riferimenti per la libreria di gestione sono disponibili su [MSDN][api_net_mgmt].

### Strumenti di Batch

Anche se non sono necessari per creare soluzioni con Batch, questi strumenti possono essere considerati essenziali per la compilazione e il debug di applicazioni e servizi di Batch.

- [Azure Batch Explorer][batch_explorer]\: Batch Explorer è una delle applicazioni Batch .NET di esempio [disponibili in GitHub][github_samples]. Compilare l'applicazione Windows Presentation Foundation (WPF) con Visual Studio 2013 o 2015 e usarla per esplorare e gestire le risorse nell'account Batch durante lo sviluppo e il debug delle soluzioni Batch. È possibile visualizzare i dettagli di attività, pool e processi, scaricare file dai nodi di calcolo o connettersi ai nodi in modalità remota usando i file desktop remoto (RDP) disponibili con pochi clic nell'interfaccia di Batch Explorer.

- [Microsoft Azure Storage Explorer][storage_explorer]\: anche se non si tratta di uno strumento di Azure Batch in senso stretto, Storage Explorer risulta molto utile per lo sviluppo e il debug delle soluzioni Batch.

## Scenario: scalabilità orizzontale di un carico di lavoro parallelo

Una soluzione comune che usa le API Batch per interagire con il servizio Batch comporta l'aumento del numero di istanze di lavoro intrinsecamente parallelo, ad esempio nel rendering di immagini per scene 3D, in un pool di nodi di calcolo. Il pool di nodi di calcolo può essere usato, ad esempio, come una "farm di rendering" che può fornire fino a migliaia di core per il processo di rendering.

Il diagramma seguente mostra un normale flusso di lavoro di Batch con un'applicazione client o un servizio ospitato che usa Batch per l'esecuzione di un carico di lavoro parallelo.

![Flusso di lavoro della soluzione Batch][2]

In questo scenario comune l'applicazione o il servizio elabora un carico di lavoro di calcolo in Azure Batch, seguendo questa procedura:

1. Caricare i **file di input** e l'**applicazione** che elaborerà i file nell'account di archiviazione di Azure. I file di input elaborati dall'applicazione possono essere, ad esempio, dati di modellazione finanziaria o file video da transcodificare. L'applicazione può essere una qualsiasi applicazione usata per l'elaborazione dei dati, ad esempio un'applicazione per il rendering 3D o un transcodificatore di file multimediali.

2. Creare un **pool** di nodi di calcolo di Batch nell'account Batch, ovvero le macchine virtuali che eseguiranno le attività. Specificare le proprietà, ad esempio le [dimensioni dei nodi](./../cloud-services/cloud-services-sizes-specs.md), il sistema operativo e il percorso in Archiviazione di Azure dell'applicazione da installare quando i nodi vengono aggiunti al pool. L'applicazione è quella caricata nel passaggio 1. È anche possibile configurare il pool per il [ridimensionamento automatico](batch-automatic-scaling.md), affinché il numero di nodi di calcolo del pool venga regolato in modo dinamico in risposta al carico di lavoro generato dalle attività.

3. Creare un **processo** Batch per eseguire il carico di lavoro nel pool di nodi di calcolo. Quando si crea un processo, lo si associa a un pool di Batch.

4. Aggiungere **attività** al processo. Quando si aggiungono attività a un processo, il servizio Batch pianifica automaticamente le attività per l'esecuzione nei nodi di calcolo del pool. Ogni attività usa l'applicazione caricata per elaborare i file di input.

    - 4a. Prima dell'esecuzione, l'attività può scaricare i dati, ovvero i file di input, che dovrà elaborare nel nodo di calcolo a cui è assegnata. Se l'applicazione non è già installata nel nodo, come indicato nel passaggio 2, è possibile scaricarla a questo punto. Al termine del download le attività vengono eseguite nei nodi a cui sono assegnate.

5. Durante l'esecuzione delle attività è possibile eseguire query sul servizio Batch per monitorare lo stato del processo e le relative attività. L'applicazione o il servizio client comunica con il servizio Batch tramite HTTPS. Dal momento che potrebbe essere necessario monitorare migliaia di attività in esecuzione in migliaia di nodi di calcolo, è necessario assicurarsi di [eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md).

6. Man mano che vengono completate, le attività possono caricare i dati dei risultati in Archiviazione di Azure. È anche possibile recuperare i file direttamente dai nodi di calcolo.

7. Quando il monitoraggio rileva che sono state completate le attività nel processo, l'applicazione o il servizio client può scaricare i dati di output per una seconda elaborazione o valutazione.

Tenere presente che questo è soltanto uno dei modi in cui è possibile usare il servizio Batch e che questo scenario descrive solo alcune delle funzionalità disponibili. È ad esempio possibile eseguire [più attività in parallelo](batch-parallel-node-tasks.md) in ogni nodo di calcolo, usare [attività di preparazione e completamento di processi](batch-job-prep-release.md) per preparare i nodi per i processi e quindi eseguire la pulizia in un secondo momento.

## Passaggi successivi

Ora che è stato esaminato uno scenario di esempio, è necessario approfondire il servizio Batch per poterlo usare per l'elaborazione di carichi di lavoro paralleli a elevato utilizzo di calcolo.

- [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) per imparare a usare C# e la libreria Batch .NET per mettere in pratica le tecniche descritte in precedenza. È consigliabile leggere questo articolo nelle prime fasi dell'apprendimento dell'uso del servizio Batch.

- Vedere [Panoramica delle funzionalità di Azure Batch](batch-api-basics.md) per informazioni più approfondite sulle funzionalità API offerte dal servizio Batch per l'elaborazione dei carichi di lavoro a elevato utilizzo di calcolo.

- Oltre a Batch Explorer, sono disponibili altri [esempi di codice in GitHub][github_samples] che mostrano come usare molte delle funzionalità di Azure Batch con la libreria Batch .NET.

- Vedere il [percorso di apprendimento di Batch][learning_path] per conoscere le risorse disponibili per imparare a usare il servizio Batch.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0525_2016-->