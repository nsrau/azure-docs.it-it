---
title: Informazioni di riferimento sul filtro ODataOData filter reference
titleSuffix: Azure Cognitive Search
description: Riferimento al linguaggio OData e sintassi completa usata per la creazione di espressioni di filtro nelle query di Ricerca cognitiva di Azure.OData language reference and full syntax used for creating filter expressions in Azure Cognitive Search queries.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282887"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Sintassi di $filter Di Data in Ricerca cognitiva di AzureOData $filter syntax in Azure Cognitive Search

Ricerca cognitiva di Azure usa [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) per applicare criteri aggiuntivi a una query di ricerca oltre ai termini di ricerca full-text. In questo articolo viene descritta in dettaglio la sintassi dei filtri. Per altre informazioni generali sui filtri e su come usarli per realizzare scenari di query specifici, vedere [Filtri in Ricerca cognitiva](search-filters.md)di Azure.For more general information about what filters are and how to use them to realize specific query scenarios, see Filters in Azure Cognitive Search .

## <a name="syntax"></a>Sintassi

Un filtro nel linguaggio OData è un'espressione booleana, che a sua volta può essere uno dei diversi tipi di espressione, come illustrato dal seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

I tipi di espressioni booleane includono:

- Espressioni di `any` filtro `all`di raccolta utilizzando o . Questi si applicano i criteri di filtro ai campi di raccolta. Per altre informazioni, vedere Operatori di [raccolte OData in Ricerca cognitiva di Azure.For](search-query-odata-collection-operators.md)more information, see OData collection operators in Azure Cognitive Search .
- Espressioni logiche che combinano `and`altre `or`espressioni `not`booleane utilizzando gli operatori , e . Per altre informazioni, vedere [Operatori logici OData in Ricerca cognitiva di Azure](search-query-odata-logical-operators.md).For more information, see OData logical operators in Azure Cognitive Search .
- Espressioni di confronto, che confrontano campi o `eq` `ne`variabili `gt` `lt`di `ge`intervallo con valori costanti utilizzando gli operatori , , , , e `le`. Per altre informazioni, vedere Operatori di [confronto OData in Ricerca cognitiva di Azure](search-query-odata-comparison-operators.md).For more information, see OData comparison operators in Azure Cognitive Search . Le espressioni di confronto vengono utilizzate anche per `geo.distance` confrontare le distanze tra le coordinate geospaziali utilizzando la funzione. Per altre informazioni, vedere [Funzioni geospaziali OData in Ricerca cognitiva di Azure](search-query-odata-geo-spatial-functions.md).For more information, see OData geo-spatial functions in Azure Cognitive Search .
- I valori `true` letterali booleani e `false`. Queste costanti possono talvolta essere utili quando si generano filtri a livello di codice, ma in caso contrario non tendono a essere utilizzate nella pratica.
- Chiamate a funzioni booleane, tra cui:
  - `geo.intersects`, che verifica se un determinato punto si trova all'interno di un determinato poligono. Per altre informazioni, vedere [Funzioni geospaziali OData in Ricerca cognitiva di Azure](search-query-odata-geo-spatial-functions.md).For more information, see OData geo-spatial functions in Azure Cognitive Search .
  - `search.in`, che confronta un campo o una variabile di intervallo con ogni valore in un elenco di valori. Per altre informazioni, vedere [Funzione OData in Ricerca cognitiva `search.in` ](search-query-odata-search-in-function.md)di Azure .For more information, see OData function in Azure Cognitive Search .
  - `search.ismatch`e `search.ismatchscoring`, che eseguono operazioni di ricerca full-text in un contesto di filtro. Per altre informazioni, vedere Funzioni di [ricerca full-text OData in Ricerca cognitiva](search-query-odata-full-text-search-functions.md)di Azure .For more information, see OData full-text search functions in Azure Cognitive Search .
- Percorsi di campo `Edm.Boolean`o variabili di intervallo di tipo . Ad esempio, se l'indice `IsEnabled` ha un campo booleano denominato `true`e si desidera restituire tutti `IsEnabled`i documenti in cui questo campo è , l'espressione di filtro può essere solo il nome .
- Espressioni booleane tra parentesi. L'utilizzo delle parentesi consente di determinare in modo esplicito l'ordine delle operazioni in un filtro. Per ulteriori informazioni sulla precedenza predefinita degli operatori OData, vedere la sezione successiva.

### <a name="operator-precedence-in-filters"></a>Precedenza degli operatori nei filtri

Se si scrive un'espressione di filtro senza parentesi intorno alle relative sottoespressioni, Ricerca cognitiva di Azure la valuterà in base a un set di regole di precedenza degli operatori. Queste regole si basano sugli operatori utilizzati per combinare sottoespressioni. Nella tabella seguente sono elencati i gruppi di operatori in ordine dalla precedenza più alta a quella più bassa:

| Gruppo | Operatore(i) |
| --- | --- |
| Operatori logici | `not` |
| Operatori di confronto | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatori logici | `and` |
| Operatori logici | `or` |

Un operatore più alto nella tabella precedente "si lega più strettamente" agli operandi rispetto ad altri operatori. Ad esempio, `and` ha una `or`precedenza maggiore rispetto a e gli operatori di confronto hanno una precedenza maggiore rispetto a uno di essi, pertanto le due espressioni seguenti sono equivalenti:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

L'operatore `not` ha la precedenza più alta di tutti- anche superiore agli operatori di confronto. Ecco perché se si tenta di scrivere un filtro come questo:

    not Rating gt 5

Verrà visualizzato questo messaggio di errore:You'll get this error message:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Questo errore si verifica perché l'operatore è associato solo al `Rating` campo, che è di tipo `Edm.Int32`, e non all'intera espressione di confronto. La correzione consiste nell'inserire `not` l'operando di tra parentesi:The fix is to put the operand of in parentheses:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitazioni relative alle dimensioni dei filtri

Esistono limiti alle dimensioni e alla complessità delle espressioni di filtro che è possibile inviare a Ricerca cognitiva di Azure.There are limits to the size and complexity of filter expressions that you can send to Azure Cognitive Search. I limiti si basano approssimativamente sul numero di clausole nell'espressione filtro. Una buona linea guida è che se si dispone di centinaia di clausole, si è a rischio di superare il limite. È consigliabile progettare l'applicazione in modo che non generi filtri di dimensioni illimitata.

> [!TIP]
> [L'utilizzo della `search.in` funzione](search-query-odata-search-in-function.md) anziché di disgiunzioni lunghe di confronti di uguaglianza consente di evitare il limite della clausola di filtro, poiché una chiamata di funzione viene conteggiata come una singola clausola.

## <a name="examples"></a>Esempi

Trova tutti gli hotel con almeno una camera con una tariffa base inferiore a 200 dollari che sono classificati a o superiore a 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Trova tutti gli hotel diversi da "Sea View Motel" che sono stati rinnovati dal 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Trova tutti gli hotel che sono stati rinnovati nel 2010 o versioni successive. Il valore letterale datetime include informazioni sul fuso orario per l'ora solare Pacifico:The datetime literal includes time zone information for Pacific Standard Time:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Trova tutti gli hotel che hanno un parcheggio incluso e dove tutte le camere sono non fumatori:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OPPURE -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Trovare tutti gli alberghi di lusso o con parcheggio incluso e di categoria 5 o superiore:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Trova tutti gli hotel con il tag "wifi" in almeno una `Collection(Edm.String)` stanza (dove ogni camera ha tag memorizzati in un campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Trova tutti gli hotel con camere:  

    $filter=Rooms/any()

Trova tutti gli hotel che non hanno camere:

    $filter=not Rooms/any()

Trova tutti gli hotel entro 10 chilometri `Location` da un determinato `Edm.GeographyPoint`punto di riferimento (dove è un campo di tipo ):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Trova tutti gli hotel all'interno di `Location` una determinata finestra descritta come un poligono (dove è un campo di tipo Edm.GeographyPoint). Il poligono deve essere chiuso, il che significa che il primo e l'ultimo set di punti devono essere uguali. Inoltre, [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Trova tutti gli hotel in cui il campo "Descrizione" è nullo. Il campo sarà null se non è mai stato impostato o se è stato impostato in modo esplicito su null:  

    $filter=Description eq null

Trova tutti gli hotel con un nome uguale a "Motel di Vista del mare" o "Hotel economico"). Queste frasi contengono spazi e lo spazio è un delimitatore predefinito. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:You can specify an alternative delimiter in single quotes as the third string parameter:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Trova tutti gli hotel con nome uguale a 'Sea View motel' o 'Hotel di bilancio' separati da '''):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Trova tutti gli hotel in cui tutte le camere hanno il tag 'wifi' o 'tub':

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Trova una corrispondenza sulle frasi all'interno di una raccolta, ad esempio "rastrelliere per asciugamani riscaldati" o "hairdryer incluso" nelle etichette.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Questa richiesta non può essere `search.ismatchscoring` espressa senza la funzione poiché combina `or`la ricerca full-text con le operazioni di filtro utilizzando .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Trovare i documenti senza la parola "luxury".

    $filter=not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Description`. I documenti che corrispondevano solo alla seconda clausola della disgiunzione saranno restituiti troppo - alberghi con `Rating` uguale a 5. Tali documenti verranno restituiti con punteggio uguale a zero per chiarire che non corrispondono a nessuna delle parti segnate dell'espressione.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Trova hotel dove i termini "hotel" e "aeroporto" non sono più di cinque parole a parte nella descrizione, e dove tutte le camere non sono fumatori. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
