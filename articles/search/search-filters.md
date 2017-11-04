---
title: Filtri in Ricerca di Azure | Microsoft Docs
description: "Filtrare in base a identità di sicurezza dell'utente, lingua, località geografica o valori numerici per ridurre i risultati della ricerca nelle query in Ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 2e8721684b1d4ed0e7392d85ea1df0f595860a05
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
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

+ Il parametro `$select` viene usato per specificare i campi da includere in un set di risultati, per limitare in modo efficace la risposta prima di inviarla all'applicazione chiamante. Questo parametro non affina la query né riduce la raccolta di documenti, ma se l'obiettivo è quello di ottenere una risposta granulare, può rappresentare una valida alternativa. 

Per altre informazioni su questi parametri, vedere [Search Documents > Request > Query parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) (Ricerca di documenti > Richiesta > Parametri di query).


## <a name="filters-in-the-query-pipeline"></a>Filtri nella pipeline di query

In fase di query, un parser di filtro accetta i criteri come input, converte l'espressione in espressioni booleane atomiche e crea un albero di filtro, che viene quindi valutato sui campi filtrabili in un indice.  

Il filtro viene applicato prima della ricerca, definendo i documenti da includere nell'elaborazione a valle per il recupero dei documenti e l'assegnazione dei punteggi relativi alla pertinenza. Quando viene usato in combinazione con una stringa di ricerca, il filtro riduce in modo efficace l'area della superficie dell'operazione di ricerca successiva. Se usato da solo (ad esempio, quando la stringa di query è vuota con `search=*`), il criterio di filtro è l'unico input. 

## <a name="filter-definition"></a>Definizione di filtro

I filtri sono espressioni OData, articolate usando un [subset della sintassi OData V4 supportata in Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

È possibile specificare un filtro per ogni operazione di **ricerca**, ma il filtro può includere più campi, più criteri e, se si usa una funzione **ismatch**, più espressioni. In un'espressione filtro multiparte è possibile specificare i predicati in qualsiasi ordine. Il riposizionamento dei predicati in una sequenza specifica non offre un miglioramento delle prestazioni rilevante.

Il limite assoluto per un'espressione filtro è il limite massimo per la richiesta. L'intera richiesta, compreso il filtro, può avere dimensioni massime di 16 MB per POST o 8 KB per GET. I limiti flessibili sono correlati al numero di clausole nell'espressione filtro. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.

Gli esempi seguenti rappresentano definizioni di filtro prototipo in diverse API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
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

```

## <a name="filter-design-patterns"></a>Modelli di progettazione di filtri

Gli esempi seguenti illustrano diversi modelli di progettazione per gli scenari di filtro. Per altre idee, vedere [OData expression syntax > Examples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples) (Sintassi delle espressioni OData -> Esempi).

+ Elemento **$filter** autonomo, senza una stringa di query, utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio e una classificazione. Si noti che la stringa di ricerca è vuota.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinazione di stringa di query e **$filter**, dove il filtro crea il subset e la stringa di query fornisce gli input dei termini per la ricerca full-text nel subset filtrato. L'uso di un filtro con una stringa di query è il modello di codice più comune.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Query composte, separate da "or", ciascuna con i propri criteri di filtro (ad esempio, 'beagle' in 'cane' or 'siamese' in 'gatto'). Le espressioni con OR vengono valutate singolarmente e le risposte di ciascuna di esse vengono combinate in un'unica risposta inviata all'applicazione chiamante. Questo modello di progettazione viene ottenuto tramite la funzione search.ismatch. È possibile usare la versione senza assegnazione di punteggio (search.ismatch) o con assegnazione di punteggio (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Leggere gli articoli seguenti per indicazioni complete sui casi d'uso specifici:

+ [Filtri facet](search-filters-facets.md)
+ [Filtri per linguaggio](search-filters-language.md)
+ [Limitazione per motivi di sicurezza](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisiti dei campi per il filtro

Nell'API REST la proprietà filterable è *attivata* per impostazione predefinita. I campi filtrabili causano un aumento delle dimensioni dell'indice. Assicurarsi di impostare `filterable=FALSE` per i campi che non si prevede di usare in un filtro. Per altre informazioni sulle impostazioni delle definizioni di campo, vedere l'articolo relativo all'operazione [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

In .NET SDK la proprietà filterable è *disattivata* per impostazione predefinita. L'API per l'impostazione della proprietà filtrabile è [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Nell'esempio seguente è impostata nella definizione di campo BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Requisiti di reindicizzazione

Se un campo non è filtrabile e lo si vuole rendere filtrabile, è necessario aggiungere un nuovo campo oppure ricompilare il campo esistente. La modifica di una definizione di campo comporta la modifica della struttura fisica dell'indice. In Ricerca di Azure tutti i percorsi di accesso consentiti vengono indicizzati per offrire alta velocità di query e ciò richiede la ricompilazione delle strutture di dati quando le definizioni di campo cambiano. 

La ricompilazione di singoli campi può essere un'operazione di basso impatto, che richiede solo un'operazione di merge che invia la chiave del documento esistente e i valori associati all'indice, lasciando intatto il resto di ogni documento. Se è necessaria la ricompilazione, vedere i collegamenti seguenti per istruzioni:

 + [Azioni di indicizzazione con .NET SDK](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Azioni di indicizzazione con l'API REST](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Concetti fondamentali sui filtri di testo

I filtri di testo sono validi per i campi stringa da cui si vuole effettuare il pull di una raccolta arbitraria di documenti in base ai valori nel corpo della ricerca.

Per i filtri di testo costituiti da stringhe, non c'è un'analisi lessicale o una separazione delle parole, quindi i confronti riguardano solo le corrispondenze esatte. Si supponga, ad esempio, un campo *f* che contiene "sunny day": non viene trovata la corrispondenza con `$filter=f eq 'Sunny'`, mentre con `$filter=f eq 'Sunny day'` sì. 

Per le stringhe di testo viene fatta distinzione tra maiuscole e minuscole. Non c'è corrispondenza tra parole minuscole e maiuscole: `$filter=f eq 'Sunny day'` non consente di trovare "sunny day".


| Approccio | Descrizione | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funzione che fornisce l'elenco delimitato da virgole di stringhe per un determinato campo. Le stringhe includono i criteri di filtro, che vengono applicati a ogni campo nell'ambito per la query. <br/><br/>`search.in(f, ‘a, b, c’)` è semanticamente equivalente a `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, ad eccezione del fatto che l'esecuzione è molto più veloce quando l'elenco di valori è grande.<br/><br/>Si consiglia di usare la funzione **search.in** per i [filtri di sicurezza](search-security-trimming-for-azure-search.md) e per tutti i filtri costituiti da testo non elaborato di cui trovare la corrispondenza con i valori in un determinato campo. Questo approccio è progettato per offrire velocità. È possibile ottenere un tempo di risposta di frazioni di secondo per centinaia o migliaia di valori. Anche se non c'è alcun limite esplicito per il numero di elementi che è possibile passare alla funzione, la latenza aumenta proporzionalmente al numero di stringhe fornite. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funzione che consente di combinare le operazioni di ricerca full-text con operazioni di filtro esclusivamente booleane nella stessa espressione filtro. Consente più combinazioni di filtro di query in un'unica richiesta. Può essere usata anche con un filtro *contains* per applicare un filtro su una stringa parziale all'interno di una stringa più grande. |  
| [$filter=stringa operatore di campo](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Espressione definita dall'utente composta da campi, operatori e valori. | 

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

Per altri esempi, vedere [OData Filter Expression Syntax > Examples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples) (Sintassi delle espressioni di filtro OData -> Esempi).

## <a name="see-also"></a>Vedere anche

+ [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
