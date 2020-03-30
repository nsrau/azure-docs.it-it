---
title: Informazioni di riferimento sulle funzioni di search.in OData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso della funzione di search.in nelle query di Ricerca cognitiva di Azure.Syntax and reference documentation for using the search.in function in Azure Cognitive Search queries.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113113"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Funzione `search.in` OData in Ricerca cognitiva di AzureOData function in Azure Cognitive Search

Uno scenario comune nelle [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) consiste nel verificare se un singolo campo in ogni documento è uguale a uno dei molti valori possibili. Ad esempio, questo è il modo in cui alcune applicazioni implementano la limitazione per motivi di [sicurezza,](search-security-trimming-for-azure-search.md) controllando un campo contenente uno o più ID principale rispetto a un elenco di ID principale che rappresentano l'utente che esegue la query. Un modo per scrivere una query [`eq`](search-query-odata-comparison-operators.md) come [`or`](search-query-odata-logical-operators.md) questa consiste nell'utilizzare gli operatori and:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Tuttavia, c'è un modo più `search.in` breve per scrivere questo, utilizzando la funzione:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Oltre ad essere più breve e `search.in` più facile da leggere, l'utilizzo fornisce anche [vantaggi in termini](#bkmk_performance) di prestazioni ed evita alcune [limitazioni di dimensione dei filtri](search-query-odata-filter.md#bkmk_limits) quando ci sono centinaia o addirittura migliaia di valori da includere nel filtro. Per questo motivo, `search.in` si consiglia vivamente di utilizzare invece di una disgiunzione più complessa delle espressioni di uguaglianza.

> [!NOTE]
> La versione 4.01 dello standard OData ha recentemente introdotto l'operatore [ `in` ](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), che ha un comportamento simile a quello della `search.in` funzione in Ricerca cognitiva di Azure. Tuttavia, Ricerca cognitiva di Azure non supporta `search.in` questo operatore, pertanto è necessario usare la funzione.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.in` ) definisce la grammatica della funzione:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

La `search.in` funzione verifica se un determinato campo stringa o variabile di intervallo è uguale a uno di un determinato elenco di valori. L'uguaglianza tra la variabile e ogni valore nell'elenco viene determinata `eq` in modo con distinzione tra maiuscole e minuscole, come per l'operatore. Un'espressione come `search.in(myfield, 'a, b, c')` equivale quindi a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, tranne per il fatto che `search.in` garantisce prestazioni di gran lunga migliori.

Esistono due overload della `search.in` funzione:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

I parametri sono definiti nella tabella seguente:

| Nome parametro | Type | Descrizione |
| --- | --- | --- |
| `variable` | `Edm.String` | Un riferimento a un campo stringa (o una variabile `search.in` di intervallo su un campo di raccolta di stringhe nel caso in cui viene utilizzato all'interno di un'espressione `any` o). `all` |
| `valueList` | `Edm.String` | Stringa contenente un elenco delimitato di `variable` valori da confrontare con il parametro. Se `delimiters` il parametro non viene specificato, i delimitatori predefiniti sono lo spazio e la virgola. |
| `delimiters` | `Edm.String` | Stringa in cui ogni carattere viene considerato `valueList` come separatore durante l'analisi del parametro. Il valore predefinito di `' ,'` questo parametro è che tutti i valori con spazi e/o virgole tra di essi verranno separati. Se è necessario utilizzare separatori diversi da spazi e virgole perché i valori includono `'|'` tali caratteri, è possibile specificare delimitatori alternativi, ad esempio in questo parametro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestazioni di`search.in`

Se si usa `search.in`, è previsto un tempo di risposta inferiore al secondo quando il secondo parametro contiene un elenco di centinaia o migliaia di valori. Non esiste un limite esplicito al numero `search.in`di elementi che è possibile passare a , anche se si è ancora limitati dalla dimensione massima della richiesta. Tuttavia, la latenza aumenterà man mano che aumenta il numero di valori.

## <a name="examples"></a>Esempi

Trova tutti gli hotel con un nome uguale a "Motel di Vista del mare" o "Hotel economico". Le frasi contengono spazi, ovvero un delimitatore predefinito. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:You can specify an alternative delimiter in single quotes as the third string parameter:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Trova tutti gli hotel con nome uguale a 'Sea View motel' o 'Hotel di bilancio' separati da '''):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Trova tutti gli hotel con camere che hanno il tag 'wifi' o 'tub':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Trova una corrispondenza sulle frasi all'interno di una raccolta, ad esempio "rastrelliere per asciugamani riscaldati" o "hairdryer incluso" nelle etichette.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Trova tutti gli hotel senza il tag 'motel' o 'cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
