---
title: Usare strumenti e API di Azure Batch e per sviluppare soluzioni di elaborazione parallela su larga scala | Microsoft Docs
description: Informazioni sulle API e gli strumenti disponibili per lo sviluppo di soluzioni con il servizio Azure Batch.
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
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9d523044f5107eea9dfbba17564cc15ec05076c5
ms.lasthandoff: 04/27/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Panoramica delle API e degli strumenti di Batch

L'elaborazione dei carichi di lavoro paralleli con Azure Batch è in genere eseguita a livello di codice usando una delle [API Batch](#batch-development-apis). L'applicazione o il servizio client può usare le API Batch per comunicare con il servizio Batch. Con le API Batch, è possibile creare e gestire pool di nodi di calcolo, che siano macchine virtuali o servizi cloud. È quindi possibile programmare processi e attività da eseguire in tali nodi. 

È possibile elaborare in modo efficiente carichi di lavoro su larga scala per l'organizzazione oppure offrire ai clienti un front-end di servizio perché possano eseguire processi e attività, su richiesta o in base a una pianificazione, in uno, centinaia o anche migliaia di nodi. È anche possibile usare il servizio Azure Batch nell'ambito di un flusso di lavoro più ampio, gestito da strumenti come [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Per approfondire l'API Batch e le relative funzionalità, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-youll-need"></a>Account di Azure necessari
Di seguito sono riportati gli account da usare in Microsoft Azure per lo sviluppo di soluzioni Batch.

* **Account e sottoscrizione di Azure**: se non si ha già una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN][msdn_benefits] oppure iscriversi per ottenere un [account Azure gratuito][free_account]. Quando si crea un account, viene creata una sottoscrizione predefinita.
* **Account Batch**: le risorse di Azure Batch, inclusi pool, nodi di calcolo, processi e attività, sono associate a un account Azure Batch. Quando l'applicazione esegue una richiesta sul servizio Batch, autentica la richiesta usando il nome dell'account Azure Batch, l'URL dell'account e la chiave di accesso. È possibile [creare un account Batch](batch-account-create-portal.md) nel portale di Azure.
* **Account di archiviazione**: Batch include il supporto predefinito per l'uso di file in [Archiviazione di Azure][azure_storage]. Quasi tutti gli scenari di Batch usano l'archivio BLOB di Azure per lo staging dei programmi eseguiti e dei dati elaborati dalle attività e per l'archiviazione dei dati di output generati. Per creare un account di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

## <a name="batch-development-apis"></a>API di sviluppo per Batch
Le applicazioni e i servizi possono rilasciare chiamate API REST dirette o usare una o più delle librerie client seguenti per eseguire e gestire i carichi di lavoro di Azure Batch.

| API | Informazioni di riferimento sulle API | Scaricare | Esercitazione | Esempi di codice | Altre informazioni |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/D |- |- | [Versioni supportate](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Esercitazione](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Note sulla versione](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Esercitazione](batch-python-tutorial.md)|[GitHub][api_sample_python] | [File Leggimi](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [File Leggimi](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[File Leggimi][api_sample_java] | [File Leggimi](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Strumenti da riga di comando di Batch

La funzionalità fornita dalle API di sviluppo è disponibile anche con gli strumenti da riga di comando: 

* [Cmdlet di PowerShell per Batch][batch_ps]: i cmdlet di Azure Batch nel modulo di [Azure PowerShell](/powershell/azure/overview) consentono di gestire le risorse Batch con PowerShell.
* [Interfaccia della riga di comando di Azure](/cli/azure/overview): l'interfaccia della riga di comando di Azure è un set di strumenti multipiattaforma che offre comandi della shell per l'interazione con molti servizi di Azure, incluso Batch.

## <a name="batch-resource-management"></a>Gestione delle risorse di Batch

Le API di Azure Resource Manager per Batch forniscono l'accesso a livello di codice agli account Batch. Usando queste API, è possibile gestire a livello di codice gli account Batch, le quote e i pacchetti dell'applicazione.  

| API | Informazioni di riferimento sulle API | Scaricare | Esercitazione | Esempi di codice |
| --- | --- | --- | --- | --- |
| **REST di Resource Manager per Batch** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET di Resource Manager per Batch** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Esercitazione](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Strumenti di Batch
Di seguito sono riportati alcuni strumenti che, anche se non necessari per creare soluzioni con Batch, si rivelano utili per la compilazione e il debug di applicazioni e servizi di Batch.

* [Portale di Azure][portal]: nei pannelli per Batch del portale di Azure si possono creare, monitorare ed eliminare pool, processi e attività di Batch. È possibile visualizzare informazioni sullo stato di queste e altre risorse mentre si eseguono i processi, nonché scaricare file dai nodi di calcolo dei pool, ad esempio il file `stderr.txt` di un'attività non riuscita durante la risoluzione dei problemi. Si possono anche scaricare i file desktop remoto (RDP) che possono essere usati per accedere ai nodi di calcolo.
* [Azure Batch Explorer][batch_explorer]: Batch Explorer offre funzionalità per la gestione delle risorse di Batch simili al portale di Azure, ma in un'applicazione client WPF (Windows Presentation Foundation) autonoma. È una delle applicazioni Batch .NET di esempio disponibili in [GitHub][github_samples] e può essere compilata con Visual Studio 2015 o versioni successive ed essere usata per esplorare e gestire le risorse nell'account Batch durante lo sviluppo e il debug delle soluzioni Batch. È possibile visualizzare i dettagli di attività, pool e processi, scaricare file dai nodi di calcolo e connettersi in remoto ai nodi usando i file desktop remoto (RDP) scaricabili con Batch Explorer.
* [Microsoft Azure Storage Explorer][storage_explorer]: anche se non è uno strumento di Azure Batch in senso stretto, Storage Explorer è un altro strumento utile per lo sviluppo e il debug delle soluzioni Batch.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica sulle funzionalità di Batch per sviluppatori](batch-api-basics.md)per informazioni essenziali per chiunque si prepari all'uso di Batch. L'articolo contiene informazioni più dettagliate sulle risorse del servizio Batch, ad esempio pool, nodi, processi e attività, e sulle numerose funzionalità delle API che è possibile usare durante la compilazione dell'applicazione Batch.
* [Introduzione alla libreria di Azure Batch per .NET](batch-dotnet-get-started.md) . È consigliabile leggere questo articolo nelle prime fasi dell'apprendimento dell'uso del servizio Batch. È anche disponibile una [versione Python](batch-python-tutorial.md) dell'esercitazione.
* Scaricare gli [esempi di codice in GitHub][github_samples] per vedere come C# e Python possono interfacciarsi con Batch per pianificare ed elaborare carichi di lavoro di esempio.
* Per conoscere le risorse disponibili per imparare a usare Batch, vedere il [percorso di apprendimento per Batch][learning_path].


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

