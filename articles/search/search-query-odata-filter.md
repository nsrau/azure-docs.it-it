---
title: Riferimento di filtro OData - ricerca di Azure
description: Riferimenti al linguaggio di OData per la sintassi del filtro nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079912"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Sintassi di OData $filter in ricerca di Azure

Ricerca di Azure Usa [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) per applicare criteri aggiuntivi per una query di ricerca oltre alle condizioni di ricerca full-text. Questo articolo descrive la sintassi dei filtri in modo dettagliato. Per informazioni più generali sulle quali sono i filtri e su come usarli per realizzare scenari di query specifici, vedere [filtri in ricerca di Azure](search-filters.md).

## <a name="syntax"></a>Sintassi

Un filtro nel linguaggio di OData è un'espressione booleana, che a sua volta può essere uno dei vari tipi di espressione, come illustrato per il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

I tipi di espressioni booleane sono:

- Espressioni di filtro insieme mediante `any` o `all`. Questi si applicano i criteri di filtro per i campi della raccolta. Per altre informazioni, vedere [gli operatori di raccolta OData in ricerca di Azure](search-query-odata-collection-operators.md).
- Espressioni logiche che combinano altre espressioni Boolean utilizzando gli operatori `and`, `or`, e `not`. Per altre informazioni, vedere [OData operatori logici nella ricerca di Azure](search-query-odata-logical-operators.md).
- Espressioni di confronto, quale confrontare i campi o le variabili con valori costanti tramite gli operatori di intervallo `eq`, `ne`, `gt`, `lt`, `ge`, e `le`. Per altre informazioni, vedere [gli operatori di confronto OData in ricerca di Azure](search-query-odata-comparison-operators.md). Espressioni di confronto vengono anche usate per confrontare le distanze tra coordinate geo-spaziali usando il `geo.distance` (funzione). Per altre informazioni, vedere [OData funzioni geospaziali in ricerca di Azure](search-query-odata-geo-spatial-functions.md).
- I valori letterali booleani `true` e `false`. Queste costanti possono essere utili in alcuni casi durante la generazione a livello di programmazione i filtri, ma in caso contrario non tendono a essere utilizzato in pratica.
- Chiamate a funzioni booleane, tra cui:
  - `geo.intersects`, che verifica se un punto specificato si trova all'interno di un poligono specificato. Per altre informazioni, vedere [OData funzioni geospaziali in ricerca di Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, che confronta una campo o variabile di intervallo in cui ciascun valore in un elenco di valori. Per altre informazioni, vedere [OData `search.in` funzione di ricerca di Azure](search-query-odata-search-in-function.md).
  - `search.ismatch` e `search.ismatchscoring`, quali eseguire le operazioni di ricerca full-text in un contesto di filtro. Per altre informazioni, vedere [OData funzioni di ricerca full-text in ricerca di Azure](search-query-odata-full-text-search-functions.md).
- Campo percorsi o le variabili di tipo di intervallo `Edm.Boolean`. Ad esempio, se l'indice includa un campo booleano chiamato `IsEnabled` e si vuole restituire tutti i documenti in cui questo campo viene `true`, l'espressione di filtro può essere semplicemente il nome `IsEnabled`.
- Espressioni booleane racchiuso tra parentesi. Usando le parentesi può aiutare a determinare in modo esplicito l'ordine delle operazioni in un filtro. Per altre informazioni sulla precedenza predefinita degli operatori di OData, vedere la sezione successiva.

### <a name="operator-precedence-in-filters"></a>Precedenza degli operatori nei filtri

Se si scrive un'espressione di filtro con nessuna parentesi che racchiudono le sottoespressioni, ricerca di Azure valuterà, in base a un set di regole di precedenza di operatore. Queste regole si basano sui quali gli operatori vengono utilizzati per combinare le sottoespressioni. La tabella seguente elenca i gruppi di operatori in ordine dalla più alta alla priorità più bassa:

| Group | Più operatori |
| --- | --- |
| Operatori logici | `not` |
| Operatori di confronto | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatori logici | `and` |
| Operatori logici | `or` |

Un operatore di livello superiore nella tabella precedente "assocerà più strettamente" per gli operandi degli altri operatori. Ad esempio, `and` è di precedenza più alta rispetto `or`, e gli operatori di confronto hanno maggiore precedenza rispetto a una di esse, in modo che le due espressioni seguenti sono equivalenti:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

Il `not` operatore ha la precedenza più alta di tutti, addirittura superiore gli operatori di confronto. Ecco perché se si prova a scrivere un filtro simile alla seguente:

    not Rating gt 5

Si otterrà questo messaggio di errore:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Questo errore si verifica perché l'operatore è associato solo il `Rating` campo, che è di tipo `Edm.Int32`e non con l'espressione di confronto intero. La correzione consiste nell'inserire l'operando di `not` racchiuso tra parentesi:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitazioni relative alle dimensioni dei filtri

Esistono limiti alle dimensioni e alla complessità delle espressioni filtro che è possibile inviare a Ricerca di Azure. I limiti si basano approssimativamente sul numero di clausole nell'espressione filtro. Una buona indicazione è che se si dispone di centinaia di clausole, si è a rischio di superamento del limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni.

> [!TIP]
> Usando [il `search.in` funzione](search-query-odata-search-in-function.md) anziché disgiunzioni lungo di uguaglianza confronti possono contribuire a evitare il limite di clausola di filtro, perché una chiamata di funzione viene conteggiata come una singola clausola.

## <a name="examples"></a>Esempi

Trovare tutti gli alberghi con almeno una stanza con una tariffa di base inferiore a 200 dollari che sono classificati 4 o superiore:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Trovare tutti gli alberghi diversi da "Sea visualizzazione Motel" rinnovati dal 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Trovare tutti gli alberghi che sono stati rinnovati nel 2010 o versione successiva. Il valore letterale datetime include informazioni sul fuso orario dell'ora solare Pacifico:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Trovare tutti gli alberghi con parcheggio incluso e in cui tutte le stanze sono non fumatori:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OPPURE -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Trovare tutti gli alberghi di lusso o con parcheggio incluso e di categoria 5 o superiore:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Trovare tutti gli alberghi con il tag "wifi" in almeno uno spazio (in cui ogni chat dispone di tag archiviati in un `Collection(Edm.String)` campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Trovare tutti gli alberghi con qualsiasi chat:  

    $filter=Rooms/any()

Trovare tutti gli alberghi che non hanno le chat:

    $filter=not Rooms/any()

Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato (dove `Location` è un campo di tipo `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Trovare tutti gli alberghi entro un determinato riquadro di visualizzazione designato come poligono (dove `Location` è un campo di tipo EDM. geographypoint). Il poligono deve essere chiuso, vale a dire il primo e ultimo set di punti deve essere lo stesso. È inoltre [quanto deve essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Trovare tutti gli alberghi in cui il campo "Descrizione" è null. Il campo sarà null se non si è mai stato impostato o se è stata impostata in modo esplicito su null:  

    $filter=Description eq null

Trovare tutti gli alberghi con nome uguale a 'Motel visualizzazione Sea' o 'hotel Budget'). Le seguenti diciture contengono spazi e lo spazio è un delimitatore di impostazione predefinita. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Trovare tutti gli alberghi con nome uguale a 'Motel visualizzazione Sea' o 'Hotel di Budget' separati da ' |'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Trovare tutti gli alberghi in cui tutte le stanze hanno il tag "wifi" o 'bagno':

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Trovare una corrispondenza in frasi all'interno di una raccolta, ad esempio 'towel riscaldato rack' o 'asciugacapelli incluso' nei tag.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Questa richiesta non può essere espresso senza il `search.ismatchscoring` funzionare poiché combina la ricerca full-text con operazioni di filtro utilizzando `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Trovare i documenti senza la parola "luxury".

    $filter=not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Description`. I documenti che corrispondono solo la seconda clausola della disgiunzione restituirà troppo, gli hotel con `Rating` uguale a 5. Tali documenti vengono restituiti con punteggio pari a zero per renderla cancellare che non rispettavano alcune parti dell'espressione con punteggio.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Trovare gli hotel in cui i termini "hotel" e "aeroporto" sono parole non più di cinque distanti nella descrizione, e in cui tutte le stanze sono non fumatori. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
