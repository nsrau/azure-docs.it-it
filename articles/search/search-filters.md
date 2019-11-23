---
title: Filter on search results
titleSuffix: Azure Cognitive Search
description: Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f4ce3cd0db20f76aa6169f15254cf36ee64151a5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406752"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

A *filter* provides criteria for selecting documents used in an Azure Cognitive Search query. Se non si usano i filtri, in una ricerca vengono inclusi tutti i documenti nell'indice. Un filtro definisce l'ambito di una query di ricerca restringendolo a un subset dei documenti. Un filtro può ad esempio limitare la ricerca full-text ai soli prodotti con un marchio o un colore specifico, con un prezzo superiore a una determinata soglia.

Alcune esperienze di ricerca impongono requisiti di filtro come parte dell'implementazione, ma è possibile usare i filtri ogni volta che si vuole limitare una ricerca usando criteri *basati su valore* (ad esempio per limitare l'ambito della ricerca al tipo di prodotto "libri" per la categoria "saggistica" con editore "Simon & Schuster").

Se invece l'obiettivo è una ricerca mirata in *strutture* di dati specifiche (ad esempio se si vuole limitare l'ambito della ricerca al campo delle recensioni dei clienti), sono disponibili metodi alternativi, descritti di seguito.

## <a name="when-to-use-a-filter"></a>Quando usare un filtro

I filtri sono fondamentali per diverse esperienze di ricerca, ad esempio la ricerca di elementi nelle vicinanze, l'esplorazione in base a facet e i filtri di sicurezza, che mostrano solo i documenti che un utente è autorizzato a visualizzare. Se si implementa una di queste esperienze, è necessario un filtro. Le coordinate di georilevazione, la categoria di facet selezionata dall'utente o l'ID di sicurezza del richiedente vengono forniti dal filtro associato alla query di ricerca.

Ecco alcuni scenari di esempio:

1. Usare un filtro per sezionare l'indice in base ai valori di dati nell'indice. Se si ha uno schema con città, tipo di abitazione e attrattive, è possibile creare un filtro per selezionare in modo esplicito i documenti che soddisfano i criteri (Seattle, condomini, lungomare). 

   La ricerca full-text con gli stessi input produce spesso risultati simili, ma un filtro è più preciso, perché richiede una corrispondenza esatta delle condizioni di filtro nel contenuto nell'indice. 

2. Usare un filtro se l'esperienza di ricerca prevede un requisito di filtro:

   * L'[esplorazione in base a facet](search-faceted-navigation.md) usa un filtro per passare la categoria di facet selezionata dall'utente.
   * La ricerca geografica usa un filtro per passare le coordinate della posizione corrente alle app che eseguono ricerche di elementi nelle vicinanze. 
   * I filtri di sicurezza passano gli identificatori di sicurezza come criteri di filtro, in cui una corrispondenza nell'indice funge da proxy per i diritti di accesso al documento.

3. Usare un filtro per applicare criteri di ricerca in un campo numerico. 

   I campi numerici possono essere recuperati nel documento e visualizzati nei risultati della ricerca, ma non sono disponibili per la ricerca (soggetti a ricerca full-text) individualmente. Se sono necessari criteri di selezione basati su dati numerici, usare un filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Metodi alternativi per ridurre l'ambito

I filtri non sono l'unica opzione disponibile per limitare i risultati della ricerca. Le alternative seguenti potrebbero essere più appropriate, a seconda dell'obiettivo:

 + Il parametro di query `searchFields` delimita la ricerca a campi specifici. Se, ad esempio, l'indice fornisce campi separati per le descrizioni in inglese e in spagnolo, è possibile usare searchFields per definire i campi da usare per la ricerca full-text. 

+ Il parametro `$select` viene usato per specificare i campi da includere in un set di risultati, per limitare in modo efficace la risposta prima di inviarla all'applicazione chiamante. This parameter does not refine the query or reduce the document collection, but if a smaller response is your goal, this parameter is an option to consider. 

Per altre informazioni su questi parametri, vedere [Search Documents > Request > Query parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) (Ricerca di documenti > Richiesta > Parametri di query).


## <a name="how-filters-are-executed"></a>How filters are executed

At query time, a filter parser accepts criteria as input, converts the expression into atomic Boolean expressions represented as a tree, and then evaluates the filter tree over filterable fields in an index.

Filtering occurs in tandem with search, qualifying which documents to include in downstream processing for document retrieval and relevance scoring. When paired with a search string, the filter effectively reduces the recall set of the subsequent search operation. Se usato da solo (ad esempio, quando la stringa di query è vuota con `search=*`), il criterio di filtro è l'unico input. 

## <a name="defining-filters"></a>Definizione di filtri
Filters are OData expressions, articulated using a [subset of OData V4 syntax supported in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

You can specify one filter for each **search** operation, but the filter itself can include multiple fields, multiple criteria, and if you use an **ismatch** function, multiple full-text search expressions. In a multi-part filter expression, you can specify predicates in any order (subject to the rules of operator precedence). Il riposizionamento dei predicati in una sequenza specifica non offre un miglioramento delle prestazioni rilevante.

One of the limits on a filter expression is the maximum size limit of the request. L'intera richiesta, compreso il filtro, può avere dimensioni massime di 16 MB per POST o 8 KB per GET. There is also a limit on the number of clauses in your filter expression. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.

Gli esempi seguenti rappresentano definizioni di filtro prototipo in diverse API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filter usage patterns

The following examples illustrate several usage patterns for filter scenarios. Per altre idee, vedere [OData expression syntax > Examples](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) (Sintassi delle espressioni OData -> Esempi).

+ Elemento **$filter** autonomo, senza una stringa di query, utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Notice the search string is just an asterisk, which means "match all documents".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Combinazione di stringa di query e **$filter**, dove il filtro crea il subset e la stringa di query fornisce gli input dei termini per la ricerca full-text nel subset filtrato. The addition of terms (walking distance theaters) introduces search scores in the results, where documents that best match the terms are ranked higher. Using a filter with a query string is the most common usage pattern.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Query composte, separate da "or", ciascuna con i propri criteri di filtro (ad esempio, 'beagle' in 'cane' or 'siamese' in 'gatto'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Leggere gli articoli seguenti per indicazioni complete sui casi d'uso specifici:

+ [Filtri facet](search-filters-facets.md)
+ [Filtri per linguaggio](search-filters-language.md)
+ [Limitazione per motivi di sicurezza](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisiti dei campi per il filtro

In the REST API, filterable is *on* by default for simple fields. I campi filtrabili causano un aumento delle dimensioni dell'indice. Assicurarsi di impostare `"filterable": false` per i campi che non si prevede di usare in un filtro. Per altre informazioni sulle impostazioni delle definizioni di campo, vedere l'articolo relativo all'operazione [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

In .NET SDK la proprietà filterable è *disattivata* per impostazione predefinita. You can make a field filterable by setting the [IsFilterable property](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) of the corresponding [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object to `true`. You can also do this declaratively by using the [IsFilterable attribute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Making an existing field filterable

You can't modify existing fields to make them filterable. Instead, you need to add a new field, or rebuild the index. For more information about rebuilding an index or repopulating fields, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Concetti fondamentali sui filtri di testo

Text filters match string fields against literal strings that you provide in the filter. Unlike full-text search, there is no lexical analysis or word-breaking for text filters, so comparisons are for exact matches only. For example, assume a field *f* contains "sunny day", `$filter=f eq 'Sunny'` does not match, but `$filter=f eq 'sunny day'` will. 

Per le stringhe di testo viene fatta distinzione tra maiuscole e minuscole. There is no lower-casing of upper-cased words: `$filter=f eq 'Sunny day'` will not find "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Approaches for filtering on text

| Approccio | Description | Quando usare questa opzione |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | A function that matches a field against a delimited list of strings. | Recommended for [security filters](search-security-trimming-for-azure-search.md) and for any filters where many raw text values need to be matched with a string field. The **search.in** function is designed for speed and is much faster than explicitly comparing the field against each string using `eq` and `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funzione che consente di combinare le operazioni di ricerca full-text con operazioni di filtro esclusivamente booleane nella stessa espressione filtro. | Use **search.ismatch** (or its scoring equivalent, **search.ismatchscoring**) when you want multiple search-filter combinations in one request. Può essere usata anche con un filtro *contains* per applicare un filtro su una stringa parziale all'interno di una stringa più grande. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Espressione definita dall'utente composta da campi, operatori e valori. | Use this when you want to find exact matches between a string field and a string value. |

## <a name="numeric-filter-fundamentals"></a>Concetti fondamentali sui filtri numerici

I campi numerici non sono di tipo `searchable` nel contesto della ricerca full-text. Solo le stringhe sono soggette a ricerca full-text. Se, ad esempio, si immette 99,99 come termine di ricerca, non si otterranno gli articoli con prezzo $99,99. Verranno invece visualizzati gli articoli con il numero 99 nei campi stringa del documento. Se quindi si hanno dati numerici, il presupposto è che verranno usati per i filtri, inclusi intervalli, facet, gruppi e così via. 

I documenti che contengono campi numerici (prezzo, dimensioni, SKU, ID) forniscono tali valori nei risultati della ricerca se il campo è contrassegnato come `retrievable`. Il concetto è che la ricerca full-text non è applicabile ai tipi di campi numerici.

## <a name="next-steps"></a>Passaggi successivi

Provare a usare **Esplora ricerche** nel portale per inviare query con parametri **$filter**. L'[indice real-estate-sample](search-get-started-portal.md) fornisce risultati interessanti per le query di filtro seguenti quando le si incolla nella barra di ricerca:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Per altri esempi, vedere [OData Filter Expression Syntax > Examples](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) (Sintassi delle espressioni di filtro OData -> Esempi).

## <a name="see-also"></a>Vedi anche

+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
