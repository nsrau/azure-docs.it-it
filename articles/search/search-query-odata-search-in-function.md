---
title: Riferimento alle funzioni di OData search.in - ricerca di Azure
description: Funzione search.in OData nelle query di ricerca di Azure.
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
ms.openlocfilehash: f72a59aac448796cf15220e15a3c8a4f12803bb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079730"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` in ricerca di Azure (funzione)

Uno scenario comune in [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) consiste nel verificare se un singolo campo in ogni documento è uguale a uno dei molti valori possibili. Ad esempio, si tratta di come implementano alcune applicazioni [la rimozione della protezione](search-security-trimming-for-azure-search.md) : selezionando un campo che contiene uno o più ID dell'entità rispetto a un elenco di ID dell'entità che rappresenta l'utente invia la query. Un modo per scrivere una query simile alla seguente consiste nell'usare la [ `eq` ](search-query-odata-comparison-operators.md) e [ `or` ](search-query-odata-logical-operators.md) operatori:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Tuttavia, è più breve consente di scrivere, utilizzando il `search.in` funzione:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Oltre a essere più breve e facile da leggere, tramite `search.in` fornisce inoltre [vantaggi nelle prestazioni](#bkmk_performance) ed è possibile evitare alcuni [limite dei filtri di dimensioni](search-query-odata-filter.md#bkmk_limits) quando sono presenti centinaia o anche migliaia di valori Per includere nel filtro. Per questo motivo, è consigliabile usare `search.in` anziché una disgiunzione più complessa di espressioni di uguaglianza.

> [!NOTE]
> Versione 4.01 dello standard OData ha recentemente introdotto il [ `in` operator](http://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), che presenta un comportamento simile come il `search.in` funzione in ricerca di Azure. Tuttavia, ricerca di Azure non supporta questo operatore, pertanto è necessario usare il `search.in` funzione.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica del `search.in` funzione:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Il `search.in` funzione verifica se un campo stringa specificata o variabile di intervallo è uguale a uno di un determinato elenco di valori. L'uguaglianza tra la variabile e ogni valore nell'elenco viene determinato in modo tra maiuscole e minuscole, esattamente come per il `eq` operatore. Un'espressione come `search.in(myfield, 'a, b, c')` equivale quindi a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, tranne per il fatto che `search.in` garantisce prestazioni di gran lunga migliori.

Esistono due overload del `search.in` funzione:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Descrizione |
| --- | --- | --- |
| `variable` | `Edm.String` | Un riferimento di campo stringa (o una variabile di intervallo su un campo di insieme di stringhe nel caso in cui `search.in` viene usato all'interno di un' `any` o `all` espressione). |
| `valueList` | `Edm.String` | Stringa contenente un elenco delimitato da virgole di valori da confrontare con il `variable` parametro. Se il `delimiters` parametro viene omesso, i delimitatori predefiniti sono spazio e virgola. |
| `delimiters` | `Edm.String` | Una stringa in cui ogni carattere viene considerato come un separatore durante l'analisi di `valueList` parametro. Il valore predefinito di questo parametro è `' ,'` che significa che tutti i valori con spazi e/o virgole tra di essi saranno separati. Se è necessario usare i separatori diversi da spazi e virgole perché i valori includono i caratteri, è possibile specificare delimitatori alternativi, ad esempio `'|'` in questo parametro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestazioni di `search.in`

Se si usa `search.in`, è previsto un tempo di risposta inferiore al secondo quando il secondo parametro contiene un elenco di centinaia o migliaia di valori. Non vi è alcun limite esplicito per il numero di elementi è possibile passare a `search.in`, anche se si è ancora limitati dalla dimensione massima della richiesta. Tuttavia, la latenza aumenterà man mano che aumenta il numero di valori.

## <a name="examples"></a>Esempi

Trovare tutti gli alberghi con nome uguale a 'Motel visualizzazione Sea' o 'hotel Budget'. Frasi contengono spazi, che è un delimitatore di impostazione predefinita. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Trovare tutti gli alberghi con nome uguale a 'Motel visualizzazione Sea' o 'Hotel di Budget' separati da ' |'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Trovare tutti gli alberghi con le chat con il tag "wifi" o 'bagno':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Trovare una corrispondenza in frasi all'interno di una raccolta, ad esempio 'towel riscaldato rack' o 'asciugacapelli incluso' nei tag.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Trovare tutti gli alberghi di motel' tag' o 'ABITACOLO':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
