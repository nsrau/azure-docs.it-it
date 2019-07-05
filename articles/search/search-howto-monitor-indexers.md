---
title: Come monitorare lo stato dell'indicizzatore e dei risultati - ricerca di Azure
description: Monitorare lo stato, lo stato di avanzamento e risultati di indicizzatori di ricerca di Azure nel portale di Azure, usando l'API REST o .NET SDK.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486285"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Come monitorare i risultati e lo stato dell'indicizzatore di ricerca di Azure

Ricerca di Azure fornisce lo stato e informazioni sulle esecuzioni attuali e cronologiche di ogni indicizzatore di monitoraggio.

Il monitoraggio dell'indicizzatore è utile quando si desidera:

* Rileva lo stato di avanzamento di un indicizzatore durante un corso di esecuzione.
* Esaminare i risultati dell'esecuzione dell'indicizzatore in corso o precedente.
* Indicizzatore di livello superiore di identificare gli errori e gli errori o avvisi relativi a singoli documenti da indicizzare.

## <a name="find-indexer-status-and-history-details"></a>Trovare i dettagli cronologia e lo stato dell'indicizzatore

È possibile accedere a informazioni di monitoraggio di un indicizzatore in vari modi, tra cui:

* Nel [portale di Azure](#portal)
* Uso di [API REST](#restapi)
* Uso di [.NET SDK](#dotnetsdk)

Indicizzatore disponibili informazioni di monitoraggio include tutti i seguenti (anche se i dati sono diversi formati di basano del metodo di accesso utilizzato):

* Informazioni sullo stato relative l'indicizzatore
* Informazioni su più recente esecuzione dell'indicizzatore, tra cui relativo stato, avvio e ora di fine e gli errori dettagliati e gli avvisi.
* Elenco di esecuzioni dell'indicizzatore cronologici e relativi stati, i risultati, errori e avvisi.

Gli indicizzatori che elaborano grandi volumi di dati possono richiedere molto tempo per l'esecuzione. Ad esempio, gli indicizzatori di gestire milioni di documenti di origine possono eseguire per 24 ore e quindi riavviare quasi immediatamente. Lo stato per gli indicizzatori con volumi elevati potrebbe essere sempre indicato **In corso** nel portale. Anche quando un indicizzatore è in esecuzione, i dettagli sono disponibili sulle esecuzioni precedenti e lo stato di avanzamento in corso.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Indicizzatori di monitoraggio nel portale

È possibile visualizzare lo stato corrente di tutti gli indicizzatori nel **indicizzatori** elenco nella pagina di panoramica del servizio di ricerca.

   ![Elenco di indicizzatori](media/search-monitor-indexers/indexers-list.png "elenco di indicizzatori")

Quando un indicizzatore è in esecuzione, lo stato in Mostra l'elenco **In corso**e il **ha avuto esito positivo di Docs** valore Mostra il numero di documenti elaborati fino a questo momento. Può richiedere alcuni minuti per il portale aggiornare i valori di stato dell'indicizzatore e conteggi di documenti.

Un indicizzatore la cui esecuzione più recente è stato illustrato ha esito positivo **Success**. L'esecuzione dell'indicizzatore può essere esito positivo anche se i singoli documenti contengono errori, se il numero di errori è minore dell'indicizzatore **elementi con errori Max** impostazione.

Se l'ultima esecuzione terminata con un errore, lo stato viene mostrato **Failed**. Lo stato **reimpostare** significa che stato di rilevamento delle modifiche dell'indicizzatore è stata reimpostata.

Fare clic su un indicizzatore nell'elenco per visualizzare ulteriori dettagli relativi all'indicizzatore correnti e recenti viene eseguito.

   ![Cronologia di esecuzione e riepilogo indicizzatore](media/search-monitor-indexers/indexer-summary.png "cronologia di esecuzione e riepilogo indicizzatore")

Il **riepilogo indicizzatore** grafico viene visualizzato un grafico del numero di documenti elaborati durante le esecuzioni più recenti.

Il **dettagli dell'esecuzione** elenco Mostra i risultati dell'esecuzione più recente fino a 50.

Fare clic sul risultato di un'esecuzione nell'elenco per visualizzare informazioni specifiche sull'esecuzione. Ciò include l'avvio e ora di fine e gli eventuali errori e avvisi che si sono verificati.

   ![Dettagli dell'esecuzione dell'indicizzatore](media/search-monitor-indexers/indexer-execution.png "dettagli dell'esecuzione dell'indicizzatore")

Se si sono verificati problemi specifici del documento durante l'esecuzione, questi verranno elencati i campi di errori e avvisi.

   ![Dettagli dell'indicizzatore con errori](media/search-monitor-indexers/indexer-execution-error.png "i dettagli di un indicizzatore con errori")

Gli avvisi sono comuni con alcuni tipi di indicizzatori e sempre non indicano un problema. Ad esempio indicizzatori che usano i servizi cognitivi possono segnalare gli avvisi quando i file PDF o immagine non contengono un testo per l'elaborazione.

Per altre informazioni sull'analisi indicizzatore errori e avvisi, vedere [risoluzione dei problemi comuni di un indicizzatore in ricerca di Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Monitorare gli indicizzatori con l'API REST

È possibile recuperare la cronologia di esecuzione e lo stato di un indicizzatore usando il [comandi Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Cronologia di esecuzione contiene fino a 50 esecuzioni più recenti, che vengono ordinate in ordine cronologico inverso (più recente per primo).

Si noti che due valori di stato diverso. Lo stato di livello superiore è per l'indicizzatore. Lo stato dell'indicizzatore **in esecuzione** significa che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non che l'it del attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato che indica se è in corso l'esecuzione specifica (**in esecuzione**), o già completato con un **successo**, **transientFailure**, oppure **persistentFailure** dello stato. 

Quando viene reimpostato un indicizzatore per aggiornare relativo stato di rilevamento modifiche, una voce di cronologia di esecuzione separato viene aggiunto con un **reimpostare** dello stato.

Per altre informazioni sui codici di stato e dell'indicizzatore dati di monitoraggio, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Monitorare gli indicizzatori tramite .NET SDK

È possibile definire la pianificazione per un indicizzatore tramite .NET SDK ricerca di Azure. A tale scopo, includere il **pianificazione** proprietà durante la creazione o l'aggiornamento di un indicizzatore.

Il seguente C# vengono scritte le informazioni sullo stato di un indicizzatore ed eseguire i risultati del più recente (o in corso) nella console.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

L'output della console avrà un aspetto simile al seguente:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Si noti che due valori di stato diverso. Lo stato di livello superiore è lo stato dell'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** significa che l'indicizzatore è configurato correttamente ed è disponibile per l'esecuzione, ma non che l'it è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato che indica se è in corso l'esecuzione specifica (**in esecuzione**), o è stata già completata con un **successo** oppure **TransientError** stato. 

Quando viene reimpostato un indicizzatore per aggiornare relativo stato di rilevamento modifiche, viene aggiunta una voce di cronologia distinta con un **reimpostare** dello stato.

Per altre informazioni sui codici di stato e informazioni di monitoraggio dell'indicizzatore, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) nell'API REST.

Informazioni dettagliate sulle specifiche del documento errori o avvisi possono essere recuperate tramite l'enumerazione di elenchi `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings`.

Per altre informazioni sulle classi di .NET SDK consente di monitorare gli indicizzatori, vedere [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
