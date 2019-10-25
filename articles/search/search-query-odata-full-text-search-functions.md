---
title: Informazioni di riferimento sulle funzioni di ricerca full-text OData
titleSuffix: Azure Cognitive Search
description: Funzioni di ricerca full-text OData, search. IsMatch e search. ismatchscoring, in query ricerca cognitiva di Azure.
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793357"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funzioni di ricerca full-text OData in Azure ricerca cognitiva-`search.ismatch` e `search.ismatchscoring`

Azure ricerca cognitiva supporta la ricerca full-text nel contesto di [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite le funzioni `search.ismatch` e `search.ismatchscoring`. Queste funzioni consentono di combinare la ricerca full-text con un filtro booleano rigoroso in modi che non sono possibili solo usando il parametro `search` di primo livello dell' [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Le funzioni `search.ismatch` e `search.ismatchscoring` sono supportate solo nei filtri nell' [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents). Non sono supportate nelle API [suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) o [AutoComplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica delle funzioni `search.ismatch` e `search.ismatchscoring`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

### <a name="searchismatch"></a>search. IsMatch

La funzione `search.ismatch` valuta una query di ricerca full-text come parte di un'espressione di filtro. I documenti che corrispondono alla query di ricerca verranno restituiti nel set di risultati. Sono disponibili gli overload seguenti di questa funzione:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Description |
| --- | --- | --- |
| `search` | `Edm.String` | Query di ricerca, in una sintassi di query Lucene [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md) . |
| `searchFields` | `Edm.String` | Elenco delimitato da virgole di campi ricercabili in cui eseguire la ricerca; il valore predefinito è tutti i campi disponibili per la ricerca nell'indice. Quando si usa la [ricerca in campo](query-lucene-syntax.md#bkmk_fields) nel parametro `search`, gli identificatori di campo nella query Lucene eseguono l'override di tutti i campi specificati in questo parametro. |
| `queryType` | `Edm.String` | `'simple'` o `'full'`; il valore predefinito è `'simple'`. Specifica il linguaggio di query usato nel parametro `search`. |
| `searchMode` | `Edm.String` | `'any'` o `'all'`, il valore predefinito è `'any'`. Indica se è necessario trovare una corrispondenza per uno o tutti i termini di ricerca nel parametro `search` per poter contare il documento come corrispondenza. Quando si usano gli [operatori booleani Lucene](query-lucene-syntax.md#bkmk_boolean) nel parametro `search`, avranno la precedenza su questo parametro. |

Tutti i parametri precedenti sono equivalenti ai [parametri di richiesta di ricerca corrispondenti nell'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

La funzione `search.ismatch` restituisce un valore di tipo `Edm.Boolean`, che consente di comporre l'elemento con altre sottoespressioni di filtro usando gli [operatori logici](search-query-odata-logical-operators.md)booleani.

> [!NOTE]
> Azure ricerca cognitiva non supporta l'uso di `search.ismatch` o `search.ismatchscoring` all'interno di espressioni lambda. Ciò significa che non è possibile scrivere filtri su raccolte di oggetti che possono correlare le corrispondenze di ricerca full-text con le corrispondenze di filtro Strict sullo stesso oggetto. Per altri dettagli su questa limitazione, oltre ad esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta in Azure ricerca cognitiva](search-query-troubleshoot-collection-filters.md). Per informazioni più dettagliate sul motivo per cui questa limitazione esiste, vedere informazioni sui [filtri di raccolta in ricerca cognitiva di Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search. ismatchscoring

La funzione `search.ismatchscoring`, come la funzione `search.ismatch`, restituisce `true` per i documenti che corrispondono alla query di ricerca full-text passata come parametro. La differenza tra di esse è che il punteggio di pertinenza dei documenti corrispondenti alla query `search.ismatchscoring` contribuirà al punteggio complessivo dei documenti, mentre nel caso di `search.ismatch`, il punteggio dei documenti non verrà modificato. Sono disponibili gli overload seguenti di questa funzione con parametri identici a quelli di `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Entrambe le funzioni `search.ismatch` e `search.ismatchscoring` possono essere utilizzate nella stessa espressione di filtro.

## <a name="examples"></a>esempi

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Si noti che questa richiesta non potrebbe essere espressa senza la funzione `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Trovare i documenti senza la parola "luxury".

    not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Rooms/Description`.

Verranno restituiti anche i documenti corrispondenti solo alla seconda clausola della disgiunzione, ovvero Hotel con `Rating` uguale a 5. Per rendere chiaro che i documenti non corrispondono a nessuna delle parti con punteggio dell'espressione, verranno restituiti con punteggio uguale a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Trovare i documenti in cui i termini "Hotel" e "Airport" si trovano entro 5 parole l'uno dall'altro nella descrizione dell'hotel e in cui il fumo non è consentito in almeno alcune stanze. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
