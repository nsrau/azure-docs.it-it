---
title: Sinonimi C# esempio - ricerca di Azure
description: In questo C# esempio informazioni su come aggiungere la funzionalità relativa ai sinonimi a un indice in ricerca di Azure. Un mapping dei sinonimi è un elenco di termini equivalenti. I campi con supporto dei sinonimi espandono le query in modo da includere il termine fornito dall'utente e tutti i relativi sinonimi.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f9cadfcf0c027f4aec4f9d4928872709ee7d3e99
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008975"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Esempio: Aggiungere sinonimi per Ricerca di Azure in C#

I sinonimi espandono una query tramite la corrispondenza con termini considerati semanticamente uguali al termine di input. È ad esempio possibile che si voglia che il termine "auto" consenta di rilevare corrispondenze con documenti contenenti i termini "automobile" o "veicolo". 

In Ricerca di Azure i sinonimi sono definiti in una *mappa di sinonimi* tramite *regole di mapping* che associano termini equivalenti. In questo esempio illustra i passaggi essenziali per l'aggiunta e utilizzo dei sinonimi a un indice esistente. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare i sinonimi creando e pubblicando regole di mapping 
> * Fare riferimento a una mappa di sinonimi in una stringa di query

È possibile creare più mappe di sinonimi, inserirle come risorse a livello di servizio disponibili per qualsiasi indice e quindi fare riferimento alla mappa da usare a livello di campo. In fase di query, oltre a eseguire ricerche in un indice, Ricerca di Azure esegue una ricerca in una mappa di sinonimi, se tale mappa viene specificata nei campi usati nella query.

> [!NOTE]
> I sinonimi sono supportati nelle ultime versioni di API e SDK (api-version=2017-11-11, SDK versione 5.0.0). Non è attualmente disponibile alcun supporto nel portale di Azure. Se l'utente considera utile il supporto dei sinonimi del portale di Azure, può fornire un feedback tramite [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Prerequisiti

I requisiti per l'esercitazione includono i seguenti:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Servizio Ricerca di Azure](search-create-service-portal.md)
* [Libreria Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Come usare Ricerca di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Panoramica

Le query di tipo "prima e dopo" illustrano il valore dei sinonimi. In questo esempio, usare un'applicazione di esempio che esegue query e restituisce i risultati in un indice di esempio. L'applicazione di esempio crea un indice di dimensioni ridotte denominato "hotels", popolato con due documenti. L'applicazione esegue query di ricerca usando termini e frasi non visualizzati nell'indice, abilita la funzionalità relativa ai sinonimi e quindi esegue di nuovo le stesse ricerche. Il codice seguente illustra il flusso complessivo.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Per informazioni sulla procedura per la creazione e il popolamento dell'indice di esempio, vedere [Come utilizzare Ricerca di Azure da un'applicazione .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Query di tipo "prima"

In `RunQueriesWithNonExistentTermsInIndex` vengono eseguite query di ricerca con i termini "five star", "internet" ed "economy AND hotel".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Nessuno dei due documenti indicizzati contiene questi termini, quindi si ottiene l'output seguente dal primo comando `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Abilitare i sinonimi

L'abilitazione dei sinonimi è un processo in due passaggi. Vengono prima di tutto definite e caricate le regole dei sinonimi e quindi vengono configurati i campi per usarle. Il processo viene illustrato in `UploadSynonyms` e `EnableSynonymsInHotelsIndex`.

1. Aggiungere una mappa di sinonimi al servizio di ricerca. In `UploadSynonyms` vengono definite quattro regole nella mappa di sinonimi 'desc-synonymmap' e viene eseguito il caricamento nel servizio.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Una mappa di sinonimi deve essere conforme al formato `solr` dello standard open source. Il formato viene illustrato in [Synonyms in Azure Search](search-synonyms.md) (Sinonimi in Ricerca di Azure) nella sezione `Apache Solr synonym format`.

2. Configurare i campi disponibili per la ricerca per l'uso della mappa di sinonimi nella definizione dell'indice. In `EnableSynonymsInHotelsIndex` vengono abilitati i sinonimi in due campi, `category` e `tags`, tramite l'impostazione della proprietà `synonymMaps` sul nome della mappa di sinonimi appena caricata.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Quando si aggiunge una mappa di sinonimi, non è necessario ricompilare l'indice. È possibile aggiungere una mappa di sinonimi al servizio e quindi correggere le definizioni di campi esistenti in qualsiasi indice in modo da usare la nuova mappa di sinonimi. L'aggiunta di nuovi attributi non ha alcun impatto sulla disponibilità dell'indice. Ciò vale anche per la disabilitazione dei sinonimi per un campo. È possibile impostare semplicemente la proprietà `synonymMaps` su un elenco vuoto.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Query di tipo "dopo"

Dopo il caricamento della mappa di sinonimi e l'aggiornamento dell'indice per l'uso della mappa di sinonimi, la seconda chiamata `RunQueriesWithNonExistentTermsInIndex` restituisce quanto segue:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
La prima query trova il documento dalla regola `five star=>luxury`. La seconda query espande la ricerca usando `internet,wifi` e la terza usando `hotel, motel` e `economy,inexpensive=>budget` nell'individuazione di documenti corrispondenti.

L'aggiunta di sinonimi modifica completamente l'esperienza di ricerca. In questo esempio, la query originale non è riuscito a restituire risultati significativi, anche se i documenti nell'indice sono rilevanti. Abilitando i sinonimi, è possibile espandere un indice in modo da includere termini di uso comune, senza modifiche ai dati sottostanti dell'indice.

## <a name="sample-application-source-code"></a>Esempio di codice sorgente dell'applicazione
Il codice sorgente completo dell'applicazione di esempio utilizzata è disponibile in questa procedura dettagliata su [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Pulire le risorse

Il modo più rapido per la pulizia dopo che è un esempio, eliminando il gruppo di risorse che contiene il servizio di ricerca di Azure. È possibile eliminare ora il gruppo di risorse per eliminare definitivamente tutti gli elementi in esso contenuti. Nel portale, il nome del gruppo di risorse è indicato nella pagina Panoramica del servizio Ricerca di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio viene illustrato il [API REST di sinonimi](https://aka.ms/rgm6rq) in C# codice per creare e pubblicare le regole di mapping e quindi chiamare la mappa di sinonimi per una query. Altre informazioni sono disponibili nella documentazione di riferimento per [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e [API REST](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Come usare i sinonimi in Ricerca di Azure](search-synonyms.md)
