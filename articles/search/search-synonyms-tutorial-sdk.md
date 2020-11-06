---
title: Sinonimi di esempio in C#
titleSuffix: Azure Cognitive Search
description: In questo esempio di C# si apprenderà come aggiungere la funzionalità sinonimi a un indice in ricerca cognitiva di Azure. Un mapping dei sinonimi è un elenco di termini equivalenti. I campi con supporto dei sinonimi espandono le query in modo da includere il termine fornito dall'utente e tutti i relativi sinonimi.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b97b223ac180df7f8eb07ad8eaab66847f50776
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422995"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Esempio: aggiungere sinonimi per ricerca cognitiva di Azure in C #

I sinonimi espandono una query tramite la corrispondenza con termini considerati semanticamente uguali al termine di input. È ad esempio possibile che si voglia che il termine "auto" consenta di rilevare corrispondenze con documenti contenenti i termini "automobile" o "veicolo". 

In ricerca cognitiva di Azure, i sinonimi vengono definiti in una *mappa di sinonimi* tramite *regole di mapping* che associano termini equivalenti. In questo esempio vengono illustrati i passaggi essenziali per l'aggiunta e l'utilizzo di sinonimi con un indice esistente.

In questo esempio si apprenderà come:

> [!div class="checklist"]
> * Creare una mappa di sinonimi usando la [classe SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Impostare la [Proprietà SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) nei campi che devono supportare l'espansione delle query tramite sinonimi.

È possibile eseguire una query su un campo abilitato per sinonimi come in genere. Non è necessaria alcuna sintassi di query aggiuntiva per accedere ai sinonimi.

È possibile creare più mappe di sinonimi, inserirle come risorse a livello di servizio disponibili per qualsiasi indice e quindi fare riferimento alla mappa da usare a livello di campo. In fase di query, oltre a eseguire ricerche in un indice, Azure ricerca cognitiva esegue una ricerca in una mappa di sinonimi, se ne viene specificata una nei campi utilizzati nella query.

> [!NOTE]
> I sinonimi possono essere creati a livello di codice, ma non nel portale.

## <a name="prerequisites"></a>Prerequisiti

I requisiti per l'esercitazione includono i seguenti:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Servizio ricerca cognitiva di Azure](search-create-service-portal.md)
* [Azure.Search.Docpacchetto uments](https://www.nuget.org/packages/Azure.Search.Documents/)

Se non si ha familiarità con la libreria client .NET, vedere [come usare Azure ricerca cognitiva in .NET](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Codice di esempio

È possibile trovare il codice sorgente completo dell'applicazione di esempio usata in questo esempio in [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Panoramica

Le query before-and-after vengono usate per illustrare il valore dei sinonimi. In questo esempio, un'applicazione di esempio esegue query e restituisce i risultati in un indice "Hotels" di esempio popolato con due documenti. In primo luogo, l'applicazione esegue query di ricerca usando termini e frasi che non sono presenti nell'indice. In secondo luogo, il codice Abilita la funzionalità sinonimi, quindi emette nuovamente le stesse query, restituendo i risultati in base alle corrispondenze nella mappa dei sinonimi. 

Il codice seguente illustra il flusso complessivo.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Query di tipo "prima"

In `RunQueriesWithNonExistentTermsInIndex` vengono eseguite query di ricerca con i termini "five star", "internet" ed "economy AND hotel".

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Nessuno dei due documenti indicizzati contiene i termini, quindi si ottiene l'output seguente dal primo `RunQueriesWithNonExistentTermsInIndex` :  **nessun documento corrispondente**.

## <a name="enable-synonyms"></a>Abilitare i sinonimi

Dopo l'esecuzione delle query "before", il codice di esempio Abilita i sinonimi. L'abilitazione dei sinonimi è un processo in due passaggi. Prima di tutto, definire e caricare le regole di sinonimo. In secondo luogo, configurare i campi per usarli. Il processo viene illustrato in `UploadSynonyms` e `EnableSynonymsInHotelsIndex`.

1. Aggiungere una mappa di sinonimi al servizio di ricerca. In `UploadSynonyms` vengono definite quattro regole nella mappa di sinonimi 'desc-synonymmap' e viene eseguito il caricamento nel servizio.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Configurare i campi disponibili per la ricerca per l'uso della mappa di sinonimi nella definizione dell'indice. In `AddSynonymMapsToFields` vengono abilitati i sinonimi in due campi, `category` e `tags`, tramite l'impostazione della proprietà `SynonymMapNames` sul nome della mappa di sinonimi appena caricata.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Quando si aggiunge una mappa di sinonimi, non è necessario ricompilare l'indice. È possibile aggiungere una mappa di sinonimi al servizio e quindi correggere le definizioni di campi esistenti in qualsiasi indice in modo da usare la nuova mappa di sinonimi. L'aggiunta di nuovi attributi non ha alcun impatto sulla disponibilità dell'indice. Ciò vale anche per la disabilitazione dei sinonimi per un campo. È possibile impostare semplicemente la proprietà `SynonymMapNames` su un elenco vuoto.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Query di tipo "dopo"

Dopo il caricamento della mappa di sinonimi e l'aggiornamento dell'indice per l'uso della mappa di sinonimi, la seconda chiamata `RunQueriesWithNonExistentTermsInIndex` restituisce quanto segue:

```
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

La prima query trova il documento dalla regola `five star=>luxury`. La seconda query espande la ricerca usando `internet,wifi` e la terza usando `hotel, motel` e `economy,inexpensive=>budget` nell'individuazione di documenti corrispondenti.

L'aggiunta di sinonimi modifica completamente l'esperienza di ricerca. In questo esempio, le query originali non sono in grado di restituire risultati significativi anche se i documenti nell'indice sono rilevanti. Abilitando i sinonimi, è possibile espandere un indice in modo da includere termini di uso comune, senza modifiche ai dati sottostanti dell'indice.

## <a name="clean-up-resources"></a>Pulire le risorse

Il modo più rapido per eseguire la pulizia dopo un esempio consiste nell'eliminare il gruppo di risorse che contiene il servizio ricerca cognitiva di Azure. È possibile eliminare ora il gruppo di risorse per eliminare definitivamente tutti gli elementi in esso contenuti. Nel portale il nome del gruppo di risorse è indicato nella pagina Panoramica del servizio Ricerca cognitiva di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata illustrata la funzionalità sinonimi del codice C# per creare e pubblicare regole di mapping e quindi chiamare la mappa dei sinonimi in una query. Altre informazioni sono disponibili nella documentazione di riferimento per [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) e [API REST](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Come usare i sinonimi in Azure ricerca cognitiva](search-synonyms.md)