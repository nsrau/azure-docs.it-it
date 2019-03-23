---
title: Suggerimenti automatici di esempio per l'aggiunta di condizioni di elenco a discesa di una casella di ricerca - ricerca di Azure
description: Aggiungi input di query suggeriti creando suggesters e formulare le richieste che richiamano le query suggerite in ricerca di Azure.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373076"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Esempio: Aggiungere suggerimenti automatici per le selezioni di query di elenco a discesa

Termini imputati possono includere un elenco a discesa dei termini di query suggeriti. Si è visto questa funzionalità nei motori di ricerca commerciali, ed è possibile implementare un'esperienza simile in ricerca di Azure usando un [dello strumento suggerimenti costrutto](index-add-suggesters.md) e un'operazione di suggerimenti su una richiesta di query. Questo articolo Usa esempi per illustrare la formulazione di una query di suggerimento automatico, usando un componente di suggerimento che sono già stati definiti. 

## <a name="rest-api"></a>API REST

È possibile usare [Postman](search-fiddler.md) oppure [PowerShell](search-create-index-rest-api.md) e il [REST API](https://docs.microsoft.com/rest/api/searchservice/) per provare questo esempio, ma i risultati verranno restituiti come documenti JSON. Un'esperienza più realistica e visual può essere trovata usando il [esempi di codice per il completamento automatico](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - indicizzare con un costrutto dello strumento suggerimenti

Suggerimenti automatici inizia con un [dello strumento suggerimenti costrutto](index-add-suggesters.md) aggiunto a un indice, costituito da campi che forniscono i valori di elenco a discesa. Dato lo schema seguente, le query suggerite verranno essere formulate usando i valori dei campi categoria e hotelName.

Supponendo che i set di dati di esempio minimo trovato nelle guide introduttive, hotel nomi includono "Rimanere fantasiosi" e "Roach motel" e le categorie includono "Lusso" e "Budget".

Se si dispone già dell'indice degli hotel, è necessario eliminare e ricreare usando lo schema seguente. Questo schema consente di aggiungere un componente di suggerimento. Ricordarsi di ricaricare i dati anche.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - eseguire una query con l'operatore di suggerimenti

Per usare un componente di suggerimento e richiamare suggerimenti automatici, è necessario inviare una [API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) richiesta con GET o POST. A questo tipo una richiesta, il servizio di ricerca esegue l'analisi dei potenziali corrispondenze dopo i primi tre caratteri vengono ricevuti. 

Nell'intestazione della richiesta, impostare **api-key** a una chiave amministratore o di query, e **Content-Type** su application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Le analisi richiesta tutti i campi inclusi nel componente per il suggerimento hotelName e categoria, restituire la risposta seguente:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Per fornire il risultato previsto, il codice client visualizzerebbe i risultati come elenco a discesa in una barra di ricerca.

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - indicizzare con un costrutto dello strumento suggerimenti

In .NET SDK, usare una [classe dello strumento suggerimenti](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Componente per il suggerimento è una raccolta, ma può richiedere solo un elemento.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - eseguire una query con Suggerisci (metodo)

Il [DocumentsOperationsExtensions.Suggest metodo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) viene utilizzata per restituire le stringhe di query suggeriti.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Vedere anche 

+ [Esplorare l'API REST con Postman](search-fiddler.md)
+ [Esempio: Completamento automatico](search-autocomplete-tutorial.md)
+ [Aggiungere suggester a un indice](index-add-suggesters.md)
+ [Aggiungere una classe Suggesters usando .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Chiamare i suggerimenti mediante GET o POST (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Chiamare i suggerimenti con SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) o 