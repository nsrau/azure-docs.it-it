---
title: Filtri per definire l'ambito dei risultati della ricerca in un indice - Ricerca di Azure
description: Filtrare in base a identità di sicurezza dell'utente, lingua, località geografica o valori numerici per ridurre i risultati della ricerca nelle query in Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 49af6f1f535df098aa45cccd7e2d629ff6ccef50
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649833"
---
# <a name="filters-in-azure-search"></a>Filtri in Ricerca di Azure 

Un *filtro* fornisce i criteri per la selezione dei documenti usati in una query di Ricerca di Azure. Se non si usano i filtri, in una ricerca vengono inclusi tutti i documenti nell'indice. Un filtro definisce l'ambito di una query di ricerca restringendolo a un subset dei documenti. Un filtro può ad esempio limitare la ricerca full-text ai soli prodotti con un marchio o un colore specifico, con un prezzo superiore a una determinata soglia.

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

+ Il parametro `$select` viene usato per specificare i campi da includere in un set di risultati, per limitare in modo efficace la risposta prima di inviarla all'applicazione chiamante. Questo parametro non perfeziona la query o riduce la raccolta dei documenti, ma se è l'obiettivo di una risposta più piccola, questo parametro è un'opzione da considerare. 

Per altre informazioni su questi parametri, vedere [Search Documents > Request > Query parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) (Ricerca di documenti > Richiesta > Parametri di query).


## <a name="how-filters-are-executed"></a>Modalità di esecuzione dei filtri

In fase di query, un parser di filtro accetta criteri come input, converte l'espressione in espressioni booleane atomiche rappresentate come albero e quindi valuta l'albero dei filtri su campi filtrabili in un indice.

Il filtro si verifica in tandem con la ricerca, qualificando i documenti da includere nell'elaborazione downstream per il recupero dei documenti e il Punteggio di pertinenza. Una volta abbinato a una stringa di ricerca, il filtro riduce efficacemente il set di richiamo della successiva operazione di ricerca. Se usato da solo (ad esempio, quando la stringa di query è vuota con `search=*`), il criterio di filtro è l'unico input. 

## <a name="defining-filters"></a>Definizione di filtri

I filtri sono espressioni OData, articolate usando un [subset della sintassi OData V4 supportata in Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

È possibile specificare un filtro per ogni operazione di **ricerca** , ma il filtro stesso può includere più campi, più criteri e se si utilizza una funzione **IsMatch** , più espressioni di ricerca full-text. In un'espressione di filtro multiparte, è possibile specificare predicati in qualsiasi ordine (in base alle regole di precedenza degli operatori). Il riposizionamento dei predicati in una sequenza specifica non offre un miglioramento delle prestazioni rilevante.

Uno dei limiti di un'espressione di filtro è il limite di dimensione massima della richiesta. L'intera richiesta, compreso il filtro, può avere dimensioni massime di 16 MB per POST o 8 KB per GET. Esiste inoltre un limite al numero di clausole nell'espressione di filtro. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.

Gli esempi seguenti rappresentano definizioni di filtro prototipo in diverse API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrare i modelli di utilizzo

Negli esempi seguenti vengono illustrati diversi modelli di utilizzo per gli scenari di filtro. Per altre idee, vedere [OData expression syntax > Examples](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) (Sintassi delle espressioni OData -> Esempi).

+ Elemento **$filter** autonomo, senza una stringa di query, utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Si noti che la stringa di ricerca è semplicemente un asterisco, che significa "Match all Documents".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinazione di stringa di query e **$filter**, dove il filtro crea il subset e la stringa di query fornisce gli input dei termini per la ricerca full-text nel subset filtrato. L'uso di un filtro con una stringa di query è il modello di utilizzo più comune.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Query composte, separate da "or", ciascuna con i propri criteri di filtro (ad esempio, 'beagle' in 'cane' or 'siamese' in 'gatto'). Le espressioni combinate con `or` vengono valutate singolarmente, con l'Unione di documenti corrispondenti a ogni espressione restituita nella risposta. Questo modello di utilizzo viene effettuato tramite `search.ismatchscoring` la funzione. È anche possibile usare la versione senza punteggio, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  È inoltre possibile combinare la ricerca full-text tramite `search.ismatchscoring` con i filtri utilizzando `and` anziché `or`, ma ciò equivale dal punto di vista funzionale all'utilizzo `search` dei `$filter` parametri e in una richiesta di ricerca. Ad esempio, le due query seguenti producono lo stesso risultato:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Leggere gli articoli seguenti per indicazioni complete sui casi d'uso specifici:

+ [Filtri facet](search-filters-facets.md)
+ [Filtri per linguaggio](search-filters-language.md)
+ [Limitazione per motivi di sicurezza](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisiti dei campi per il filtro

Nell'API REST, il filtro è attivo per impostazione predefinita per i campi semplici. I campi filtrabili causano un aumento delle dimensioni dell'indice. Assicurarsi di impostare `"filterable": false` per i campi che non si prevede di usare in un filtro. Per altre informazioni sulle impostazioni delle definizioni di campo, vedere l'articolo relativo all'operazione [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

In .NET SDK la proprietà filterable è *disattivata* per impostazione predefinita. È possibile rendere un campo filtrabile impostando la [proprietà di filtro](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) dell'oggetto [campo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) corrispondente su `true`. È anche possibile eseguire questa operazione in modo dichiarativo usando l' [attributo di filtro](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Nell'esempio seguente l'attributo viene impostato sulla `BaseRate` proprietà di una classe di modello che esegue il mapping alla definizione dell'indice.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Creazione di un campo filtrabile esistente

Non è possibile modificare i campi esistenti per renderli filtrabili. È invece necessario aggiungere un nuovo campo o ricompilare l'indice. Per altre informazioni sulla ricompilazione di un indice o sul ripopolamento dei campi, vedere [come ricompilare un indice di ricerca di Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Concetti fondamentali sui filtri di testo

I filtri di testo corrispondono ai campi stringa rispetto alle stringhe letterali fornite nel filtro. Diversamente dalla ricerca full-text, non esiste alcuna analisi lessicale o suddivisione in parole per i filtri di testo, quindi i confronti sono solo per corrispondenze esatte. Si supponga, ad esempio, che un campo *f* contenga " `$filter=f eq 'Sunny'` Sunny Day", non `$filter=f eq 'sunny day'` corrisponda a, ma a. 

Per le stringhe di testo viene fatta distinzione tra maiuscole e minuscole. Non sono presenti maiuscole e minuscole di parole maiuscole: `$filter=f eq 'Sunny day'` non troverà "Sunny Day".

### <a name="approaches-for-filtering-on-text"></a>Approcci per l'applicazione di filtri al testo

| Approccio | Descrizione | Quando usare le autorizzazioni |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funzione che corrisponde a un campo rispetto a un elenco di stringhe delimitato. | Consigliato per i [filtri di sicurezza](search-security-trimming-for-azure-search.md) e per tutti i filtri in cui è necessario trovare una corrispondenza per molti valori di testo non elaborati con un campo stringa. La funzione **search.in** è progettata per la velocità ed è molto più veloce rispetto a confrontare in modo esplicito il `eq` campo `or`con ogni stringa utilizzando e. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funzione che consente di combinare le operazioni di ricerca full-text con operazioni di filtro esclusivamente booleane nella stessa espressione filtro. | Usare **search. IsMatch** (o l'equivalente di assegnazione dei punteggi, **search. ismatchscoring**) quando si desiderano più combinazioni di filtri di ricerca in un'unica richiesta. Può essere usata anche con un filtro *contains* per applicare un filtro su una stringa parziale all'interno di una stringa più grande. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Espressione definita dall'utente composta da campi, operatori e valori. | Utilizzare questo valore quando si desidera trovare corrispondenze esatte tra un campo stringa e un valore stringa. |

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

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
