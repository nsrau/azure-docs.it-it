---
title: Monitorare lo stato e i risultati dell'indicizzatoreMonitor indexer status and results
titleSuffix: Azure Cognitive Search
description: Monitorare lo stato, lo stato e i risultati degli indicizzatori di Ricerca cognitiva di Azure nel portale di Azure, usando l'API REST o .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112985"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Come monitorare lo stato e i risultati dell'indicizzatore di Ricerca cognitiva di AzureHow to monitor Azure Cognitive Search indexer status and results

Ricerca cognitiva di Azure offre informazioni sullo stato e sul monitoraggio delle esecuzioni correnti e cronologiche di ogni indicizzatore.

Il monitoraggio dell'indicizzatore è utile quando si desidera:Indexer monitoring is useful when you want to:

* Tenere traccia dello stato di avanzamento di un indicizzatore durante un'esecuzione in corso.
* Esaminare i risultati dell'esecuzione dell'indicizzatore in corso o precedente.
* Identificare gli errori dell'indicizzatore di primo livello e gli errori o gli avvisi relativi a singoli documenti da indicizzare.

## <a name="get-status-and-history"></a>Ottenere lo stato e la cronologiaGet status and history

È possibile accedere alle informazioni di monitoraggio dell'indicizzatore in vari modi, tra cui:You can access indexer monitoring information in various ways, including:

* Nel [portale di Azure](#portal)
* Uso [dell'API RESTUsing](#restapi) the REST API
* Utilizzo di [.NET SDK](#dotnetsdk)

Le informazioni di monitoraggio dell'indicizzatore disponibili includono tutti gli elementi seguenti (anche se i formati dei dati differiscono in base al metodo di accesso utilizzato):

* Informazioni sullo stato dell'indicizzatore stesso
* Informazioni sull'esecuzione più recente dell'indicizzatore, inclusi lo stato, l'ora di inizio e di fine e gli errori e gli avvisi dettagliati.
* Viene eseguito un elenco di filtri dell'indicizzatore cronologici e relativi stati, risultati, errori e avvisi.

L'esecuzione degli indicizzatori che elaborano grandi volumi di dati può richiedere molto tempo. Ad esempio, gli indicizzatori che gestiscono milioni di documenti di origine possono essere eseguiti per 24 ore e quindi riavviare quasi immediatamente. Lo stato degli indicizzatori con volumi elevati potrebbe sempre essere **In corso** nel portale. Anche quando è in esecuzione un indicizzatore, sono disponibili dettagli sullo stato di avanzamento in corso e sulle esecuzioni precedenti.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitorare tramite il portale

È possibile visualizzare lo stato corrente di tutti gli indicizzatori nell'elenco **Indicizzatori** della pagina Panoramica del servizio di ricerca.

   ![Elenco degli indicizzatori](media/search-monitor-indexers/indexers-list.png "Elenco degli indicizzatori")

Durante l'esecuzione di un indicizzatore, lo stato nell'elenco è **In corso**e il valore **Documenti completati** indica il numero di documenti elaborati finora. Il portale può richiedere alcuni minuti per aggiornare i valori di stato dell'indicizzatore e i conteggi dei documenti.

Un indicizzatore la cui esecuzione più recente è stata eseguita correttamente mostra **Success**. Un'esecuzione dell'indicizzatore può avere esito positivo anche se singoli documenti presentano errori, se il numero di errori è inferiore all'impostazione **Max di elementi non riusciti** dell'indicizzatore.

Se l'esecuzione più recente è terminata con un errore, lo stato è **Non riuscito**. Lo stato **Reimposta** indica che lo stato di rilevamento delle modifiche dell'indicizzatore è stato reimpostato.

Fare clic su un indicizzatore nell'elenco per visualizzare ulteriori dettagli sulle esecuzioni correnti e recenti dell'indicizzatore.

   ![Riepilogo dell'indicizzatore e cronologia di esecuzione](media/search-monitor-indexers/indexer-summary.png "Riepilogo dell'indicizzatore e cronologia di esecuzione")

Il grafico **di riepilogo dell'indicizzatore** visualizza un grafico del numero di documenti elaborati nelle esecuzioni più recenti.

**L'elenco Dettagli esecuzione** mostra fino a 50 dei risultati di esecuzione più recenti.

Fare clic su un risultato di esecuzione nell'elenco per visualizzare le specifiche relative all'esecuzione. Sono inclusi l'ora di inizio e di fine e gli eventuali errori e avvisi che si sono verificati.

   ![Dettagli sull'esecuzione dell'indicizzatore](media/search-monitor-indexers/indexer-execution.png "Dettagli sull'esecuzione dell'indicizzatore")

Se si sono verificati problemi specifici del documento durante l'esecuzione, verranno elencati nei campi Errori e Avvisi.

   ![Dettagli dell'indicizzatore con errori](media/search-monitor-indexers/indexer-execution-error.png "Dettagli dell'indicizzatore con errori")

Gli avvisi sono comuni con alcuni tipi di indicizzatori e non sempre indicano un problema. Ad esempio, gli indicizzatori che utilizzano servizi cognitivi possono segnalare avvisi quando i file di immagine o PDF non contengono testo da elaborare.

Per altre informazioni sull'analisi degli errori e degli avvisi dell'indicizzatore, vedere Risoluzione dei problemi comuni degli [indicizzatori in Ricerca cognitiva](search-indexer-troubleshooting.md)di Azure.For more information about investigating indexer errors and warnings, see Troubleshooting common indexer issues in Azure Cognitive Search .

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorare usando le API RESTMonitor using REST APIs

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore utilizzando il [comando Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

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

La cronologia di esecuzione contiene fino alle 50 esecuzioni più recenti, ordinate in ordine cronologico inverso (prima la più recente).

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è per l'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** indica che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non che è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche un proprio stato che indica se l'esecuzione specifica è in corso (**in esecuzione**) o già completata con uno stato **di esito**positivo , **transientFailure**o **persistentFailure** . 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce della cronologia di esecuzione separata con lo stato **Reimposta.When** an indexer is reset to refresh its change tracking state, a separate execution history entry is added with a Reset status.

Per ulteriori informazioni sui codici di stato e sui dati di monitoraggio dell'indicizzatore, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitorare utilizzando .NET SDK

È possibile definire la pianificazione per un indicizzatore usando Azure Cognitive Search .NET SDK. A tale scopo, includere la proprietà **schedule** durante la creazione o l'aggiornamento di un indicizzatore.

Nell'esempio in Cè riportato di seguito vengono scrivete informazioni sullo stato di un indicizzatore e sui risultati dell'esecuzione più recente (o in corso) nella console.

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

L'output nella console sarà simile al seguente:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è lo stato dell'indicizzatore stesso. Lo stato dell'indicizzatore **In esecuzione** indica che l'indicizzatore è impostato correttamente e disponibile per l'esecuzione, ma non che è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato per verificare se l'esecuzione specifica è in corso (**In esecuzione**) o è già stata completata con lo stato **Success** o **TransientError.** 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce della cronologia separata con lo stato **Reimposta.**

Per altre informazioni sui codici di stato e sulle informazioni di monitoraggio dell'indicizzatore, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) nell'API REST.

I dettagli relativi a errori o avvisi specifici `IndexerExecutionResult.Errors` del `IndexerExecutionResult.Warnings`documento possono essere recuperati enumerando gli elenchi e .

Per ulteriori informazioni sulle classi .NET SDK utilizzate per monitorare gli indicizzatori, vedere [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
