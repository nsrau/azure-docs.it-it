---
title: Come monitorare lo stato e i risultati dell'indicizzatore-ricerca di Azure
description: Monitorare lo stato, lo stato di avanzamento e i risultati degli indicizzatori di ricerca di Azure nella portale di Azure, usando l'API REST o .NET SDK.
ms.date: 06/28/2019
author: RobDixon22
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0096334e06051b9ff474543384febb37bdf1c8e2
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533709"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Come monitorare lo stato e i risultati dell'indicizzatore di ricerca di Azure

Ricerca di Azure offre informazioni sullo stato e sul monitoraggio sulle esecuzioni correnti e cronologiche di ogni indicizzatore.

Il monitoraggio dell'indicizzatore è utile quando si desidera:

* Tenere traccia dello stato di avanzamento di un indicizzatore durante un'esecuzione in corso.
* Esaminare i risultati dell'esecuzione dell'indicizzatore in corso o in precedenza.
* Identificare gli errori dell'indicizzatore di primo livello ed errori o avvisi relativi a singoli documenti indicizzati.

## <a name="get-status-and-history"></a>Ottenere lo stato e la cronologia

È possibile accedere alle informazioni di monitoraggio dell'indicizzatore in diversi modi, tra cui:

* Nel [portale di Azure](#portal)
* Uso dell' [API REST](#restapi)
* Uso di [.NET SDK](#dotnetsdk)

Le informazioni di monitoraggio disponibili per l'indicizzatore includono tutti gli elementi seguenti (anche se i formati di dati variano in base al metodo di accesso usato):

* Informazioni sullo stato dell'indicizzatore stesso
* Informazioni sulla più recente esecuzione dell'indicizzatore, inclusi lo stato, l'ora di inizio e di fine, nonché gli errori e gli avvisi dettagliati.
* Elenco di esecuzioni cronologiche degli indicizzatori e relativi stati, risultati, errori e avvisi.

Gli indicizzatori che elaborano volumi elevati di dati possono richiedere molto tempo per l'esecuzione. Ad esempio, gli indicizzatori che gestiscono milioni di documenti di origine possono essere eseguiti per 24 ore e quindi riavviati quasi immediatamente. Lo stato per gli indicizzatori con volumi elevati può sempre indicare **in corso** nel portale. Anche quando un indicizzatore è in esecuzione, sono disponibili dettagli sullo stato di avanzamento e sulle esecuzioni precedenti.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitorare tramite il portale

È possibile visualizzare lo stato corrente di tutti gli indicizzatori nell'elenco **indicizzatori** della pagina di panoramica del servizio di ricerca.

   ![Elenco di indicizzatori](media/search-monitor-indexers/indexers-list.png "Elenco di indicizzatori")

Quando un indicizzatore è in esecuzione, lo stato nell'elenco viene visualizzato **in corso**e il valore **docs succeeded** indica il numero di documenti elaborati fino a questo momento. Potrebbero essere necessari alcuni minuti prima che il portale aggiorni i valori di stato e i conteggi dei documenti dell'indicizzatore.

Un indicizzatore la cui esecuzione più recente ha avuto esito positivo indica l' **esito**positivo. Un'esecuzione dell'indicizzatore può avere esito positivo anche se i singoli documenti contengono errori, se il numero di errori è inferiore all'impostazione **massima degli elementi non riusciti** dell'indicizzatore.

Se l'esecuzione più recente è terminata con un errore, lo stato viene visualizzato come **non riuscito**. Lo stato **Reset** indica che lo stato di rilevamento delle modifiche dell'indicizzatore è stato reimpostato.

Fare clic su un indicizzatore nell'elenco per visualizzare altri dettagli sulle esecuzioni correnti e recenti dell'indicizzatore.

   ![Riepilogo e cronologia di esecuzione dell'indicizzatore](media/search-monitor-indexers/indexer-summary.png "Riepilogo e cronologia di esecuzione dell'indicizzatore")

Nel grafico di **Riepilogo dell'indicizzatore** viene visualizzato un grafico del numero di documenti elaborati nelle esecuzioni più recenti.

L'elenco **Dettagli esecuzione** Mostra fino a 50 dei risultati dell'esecuzione più recenti.

Fare clic su un risultato dell'esecuzione nell'elenco per visualizzare le specifiche relative all'esecuzione. Sono incluse le ore di inizio e di fine e gli eventuali errori e avvisi che si sono verificati.

   ![Dettagli esecuzione indicizzatore](media/search-monitor-indexers/indexer-execution.png "Dettagli esecuzione indicizzatore")

Se durante l'esecuzione si sono verificati problemi specifici del documento, questi verranno elencati nei campi errori e avvisi.

   ![Dettagli indicizzatore con errori](media/search-monitor-indexers/indexer-execution-error.png "Dettagli indicizzatore con errori")

Gli avvisi sono comuni ad alcuni tipi di indicizzatori e non sempre indicano un problema. Ad esempio, gli indicizzatori che usano servizi cognitivi possono segnalare avvisi quando i file di immagine o PDF non contengono testo da elaborare.

Per ulteriori informazioni sull'analisi degli errori e degli avvisi dell'indicizzatore, vedere [risoluzione dei problemi comuni dell'indicizzatore in ricerca di Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorare con le API REST

È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore usando il [comando Get Indexer status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

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

La cronologia di esecuzione contiene fino alle 50 esecuzioni più recenti, che sono ordinate in ordine cronologico inverso (prima più recenti).

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è per l'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** indica che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato che indica se l'esecuzione specifica è in corso (**in esecuzione**) o è già stata completata con uno stato di **esito positivo**, **transientFailure**o **persistentFailure** . 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce della cronologia di esecuzione separata con uno stato di **reimpostazione** .

Per ulteriori informazioni sui codici di stato e sui dati di monitoraggio dell'indicizzatore, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitorare con .NET SDK

È possibile definire la pianificazione per un indicizzatore usando Azure search .NET SDK. A tale scopo, includere la proprietà **Schedule** durante la creazione o l'aggiornamento di un indicizzatore.

Nell'esempio C# seguente vengono scritte informazioni sullo stato di un indicizzatore e sui risultati dell'esecuzione più recente (o in corso) della console.

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

Si noti che sono presenti due valori di stato diversi. Lo stato di primo livello è lo stato dell'indicizzatore stesso. Lo stato dell'indicizzatore **in esecuzione** indica che l'indicizzatore è configurato correttamente e disponibile per l'esecuzione, ma non è attualmente in esecuzione.

Ogni esecuzione dell'indicizzatore ha anche il proprio stato per determinare se l'esecuzione specifica è in corso (**in esecuzione**) o se è già stata completata con uno stato di **esito positivo** o **TransientError** . 

Quando un indicizzatore viene reimpostato per aggiornare lo stato di rilevamento delle modifiche, viene aggiunta una voce di cronologia separata con uno stato di **reimpostazione** .

Per altre informazioni sui codici di stato e le informazioni di monitoraggio dell'indicizzatore, vedere [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) nell'API REST.

È possibile recuperare informazioni dettagliate sugli errori o gli avvisi specifici del documento enumerando gli elenchi `IndexerExecutionResult.Errors` e `IndexerExecutionResult.Warnings`.

Per ulteriori informazioni sulle classi .NET SDK utilizzate per monitorare gli indicizzatori, vedere [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) e [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
