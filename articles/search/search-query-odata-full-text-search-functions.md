---
title: Informazioni di riferimento sulle funzioni di ricerca full-text OData
titleSuffix: Azure Cognitive Search
description: Funzioni di ricerca full-text OData, search.ismatch e search.ismatchscoring, nelle query di Ricerca cognitiva di Azure.OData full-text search functions, search.ismatch and search.ismatchscoring, in Azure Cognitive Search queries.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793357"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funzioni di ricerca full-text OData `search.ismatch` in Ricerca cognitiva di Azure - e`search.ismatchscoring`

Ricerca cognitiva di Azure supporta la ricerca full-text `search.ismatch` nel `search.ismatchscoring` contesto delle espressioni di [filtro OData](query-odata-filter-orderby-syntax.md) tramite le funzioni e . Queste funzioni consentono di combinare la ricerca full-text con un filtro booleano `search` rigoroso in modi che non sono possibili solo utilizzando il parametro di primo livello dell'API [di ricerca.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

> [!NOTE]
> Le `search.ismatch` `search.ismatchscoring` funzioni e sono supportate solo nei filtri nell'API [di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents). Non sono supportati nelle API [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) o [Completamento automatico.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.ismatch` ) `search.ismatchscoring` definisce la grammatica delle funzioni e :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

### <a name="searchismatch"></a>search.ismatch (ricerca.ismatch)

La `search.ismatch` funzione valuta una query di ricerca full-text come parte di un'espressione di filtro. I documenti che corrispondono alla query di ricerca verranno restituiti nel set di risultati. Sono disponibili gli overload seguenti di questa funzione:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Descrizione |
| --- | --- | --- |
| `search` | `Edm.String` | La query di ricerca (nella sintassi di query Lucene [semplice](query-simple-syntax.md) o [completa).](query-lucene-syntax.md) |
| `searchFields` | `Edm.String` | Elenco delimitato da virgole di campi ricercabili in cui eseguire la ricerca; per impostazione predefinita tutti i campi ricercabili nell'indice. Quando si utilizza la `search` [ricerca campiata](query-lucene-syntax.md#bkmk_fields) nel parametro, gli identificatori di campo nella query Lucene sostituiscono tutti i campi specificati in questo parametro. |
| `queryType` | `Edm.String` | `'simple'`o `'full'`; il valore `'simple'`predefinito è . Specifica il linguaggio di query usato nel parametro `search`. |
| `searchMode` | `Edm.String` | `'any'`o `'all'`, il `'any'`valore predefinito è . Indica se alcuni o tutti i `search` termini di ricerca nel parametro devono corrispondere per poter contare il documento come corrispondenza. Quando si utilizzano gli `search` [operatori booleani Lucene](query-lucene-syntax.md#bkmk_boolean) nel parametro, avranno la precedenza su questo parametro. |

Tutti i parametri di cui sopra sono equivalenti ai parametri della richiesta di ricerca corrispondenti [nell'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

La `search.ismatch` funzione restituisce `Edm.Boolean`un valore di tipo , che consente di comporla con altre sottoespressioni di filtro utilizzando gli [operatori logici booleani](search-query-odata-logical-operators.md).

> [!NOTE]
> Ricerca cognitiva di `search.ismatch` Azure `search.ismatchscoring` non supporta l'uso o l'interno di espressioni lambda. Ciò significa che non è possibile scrivere filtri su raccolte di oggetti in grado di correlare le corrispondenze di ricerca full-text con corrispondenze di filtro rigorose sullo stesso oggetto. Per altre informazioni su questa limitazione, nonché sugli esempi, vedere [Risoluzione dei problemi relativi ai filtri](search-query-troubleshoot-collection-filters.md)di raccolta in Ricerca cognitiva di Azure.For more details on this limitation as as examples, see Troubleshooting collection filters in Azure Cognitive Search . Per informazioni più approfondite sul motivo per cui questa limitazione esiste, vedere Informazioni sui filtri di [raccolta in Ricerca cognitiva](search-query-understand-collection-filters.md)di Azure .For more in-depth information on why this limitation exists, see Understanding collection filters in Azure Cognitive Search .


### <a name="searchismatchscoring"></a>search.ismatchscoring

La `search.ismatchscoring` funzione, `search.ismatch` come la `true` funzione, restituisce per i documenti che corrispondono alla query di ricerca full-text passata come parametro. La differenza tra di esse è che il punteggio di pertinenza dei documenti corrispondenti alla query `search.ismatchscoring` contribuirà al punteggio complessivo dei documenti, mentre nel caso di `search.ismatch`, il punteggio dei documenti non verrà modificato. Sono disponibili gli overload seguenti di questa funzione con parametri identici a quelli di `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Entrambe `search.ismatch` `search.ismatchscoring` le funzioni e possono essere utilizzate nella stessa espressione di filtro.

## <a name="examples"></a>Esempi

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Si noti che questa richiesta non potrebbe essere espressa senza la funzione `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Trovare i documenti senza la parola "luxury".

    not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Rooms/Description`.

I documenti che corrispondevano solo alla seconda clausola della disgiunzione saranno restituiti troppo - alberghi con `Rating` uguale a 5. Per chiarire che tali documenti non corrispondono a nessuna delle parti segnate dell'espressione, verranno restituiti con punteggio uguale a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Trova i documenti in cui i termini "hotel" e "aeroporto" sono entro 5 parole l'uno dall'altro nella descrizione dell'hotel, e dove il fumo non è consentito in almeno alcune delle camere. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
