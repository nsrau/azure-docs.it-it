---
title: Nozioni di base su Azure Batch | Documentazione Microsoft
description: Informazioni sull&quot;uso del servizio Azure Batch per carichi di lavoro HPC e paralleli su larga scala
services: batch
documentationcenter: 
author: mmacy
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2559aae752f319805c2c9f87a2e2a81d9b37e4b2


---
# <a name="basics-of-azure-batch"></a>Nozioni di base su Azure Batch
Azure Batch consente di eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. È un servizio di piattaforma che pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su una raccolta gestita di macchine virtuali e che può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi.

Il servizio Batch consente di definire le risorse di calcolo di Azure per eseguire le applicazioni in parallelo e su larga scala. È possibile eseguire processi su richiesta o pianificati e non è necessario creare, configurare e gestire manualmente un cluster HPC, singole macchine virtuali, reti virtuali o un'infrastruttura complessa di pianificazione di processi e attività.

## <a name="use-cases-for-batch"></a>Casi d'uso di Batch
Batch è un servizio gestito di Azure usato per l'*elaborazione batch* o *batch computing*, ovvero l'esecuzione di un volume elevato di attività simili per ottenere il risultato previsto. Il batch computing viene in genere usato dalle aziende che devono elaborare, trasformare e analizzare regolarmente volumi di dati di grandi dimensioni.

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

Batch può anche eseguire calcoli paralleli con un passaggio di riduzione alla fine e carichi di lavoro HPC più complessi, ad esempio applicazioni [MPI (Message Passing Interface)](batch-mpi.md) .

Per un confronto tra Batch e altre soluzioni HPC in Azure, vedere [Soluzioni Batch e HPC nel cloud di Azure](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Sviluppo con Batch
L'elaborazione dei carichi di lavoro paralleli con Batch è in genere eseguita a livello di codice usando una delle [API Batch](#batch-development-apis). Queste ultime permettono di creare e gestire pool di nodi di calcolo, o macchine virtuali, nonché di pianificare i processi e le attività da eseguire su tali nodi. Un'applicazione o un servizio client che viene creato usa le API Batch per comunicare con il servizio Batch.

È possibile elaborare in modo efficiente carichi di lavoro su larga scala per l'organizzazione oppure offrire ai clienti un front-end di servizio perché possano eseguire processi e attività, su richiesta o in base a una pianificazione, in uno, centinaia o anche migliaia di nodi. È anche possibile usare il servizio Batch nell'ambito di un flusso di lavoro più ampio, gestito da strumenti come [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [!TIP]
> Per approfondire l'API Batch e le relative funzionalità, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
> 
> 

### <a name="azure-accounts-youll-need"></a>Account di Azure necessari
Di seguito sono riportati gli account da usare in Microsoft Azure per lo sviluppo di soluzioni Batch.

* **Account e sottoscrizione di Azure**: se non si ha già una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN][msdn_benefits] oppure iscriversi per ottenere un [account Azure gratuito][free_account]. Quando si crea un account, viene creata una sottoscrizione predefinita.
* **Account Batch** : quando le applicazioni interagiscono con il servizio Batch, il nome dell'account, l'URL dell'account e una chiave di accesso vengono usati come credenziali. Tutte le risorse di Batch, come pool, nodi di calcolo, processi e attività, sono associate a un account Batch. È possibile [creare un account Batch](batch-account-create-portal.md) nel portale di Azure.
* **Account di archiviazione**: Batch include il supporto predefinito per l'uso di file in [Archiviazione di Azure][azure_storage]. Quasi tutti gli scenari di Batch usano Archiviazione di Azure per lo staging dei programmi eseguiti e dei dati elaborati dalle attività e per l'archiviazione dei dati di output generati. Per creare un account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>API di sviluppo per Batch
Le applicazioni e i servizi possono effettuare chiamate dirette all'API REST, usare una o più delle librerie client seguenti oppure una combinazione di entrambi per gestire le risorse di calcolo ed eseguire carichi di lavoro paralleli su larga scala usando il servizio Batch.

| API | Informazioni di riferimento sulle API | Scaricare | Esempi di codice |
| --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/D |[MSDN][batch_rest] |
| **Batch .NET** |[MSDN][api_net] |[NuGet ][api_net_nuget] |[GitHub][api_sample_net] |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |
| **Batch Java** (anteprima) |[github.io][api_java] |[Maven][api_java_jar] |[GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestione delle risorse di Batch
Oltre a usare le API client, è possibile gestire le risorse nell'account Batch anche nei modi seguenti.

* [Cmdlet di PowerShell per Batch][batch_ps]: i cmdlet di Azure Batch nel modulo di [Azure PowerShell](../powershell-install-configure.md) consentono di gestire le risorse Batch con PowerShell.
* [Interfaccia della riga di comando di Azure](../xplat-cli-install.md): l'interfaccia della riga di comando di Azure è un set di strumenti multipiattaforma che offre comandi della shell per l'interazione con molti servizi di Azure, incluso Batch.
* Libreria client di [gestione .NET per Batch](batch-management-dotnet.md): anche la libreria client di gestione .NET per Batch è disponibile in [NuGet][api_net_mgmt_nuget] e può essere usata per gestire gli account, le quote e i pacchetti dell'applicazione Batch a livello di codice. I riferimenti per la libreria di gestione sono disponibili in [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Strumenti di Batch
Di seguito sono riportati alcuni strumenti che, anche se non necessari per creare soluzioni con Batch, si rivelano utili per la compilazione e il debug di applicazioni e servizi di Batch.

* [Portale di Azure][portal]: nei pannelli per Batch del portale di Azure si possono creare, monitorare ed eliminare pool, processi e attività di Batch. È possibile visualizzare informazioni sullo stato di queste e altre risorse mentre si eseguono i processi, nonché scaricare file dai nodi di calcolo dei pool, ad esempio il file `stderr.txt` di un'attività non riuscita durante la risoluzione dei problemi. Si possono anche scaricare i file desktop remoto (RDP) che possono essere usati per accedere ai nodi di calcolo.
* [Azure Batch Explorer][batch_explorer]: Batch Explorer offre funzionalità per la gestione delle risorse di Batch simili al portale di Azure, ma in un'applicazione client WPF (Windows Presentation Foundation) autonoma. È una delle applicazioni Batch .NET di esempio disponibili in [GitHub][github_samples] e può essere compilata con Visual Studio 2015 o versioni successive ed essere usata per esplorare e gestire le risorse nell'account Batch durante lo sviluppo e il debug delle soluzioni Batch. È possibile visualizzare i dettagli di attività, pool e processi, scaricare file dai nodi di calcolo e connettersi in remoto ai nodi usando i file desktop remoto (RDP) scaricabili con Batch Explorer.
* [Microsoft Azure Storage Explorer][storage_explorer]: anche se non è uno strumento di Azure Batch in senso stretto, Storage Explorer è un altro strumento utile per lo sviluppo e il debug delle soluzioni Batch.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenario: scalabilità orizzontale di un carico di lavoro parallelo
Una soluzione comune che usa le API Batch per interagire con il servizio Batch comporta l'aumento del numero di istanze di lavoro intrinsecamente parallelo, ad esempio nel rendering di immagini per scene 3D, in un pool di nodi di calcolo. Il pool di nodi di calcolo può essere usato, ad esempio, come una "farm di rendering" che può fornire fino a migliaia di core per il processo di rendering.

Il diagramma seguente mostra un normale flusso di lavoro di Batch con un'applicazione client o un servizio ospitato che usa Batch per l'esecuzione di un carico di lavoro parallelo.

![Flusso di lavoro della soluzione Batch][2]

In questo scenario comune l'applicazione o il servizio elabora un carico di lavoro di calcolo in Azure Batch, seguendo questa procedura:

1. Caricare i **file di input** e l'**applicazione** che elaborerà i file nell'account di archiviazione di Azure. I file di input elaborati dall'applicazione possono essere, ad esempio, dati di modellazione finanziaria o file video da transcodificare. L'applicazione può essere una qualsiasi applicazione usata per l'elaborazione dei dati, ad esempio un'applicazione per il rendering 3D o un transcodificatore di file multimediali.
2. Creare un **pool** di nodi di calcolo di Batch nell'account Batch. Questi nodi sono le macchine virtuali che eseguiranno le attività. Specificare le proprietà, ad esempio le [dimensioni dei nodi](../cloud-services/cloud-services-sizes-specs.md), il sistema operativo e il percorso in Archiviazione di Azure dell'applicazione da installare quando i nodi vengono aggiunti al pool. Si tratta dell'applicazione caricata nel passaggio 1. È anche possibile configurare il pool per il [ridimensionamento automatico](batch-automatic-scaling.md), in modo che il numero di nodi di calcolo del pool venga regolato dinamicamente in risposta al carico di lavoro generato dalle attività.
3. Creare un **processo** di Batch per eseguire il carico di lavoro nel pool di nodi di calcolo. Quando si crea un processo, lo si associa a un pool di Batch.
4. Aggiungere **attività** al processo. Quando si aggiungono attività a un processo, il servizio Batch pianifica automaticamente le attività per l'esecuzione nei nodi di calcolo del pool. Ogni attività usa l'applicazione caricata per elaborare i file di input.
   
   * 4a. Prima dell'esecuzione, l'attività può scaricare i dati, ovvero i file di input, che dovrà elaborare nel nodo di calcolo a cui è assegnata. Se l'applicazione non è già installata nel nodo, come indicato nel passaggio 2, è possibile scaricarla a questo punto. Al termine del download le attività vengono eseguite nei nodi a cui sono assegnate.
5. Durante l'esecuzione delle attività è possibile eseguire query sul servizio Batch per monitorare lo stato del processo e le relative attività. L'applicazione o il servizio client comunica con il servizio Batch tramite HTTPS. Dal momento che potrebbe essere necessario monitorare migliaia di attività in esecuzione in migliaia di nodi di calcolo, è necessario assicurarsi di [eseguire query sul servizio Batch in modo efficiente](batch-efficient-list-queries.md).
6. Man mano che vengono completate, le attività possono caricare i dati dei risultati in Archiviazione di Azure. È anche possibile recuperare i file direttamente dai nodi di calcolo.
7. Quando il monitoraggio rileva che sono state completate le attività nel processo, l'applicazione o il servizio client può scaricare i dati di output per una seconda elaborazione o valutazione.

Tenere presente che questo è soltanto uno dei modi in cui è possibile usare Batch e che questo scenario descrive solo alcune delle funzionalità disponibili. È ad esempio possibile eseguire [più attività in parallelo](batch-parallel-node-tasks.md) in ogni nodo di calcolo, nonché usare [attività di preparazione e completamento di processi](batch-job-prep-release.md) per preparare i nodi per i processi e quindi eseguire la pulizia in un secondo momento.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata esaminata una panoramica generale del servizio Batch è necessario un approfondimento per imparare a usare il servizio per l'elaborazione di carichi di lavoro paralleli a elevato utilizzo di calcolo.

* Vedere [Panoramica sulle funzionalità di Batch per sviluppatori](batch-api-basics.md)per informazioni essenziali per chiunque si prepari all'uso di Batch. L'articolo contiene informazioni più dettagliate sulle risorse del servizio Batch, ad esempio pool, nodi, processi e attività, e sulle numerose funzionalità delle API che è possibile usare durante la compilazione dell'applicazione Batch.
* [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) . È consigliabile leggere questo articolo nelle prime fasi dell'apprendimento dell'uso del servizio Batch. È anche disponibile una [versione Python](batch-python-tutorial.md) dell'esercitazione.
* Scaricare gli [esempi di codice in GitHub][github_samples] per vedere come C# e Python possono interfacciarsi con Batch per pianificare ed elaborare carichi di lavoro di esempio.
* Per conoscere le risorse disponibili per imparare a usare Batch, vedere il [percorso di apprendimento per Batch][learning_path].

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
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
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Nov16_HO2-->


