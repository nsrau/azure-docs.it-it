---
title: OData ricerca full-text di riferimento alle funzioni - ricerca di Azure
description: Funzioni OData ricerca full-text, IsMatch e search.ismatchscoring, nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079808"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData funzioni di ricerca full-text in ricerca di Azure - `search.ismatch` e `search.ismatchscoring`

Ricerca di Azure supporta la ricerca full-text nel contesto di [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite il `search.ismatch` e `search.ismatchscoring` funzioni. Queste funzioni consentono di combinare la ricerca full-text con un filtro booleano strict in modi che non sono possibili solo tramite il primo livello `search` parametro il [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> Il `search.ismatch` e `search.ismatchscoring` le funzioni sono supportate solo nei filtri nel [API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents). Non sono supportate nel [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) oppure [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica del `search.ismatch` e `search.ismatchscoring` funzioni:

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
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

### <a name="searchismatch"></a>search.ismatch

Il `search.ismatch` funzione valuta una query di ricerca full-text come parte di un'espressione di filtro. I documenti che corrispondono alla query di ricerca verranno restituiti nel set di risultati. Sono disponibili gli overload seguenti di questa funzione:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Descrizione |
| --- | --- | --- |
| `search` | `Edm.String` | La query di ricerca (in uno [semplice](query-simple-syntax.md) oppure [completo](query-lucene-syntax.md) sintassi di query Lucene). |
| `searchFields` | `Edm.String` | Elenco delimitato da virgole di campi ricercabili da cercare. il valore predefinito è tutti i campi ricercabili nell'indice. Quando si usa [ricerca natura prese in considerazione](query-lucene-syntax.md#bkmk_fields) nel `search` parametro, gli identificatori di campo nella query Lucene ignorare tutti i campi specificati in questo parametro. |
| `queryType` | `Edm.String` | `'simple'` oppure `'full'`; per impostazione predefinita `'simple'`. Specifica il linguaggio di query usato nel parametro `search`. |
| `searchMode` | `Edm.String` | `'any'` oppure `'all'`, per impostazione predefinita `'any'`. Indica se any o all della ricerca termini nel `search` parametro deve essere corrisposte per includere il documento come corrispondenza. Quando si usa la [gli operatori booleani Lucene](query-lucene-syntax.md#bkmk_boolean) nel `search` parametro, si avrà la precedenza su questo parametro. |

Tutti i parametri indicati sopra sono equivalenti ai corrispondenti [i parametri di richiesta di ricerca nell'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Il `search.ismatch` funzione restituisce un valore di tipo `Edm.Boolean`, che consente di comporre, con altre espressioni di filtro secondario usando il valore booleano [operatori logici](search-query-odata-logical-operators.md).

> [!NOTE]
> Ricerca di Azure non supporta l'utilizzo `search.ismatch` o `search.ismatchscoring` all'interno di espressioni lambda. Ciò significa che non è possibile per i filtri di scrittura sulle raccolte di oggetti che è possono correlare corrispondenze di ricerca full-text con corrispondenze con i filtri strict sullo stesso oggetto. Per altre informazioni su questa limitazione, nonché esempi, vedere [risoluzione dei problemi di filtri di raccolta in ricerca di Azure](search-query-troubleshoot-collection-filters.md). Per informazioni dettagliate sui motivi per cui esiste una limitazione, vedere [informazioni sui filtri in ricerca di Azure per raccolta](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

Il `search.ismatchscoring` funzione, come i `search.ismatch` funzionare, restituisce `true` per i documenti che corrispondono alla query di ricerca full-text passato come parametro. La differenza tra di esse è che il punteggio di pertinenza dei documenti corrispondenti alla query `search.ismatchscoring` contribuirà al punteggio complessivo dei documenti, mentre nel caso di `search.ismatch`, il punteggio dei documenti non verrà modificato. Sono disponibili gli overload seguenti di questa funzione con parametri identici a quelli di `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Sia la `search.ismatch` e `search.ismatchscoring` funzioni possono essere usate nell'espressione di filtro stesso.

## <a name="examples"></a>Esempi

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Si noti che questa richiesta non potrebbe essere espressa senza la funzione `search.ismatchscoring`.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Trovare i documenti senza la parola "luxury".

    not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Rooms/Description`.

I documenti che corrispondono solo la seconda clausola della disgiunzione restituirà troppo, gli hotel con `Rating` uguale a 5. Per renderlo cancellare che tali documenti non corrisponde ad alcuno delle parti dell'espressione con punteggio, saranno restituiti con punteggio pari a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Trovare documenti in cui i termini "hotel" e "aeroporto" è entro 5 parole tra loro nella descrizione dell'hotel e dove fumatori non sono consentito in almeno in parte le sale. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
