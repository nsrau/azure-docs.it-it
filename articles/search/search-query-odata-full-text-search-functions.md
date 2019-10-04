---
title: Guida di riferimento alle funzioni di ricerca full-text OData-ricerca di Azure
description: Funzioni di ricerca full-text OData, search. IsMatch e search. ismatchscoring, nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648005"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Funzioni di ricerca full-text OData in ricerca di `search.ismatch` Azure-e`search.ismatchscoring`

Ricerca di Azure supporta la ricerca full-text nel contesto di [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite `search.ismatchscoring` le `search.ismatch` funzioni e. Queste funzioni consentono di combinare la ricerca full-text con un filtro booleano rigoroso in modi che non sono possibili solo usando il parametro `search` di primo livello dell' [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Le `search.ismatch` funzioni `search.ismatchscoring` e sono supportate solo nei filtri nell' [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents). Non sono supportate nelle API [suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) o [AutoComplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica delle `search.ismatch` funzioni `search.ismatchscoring` e:

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
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

### <a name="searchismatch"></a>search. IsMatch

La `search.ismatch` funzione valuta una query di ricerca full-text come parte di un'espressione di filtro. I documenti che corrispondono alla query di ricerca verranno restituiti nel set di risultati. Sono disponibili gli overload seguenti di questa funzione:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | DESCRIZIONE |
| --- | --- | --- |
| `search` | `Edm.String` | Query di ricerca, in una sintassi di query Lucene [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md) . |
| `searchFields` | `Edm.String` | Elenco delimitato da virgole di campi ricercabili in cui eseguire la ricerca; il valore predefinito è tutti i campi disponibili per la ricerca nell'indice. Quando si usa la [ricerca nel campo](query-lucene-syntax.md#bkmk_fields) nel parametro `search`, gli identificatori di campo nella query Lucene eseguono l'override di tutti i campi specificati in questo parametro. |
| `queryType` | `Edm.String` | `'simple'`o `'full'`; il valore predefinito `'simple'`è. Specifica il linguaggio di query usato nel parametro `search`. |
| `searchMode` | `Edm.String` | `'any'`oppure `'all'`, il valore predefinito `'any'`è. Indica se è necessario trovare una corrispondenza per uno o tutti `search` i termini di ricerca nel parametro per poter contare il documento come corrispondenza. Quando si usano gli [operatori booleani Lucene](query-lucene-syntax.md#bkmk_boolean) nel `search` parametro, avranno la precedenza su questo parametro. |

Tutti i parametri precedenti sono equivalenti ai [parametri di richiesta di ricerca corrispondenti nell'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

La `search.ismatch` funzione restituisce un valore di tipo `Edm.Boolean`, che consente di comporre l'elemento con altre sottoespressioni di filtro usando gli [operatori logici](search-query-odata-logical-operators.md)booleani.

> [!NOTE]
> Ricerca di Azure non supporta l' `search.ismatch` uso `search.ismatchscoring` di o all'interno di espressioni lambda. Ciò significa che non è possibile scrivere filtri su raccolte di oggetti che possono correlare le corrispondenze di ricerca full-text con le corrispondenze di filtro Strict sullo stesso oggetto. Per altri dettagli su questa limitazione, oltre ad esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta in ricerca di Azure](search-query-troubleshoot-collection-filters.md). Per informazioni più dettagliate sul motivo per cui questa limitazione esiste, vedere informazioni sui [filtri di raccolta in ricerca di Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

La `search.ismatchscoring` funzione, come la `search.ismatch` funzione, restituisce `true` per i documenti che corrispondono alla query di ricerca full-text passata come parametro. La differenza tra di esse è che il punteggio di pertinenza dei documenti corrispondenti alla query `search.ismatchscoring` contribuirà al punteggio complessivo dei documenti, mentre nel caso di `search.ismatch`, il punteggio dei documenti non verrà modificato. Sono disponibili gli overload seguenti di questa funzione con parametri identici a quelli di `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Entrambe le `search.ismatch` funzioni `search.ismatchscoring` e possono essere utilizzate nella stessa espressione di filtro.

## <a name="examples"></a>Esempi

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

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
