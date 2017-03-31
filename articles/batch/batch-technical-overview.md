---
title: Azure Batch esegue soluzioni di calcolo parallele su larga scala nel cloud | Microsoft Docs
description: Informazioni sull&quot;uso del servizio Azure Batch per carichi di lavoro HPC e paralleli su larga scala
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 8a850f8b1cccda631786f89f2c88647604fba9ed
ms.lasthandoff: 03/15/2017


---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Eseguire carichi di lavoro intrinsecamente paralleli con Batch

Azure Batch è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su una raccolta gestita di macchine virtuali e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi.

Con Azure Batch, è possibile definire facilmente le risorse di calcolo di Azure per eseguire le applicazioni in parallelo e su larga scala. Non è necessario creare, configurare e gestire manualmente un cluster HPC, singole macchine virtuali, reti virtuali o un'infrastruttura complessa di pianificazione di processi e attività. Azure Batch automatizza o semplifica queste attività.

## <a name="use-cases-for-batch"></a>Casi d'uso di Batch
Batch è un servizio gestito di Azure usato per l'*elaborazione batch* o *batch computing*, ovvero l'esecuzione di un volume elevato di attività simili per un risultato previsto. Il batch computing viene in genere usato dalle aziende che devono elaborare, trasformare e analizzare regolarmente volumi di dati di grandi dimensioni.

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

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenario: scalabilità orizzontale di un carico di lavoro parallelo
Una soluzione comune che usa le API Batch per interagire con il servizio Batch comporta l'aumento del numero di istanze di lavoro intrinsecamente parallelo, ad esempio nel rendering di immagini per scene 3D, in un pool di nodi di calcolo. Il pool di nodi di calcolo può essere usato, ad esempio, come una "farm di rendering" che può fornire fino a migliaia di core per il processo di rendering.

Il diagramma seguente mostra un normale flusso di lavoro di Batch con un'applicazione client o un servizio ospitato che usa Batch per l'esecuzione di un carico di lavoro parallelo.

![Flusso di lavoro della soluzione Batch][2]

In questo scenario comune l'applicazione o il servizio elabora un carico di lavoro di calcolo in Azure Batch, seguendo questa procedura:

1. Caricare i **file di input** e l'**applicazione** che elaborerà i file nell'account di archiviazione di Azure. I file di input elaborati dall'applicazione possono essere, ad esempio, dati di modellazione finanziaria o file video da transcodificare. L'applicazione può essere una qualsiasi applicazione usata per l'elaborazione dei dati, ad esempio un'applicazione per il rendering 3D o un transcodificatore di file multimediali.
2. Creare un **pool** di nodi di calcolo di Batch nell'account Batch. Questi nodi sono le macchine virtuali che eseguiranno le attività. Specificare le proprietà, ad esempio le [dimensioni dei nodi](../cloud-services/cloud-services-sizes-specs.md), il sistema operativo e il percorso in Archiviazione di Azure dell'applicazione da installare quando i nodi vengono aggiunti al pool. Si tratta dell'applicazione caricata nel passaggio 1. È anche possibile configurare il pool per la [scalabilità automatica](batch-automatic-scaling.md) in risposta al carico di lavoro generato dalle attività. La scalabilità automatica adegua in modo dinamico il numero di nodi di calcolo nel pool.
3. Creare un **processo** di Batch per eseguire il carico di lavoro nel pool di nodi di calcolo. Quando si crea un processo, lo si associa a un pool di Batch.
4. Aggiungere **attività** al processo. Quando si aggiungono attività a un processo, il servizio Batch pianifica automaticamente le attività per l'esecuzione nei nodi di calcolo del pool. Ogni attività usa l'applicazione caricata per elaborare i file di input.
   
   * 4a. Prima dell'esecuzione, l'attività può scaricare i dati, ovvero i file di input, che dovrà elaborare nel nodo di calcolo a cui è assegnata. Se l'applicazione non è già installata nel nodo, come indicato nel passaggio 2, è possibile scaricarla a questo punto. Al termine del download le attività vengono eseguite nei nodi a cui sono assegnate.
5. Durante l'esecuzione delle attività è possibile eseguire query sul servizio Batch per monitorare lo stato del processo e le relative attività. L'applicazione o il servizio client comunica con il servizio Batch tramite HTTPS. Poiché è possibile che si monitorino migliaia di attività in esecuzione in migliaia di nodi di calcolo, assicurarsi di [effettuare una query del servizio Batch in modo efficiente](batch-efficient-list-queries.md).
6. Man mano che vengono completate, le attività possono caricare i dati dei risultati in Archiviazione di Azure. È anche possibile recuperare i file direttamente dal file system in un nodo di calcolo.
7. Quando il monitoraggio rileva che sono state completate le attività nel processo, l'applicazione o il servizio client può scaricare i dati di output per una seconda elaborazione o valutazione.

Tenere presente che questo è soltanto uno dei modi in cui è possibile usare Batch e che questo scenario descrive solo alcune delle funzionalità disponibili. È ad esempio possibile eseguire [più attività in parallelo](batch-parallel-node-tasks.md) in ogni nodo di calcolo, nonché usare [attività di preparazione e completamento di processi](batch-job-prep-release.md) per preparare i nodi per i processi e quindi eseguire la pulizia in un secondo momento.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata esaminata una panoramica generale del servizio Batch è necessario un approfondimento per imparare a usare il servizio per l'elaborazione di carichi di lavoro paralleli a elevato utilizzo di calcolo.

* Vedere [Panoramica sulle funzionalità di Batch per sviluppatori](batch-api-basics.md)per informazioni essenziali per chiunque si prepari all'uso di Batch. L'articolo contiene informazioni più dettagliate sulle risorse del servizio Batch, ad esempio pool, nodi, processi e attività, e sulle numerose funzionalità delle API che è possibile usare durante la compilazione dell'applicazione Batch.
* Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
* [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) . È consigliabile leggere questo articolo nelle prime fasi dell'apprendimento dell'uso del servizio Batch. È anche disponibile una [versione Python](batch-python-tutorial.md) dell'esercitazione.
* Scaricare gli [esempi di codice in GitHub][github_samples] per vedere come C# e Python possono interfacciarsi con Batch per pianificare ed elaborare carichi di lavoro di esempio.
* Per conoscere le risorse disponibili per imparare a usare Batch, vedere il [percorso di apprendimento per Batch][learning_path].


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

