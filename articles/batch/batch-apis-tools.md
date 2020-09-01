---
title: API e strumenti per sviluppatori
description: Informazioni sulle API e gli strumenti disponibili per lo sviluppo di soluzioni con il servizio Azure Batch.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146403"
---
# <a name="overview-of-batch-apis-and-tools"></a>Panoramica delle API e degli strumenti di Batch

L'elaborazione dei carichi di lavoro paralleli con Azure Batch viene in genere eseguita a livello di codice usando una delle API Batch. L'applicazione o il servizio client può usare le API Batch per comunicare con il servizio Batch. Con le API Batch, è possibile creare e gestire pool di nodi di calcolo, che siano macchine virtuali o servizi cloud. È quindi possibile programmare processi e attività da eseguire in tali nodi.

È possibile elaborare in modo efficiente carichi di lavoro su larga scala per l'organizzazione oppure offrire ai clienti un front-end di servizio perché possano eseguire processi e attività, su richiesta o in base a una pianificazione, in uno, centinaia o anche migliaia di nodi. È anche possibile usare il servizio Azure Batch nell'ambito di un flusso di lavoro più ampio, gestito da strumenti come [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Per altre informazioni sulle funzionalità e sul flusso di lavoro usati in Azure Batch, vedere [Flusso di lavoro e risorse del servizio Batch](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Account Azure per lo sviluppo Batch

Quando si sviluppano soluzioni Batch, è necessario usare gli account seguenti nella sottoscrizione di Azure:

- **Account Batch**: le risorse di Azure Batch, inclusi pool, nodi di calcolo, processi e attività, sono associate a un account Azure [Batch](accounts.md). Quando l'applicazione effettua una richiesta sul servizio Batch, autentica la richiesta usando il nome dell'account Azure Batch, l'URL dell'account e una chiave di accesso o un token di Azure Active Directory. È possibile [creare un account Batch](batch-account-create-portal.md) nel portale di Azure oppure a livello di codice.
- **Account di archiviazione**: Batch include il supporto predefinito per l'uso di file in [Archiviazione di Azure](../storage/index.yml). Quasi tutti gli scenari di Batch usano l'archivio BLOB di Azure per lo staging dei programmi eseguiti e dei dati elaborati dalle attività e per l'archiviazione dei dati di output generati. Ogni account Batch è in genere associato a un account di archiviazione corrispondente.

## <a name="service-level-and-management-level-apis"></a>API a livello di servizio e di gestione

Azure Batch offre due set di API, uno per il livello di servizio e uno per il livello di gestione. La denominazione è spesso simile, ma i risultati restituiti sono diversi.

Nel log attività vengono registrate solo le azioni delle API di gestione. Le API per il livello di servizio ignorano il livello di gestione delle risorse di Azure (management.azure.com) e non vengono registrate.

L'[API di servizio Batch per eliminare un pool](/rest/api/batchservice/pool/delete) è ad esempio indirizzata direttamente all'account Batch: `DELETE {batchUrl}/pools/{poolId}`

Mentre l'[API di gestione Batch per eliminare un pool](/rest/api/batchmanagement/pool/delete) è indirizzata al livello management.azure.com: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>API di servizio Batch

Le applicazioni e i servizi possono rilasciare chiamate API REST dirette o usare una o più delle librerie client seguenti per eseguire e gestire i carichi di lavoro di Azure Batch.

| API | Informazioni di riferimento sulle API | Download | Esercitazione | Esempi di codice | Altre informazioni |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[API REST di Azure-docs](/rest/api/batchservice/) |N/D |- |- | [Versioni supportate](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Azure SDK per .NET-docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Esercitazione](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Note sulla versione](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Azure SDK per Python-docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Esercitazione](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [File Leggimi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK per JavaScript-docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Esercitazione](batch-nodejs-get-started.md) |- | [File Leggimi](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Azure SDK per Java-docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [File Leggimi](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API per la gestione di Batch

Le API di Azure Resource Manager per Batch forniscono l'accesso a livello di codice agli account Batch. Usando queste API, è possibile gestire a livello di codice gli account Batch, le quote, i pacchetti dell'applicazione e altre risorse tramite il provider Microsoft.Batch.  

| API | Informazioni di riferimento sulle API | Download | Esercitazione | Esempi di codice |
| --- | --- | --- | --- | --- |
| **REST per la gestione di Batch** |[API REST di Azure-docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET per la gestione di Batch** |[Azure SDK per .NET-docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Esercitazione](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Python per la gestione di Batch** |[Azure SDK per Python-docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Node.js per la gestione di Batch** |[Azure SDK per JavaScript-docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Java per la gestione di Batch** |[Azure SDK per Java-docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Strumenti da riga di comando di Batch

Questi strumenti da riga di comando forniscono le stesse funzionalità delle API del servizio Batch e di gestione di Batch: 

- [Cmdlet di PowerShell per Batch](/powershell/module/az.batch/): i cmdlet di Azure Batch nel modulo [Azure PowerShell](/powershell/azure/) consentono di gestire le risorse di Batch con PowerShell.
- [Interfaccia della riga di comando di Azure](/cli/azure): l'interfaccia della riga di comando di Azure è un set di strumenti multipiattaforma che offre comandi della shell per l'interazione con molti servizi di Azure, inclusi il servizio Batch e il servizio di gestione di Batch. Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure con Batch, vedere [Gestire le risorse di Batch con l'interfaccia della riga di comando di Azure](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Altri strumenti per lo sviluppo di applicazioni

Gli strumenti aggiuntivi riportati di seguito possono risultare utili per la compilazione e il debug di applicazioni e servizi Batch.

- [Portale di Azure](https://portal.azure.com/): nel portale di Azure è possibile creare, monitorare ed eliminare pool, processi e attività di Batch. È possibile visualizzare informazioni sullo stato di queste e altre risorse mentre si eseguono i processi, nonché scaricare file dai nodi di calcolo dei pool. È ad esempio possibile scaricare il file `stderr.txt` di un'attività non riuscita durante la risoluzione dei problemi. Si possono anche scaricare i file desktop remoto (RDP) che possono essere usati per accedere ai nodi di calcolo.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer, in precedenza detto BatchLabs, è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare e monitorare le applicazioni di Azure Batch, oltre che di eseguirne il debug. È possibile scaricare un [pacchetto di installazione](https://azure.github.io/BatchExplorer/) per Mac, Linux o Windows.
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard è uno strumento che consente di effettuare il provisioning, eseguire e monitorare l'elaborazione batch basata su contenitori e carichi di lavoro HPC in Azure Batch.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): anche se non è uno strumento di Azure Batch in senso stretto, Storage Explorer è un altro strumento utile per lo sviluppo e il debug delle soluzioni Batch.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per informazioni sulla registrazione di eventi dall'applicazione Batch, vedere [Metriche, avvisi e log di Batch per la valutazione diagnostica e il monitoraggio](batch-diagnostics.md).
- Per informazioni di riferimento sugli eventi generati dal servizio Batch, vedere [Batch Analytics](batch-analytics.md).
- Per informazioni sulle variabili di ambiente per i nodi di calcolo, vedere [Variabili di ambiente del runtime attività di Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- [Introduzione alla libreria di Azure Batch per .NET](tutorial-parallel-dotnet.md) . Sono disponibili anche una [versione Python](tutorial-parallel-python.md) e un'[esercitazione su Node.js](batch-nodejs-get-started.md).
- Scaricare gli [esempi di codice in GitHub](https://github.com/Azure-Samples/azure-batch-samples) per vedere come C# e Python possono interfacciarsi con Batch per pianificare ed elaborare carichi di lavoro di esempio.
