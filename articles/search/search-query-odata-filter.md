---
title: Riferimento al filtro OData-ricerca di Azure
description: Informazioni di riferimento sul linguaggio OData per la sintassi del filtro nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8817ce075409a3f166b82404767697dc1326cc89
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647582"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Sintassi di $filter OData in ricerca di Azure

Ricerca di Azure usa [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) per applicare criteri aggiuntivi a una query di ricerca oltre ai termini della ricerca full-text. Questo articolo descrive in dettaglio la sintassi dei filtri. Per informazioni più generali sui filtri e su come usarli per realizzare scenari di query specifici, vedere [filtri in ricerca di Azure](search-filters.md).

## <a name="syntax"></a>Sintassi

Un filtro nel linguaggio OData è un'espressione booleana, che a sua volta può essere uno dei diversi tipi di espressione, come illustrato nel seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

I tipi di espressioni booleane includono:

- Espressioni di filtro della `any` raccolta `all`che usano o. Questi applicano i criteri di filtro ai campi della raccolta. Per altre informazioni, vedere [operatori di raccolte OData in ricerca di Azure](search-query-odata-collection-operators.md).
- Espressioni logiche che combinano altre espressioni booleane usando gli `and`operatori `or`, e `not`. Per altre informazioni, vedere [operatori logici OData in ricerca di Azure](search-query-odata-logical-operators.md).
- Espressioni di confronto, che consentono di confrontare campi o variabili di intervallo con valori `eq`costanti usando gli operatori `ge` `gt`, `ne` `lt`, `le`,, e. Per altre informazioni, vedere [operatori di confronto OData in ricerca di Azure](search-query-odata-comparison-operators.md). Le espressioni di confronto vengono usate anche per confrontare le distanze tra le coordinate geospaziali usando la `geo.distance` funzione. Per altre informazioni, vedere [funzioni geospaziali OData in ricerca di Azure](search-query-odata-geo-spatial-functions.md).
- Valori letterali `true` booleani e `false`. Queste costanti possono essere utili a volte quando si generano filtri a livello di codice, ma in caso contrario non tendono a essere usate in pratica.
- Chiamate a funzioni booleane, tra cui:
  - `geo.intersects`, che verifica se un punto specificato si trova all'interno di un poligono specificato. Per altre informazioni, vedere [funzioni geospaziali OData in ricerca di Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, che confronta un campo o una variabile di intervallo con ogni valore in un elenco di valori. Per altre informazioni, vedere [funzione `search.in` OData in ricerca di Azure](search-query-odata-search-in-function.md).
  - `search.ismatch`e `search.ismatchscoring`, che eseguono operazioni di ricerca full-text in un contesto di filtro. Per altre informazioni, vedere [funzioni di ricerca full-text OData in ricerca di Azure](search-query-odata-full-text-search-functions.md).
- Percorsi dei campi o variabili di intervallo `Edm.Boolean`di tipo. Se, ad esempio, l'indice dispone di un campo `IsEnabled` booleano denominato e si desidera restituire tutti i documenti in `true`cui il campo è, l'espressione di filtro `IsEnabled`può essere solo il nome.
- Espressioni booleane tra parentesi. L'utilizzo delle parentesi consente di determinare in modo esplicito l'ordine delle operazioni in un filtro. Per ulteriori informazioni sulla precedenza predefinita degli operatori OData, vedere la sezione successiva.

### <a name="operator-precedence-in-filters"></a>Precedenza tra gli operatori nei filtri

Se si scrive un'espressione di filtro senza parentesi intorno alle relative espressioni secondarie, ricerca di Azure la valuterà in base a un set di regole di precedenza degli operatori. Queste regole sono basate sugli operatori utilizzati per combinare sottoespressioni. La tabella seguente elenca i gruppi di operatori in ordine dalla precedenza più alta alla più bassa:

| Group | Operatore/i |
| --- | --- |
| Operatori logici | `not` |
| Operatori di confronto | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operatori logici | `and` |
| Operatori logici | `or` |

Un operatore più elevato nella tabella precedente verrà "associato in modo più rigoroso" ai propri operandi rispetto ad altri operatori. Ad esempio, `and` è di `or`precedenza superiore a e gli operatori di confronto sono con precedenza maggiore di uno di essi, quindi le due espressioni seguenti sono equivalenti:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

L' `not` operatore ha la precedenza più alta di tutti--anche maggiore degli operatori di confronto. Per questo motivo, se si tenta di scrivere un filtro simile al seguente:

    not Rating gt 5

Verrà ricevuto questo messaggio di errore:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Questo errore si verifica perché l'operatore è associato solo al `Rating` campo, che è di tipo `Edm.Int32`e non all'intera espressione di confronto. La correzione consiste nell'inserire l'operando `not` di tra parentesi:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitazioni relative alle dimensioni dei filtri

Esistono limiti alle dimensioni e alla complessità delle espressioni filtro che è possibile inviare a Ricerca di Azure. I limiti si basano approssimativamente sul numero di clausole nell'espressione filtro. Una linea guida è che se si dispone di centinaia di clausole, si rischia di superare il limite. Si consiglia di progettare l'applicazione in modo che non generi filtri di dimensioni non vincolate.

> [!TIP]
> L'utilizzo della [ funzioneanzichédidisgiunzionilunghediconfrontidiuguaglianzaconsentedievitareillimitedellaclausoladifiltro,perchéunachiamatadifunzionevieneconteggiatacomeunasingolaclausola.`search.in` ](search-query-odata-search-in-function.md)

## <a name="examples"></a>Esempi

Trovare tutti gli alberghi con almeno una stanza con una tariffa di base inferiore a $200 che sono classificate a o superiori a 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Trovare tutti gli alberghi diversi da "Motel di Sea View" rinnovati da 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Trovare tutti gli hotel rinnovati in 2010 o versioni successive. Il valore letterale datetime include le informazioni sul fuso orario per l'ora solare Pacifico:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Trovare tutti gli alberghi con il parcheggio incluso e la posizione in cui tutte le stanze non sono in fumo:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OPPURE -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Trovare tutti gli alberghi di lusso o con parcheggio incluso e di categoria 5 o superiore:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Trovare tutti gli alberghi con il tag "WiFi" in almeno una stanza (dove ogni stanza contiene tag archiviati in un `Collection(Edm.String)` campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Trova tutti gli hotel con qualsiasi chat room:  

    $filter=Rooms/any()

Trovare tutti gli alberghi senza chat room:

    $filter=not Rooms/any()

Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato `Location` (dove è un campo `Edm.GeographyPoint`di tipo):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Trova tutti gli alberghi all'interno di un viewport specificato, descritto come `Location` poligono (dove è un campo di tipo EDM. GeographyPoint). Il poligono deve essere chiuso, vale a dire che il primo e l'ultimo set di punti devono essere uguali. Inoltre, [i punti devono essere elencati in ordine antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Trova tutti gli hotel in cui il campo "Description" è null. Il campo sarà null se non è mai stato impostato o se è stato impostato in modo esplicito su null:  

    $filter=Description eq null

Trovare tutti gli alberghi con nome uguale a' Sea View Motel ' o ' Budget Hotel '). Queste frasi contengono spazi e lo spazio è un delimitatore predefinito. È possibile specificare un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Trovare tutti gli alberghi con nome uguale a' Sea View Motel ' o ' Budget Hotel ' separati da' |'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Trovare tutti gli hotel in cui tutte le stanze hanno il tag ' WiFi ' o ' tub ':

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Trovare una corrispondenza sulle frasi all'interno di una raccolta, ad esempio "rack di asciugamani riscaldati" o "asciugacapelli incluso" nei tag.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Questa richiesta non può essere espressa senza `search.ismatchscoring` la funzione poiché combina la ricerca full-text con le operazioni `or`di filtro usando.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Trovare i documenti senza la parola "luxury".

    $filter=not search.ismatch('luxury')

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `HotelName` e `Description`. Verranno restituiti anche i documenti corrispondenti solo alla seconda clausola della disgiunzione, ovvero Hotel con `Rating` uguale a 5. Tali documenti verranno restituiti con punteggio uguale a zero per chiarire che non corrispondono a nessuna delle parti con punteggio dell'espressione.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Trovare gli hotel in cui i termini "Hotel" e "Airport" non sono più di cinque parole nella descrizione e dove tutte le stanze non sono fumanti. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
