---
title: Riferimenti alle funzioni geospaziali OData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso di funzioni geospaziali OData, Geo. distance e geo. Intersects, in Azure ricerca cognitiva query.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113168"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funzioni geospaziali OData in Azure ricerca cognitiva-`geo.distance` e `geo.intersects`

Azure ricerca cognitiva supporta le query geospaziali nelle [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite le funzioni `geo.distance` e `geo.intersects`. La funzione `geo.distance` restituisce la distanza in chilometri tra due punti, uno costituito da un campo o una variabile di intervallo e l'altro è una costante passata come parte del filtro. La funzione `geo.intersects` restituisce `true` se un punto specificato si trova all'interno di un poligono specificato, dove il punto è un campo o una variabile di intervallo e il poligono viene specificato come una costante passata come parte del filtro.

La funzione `geo.distance` può essere usata anche nel parametro [ **$OrderBy** ](search-query-odata-orderby.md) per ordinare i risultati della ricerca in base alla distanza da un determinato punto. La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` in **$OrderBy**, il campo a cui si applica deve essere di tipo `Edm.GeographyPoint` e deve anche essere **ordinabile**.

> [!NOTE]
> Quando si usa `geo.distance` nel parametro **$OrderBy** , il campo passato alla funzione deve contenere solo un singolo punto geografico. In altre parole, deve essere di tipo `Edm.GeographyPoint` e non `Collection(Edm.GeographyPoint)`. Non è possibile ordinare i campi di raccolta in ricerca cognitiva di Azure.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica delle funzioni `geo.distance` e `geo.intersects`, nonché i valori geospaziali su cui operano:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

### <a name="geodistance"></a>distanza geografica

La funzione `geo.distance` accetta due parametri di tipo `Edm.GeographyPoint` e restituisce un valore `Edm.Double` che rappresenta la distanza tra di essi in chilometri. Questo comportamento è diverso da quello degli altri servizi che supportano le operazioni Geo-spaziali OData, che in genere restituiscono le distanze in metri.

Uno dei parametri di `geo.distance` deve essere una costante del punto geografico, mentre l'altro deve essere un percorso di campo (o una variabile di intervallo in caso di iterazione di un filtro su un campo di tipo `Collection(Edm.GeographyPoint)`). L'ordine di questi parametri non è rilevante.

La costante del punto geografico è nel formato `geography'POINT(<longitude> <latitude>)'`, dove la longitudine e la latitudine sono costanti numeriche.

> [!NOTE]
> Quando si usa `geo.distance` in un filtro, è necessario confrontare la distanza restituita dalla funzione con una costante usando `lt`, `le`, `gt`o `ge`. Gli operatori `eq` e `ne` non sono supportati quando si confrontano le distanze. Ad esempio, si tratta di un utilizzo corretto di `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>intersecazione geografica

La funzione `geo.intersects` accetta una variabile di tipo `Edm.GeographyPoint` e una costante `Edm.GeographyPolygon` e restituisce un `Edm.Boolean` -- `true` se il punto si trova all'interno dei limiti del poligono, `false` in caso contrario.

Il poligono è una superficie bidimensionale archiviata come una sequenza di punti che definiscono un anello di delimitazione (vedere gli [esempi](#examples) riportati di seguito). Il poligono deve essere chiuso, quindi il primo e l'ultimo set di punti devono coincidere. [I punti in un poligono devono essere in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Query geospaziali e poligoni che si estendono sul meridiano 180th

Per molte librerie di query geospaziali, la formulazione di una query che include il meridiano 180th (near the Dateline) è disattivata o richiede una soluzione alternativa, ad esempio la suddivisione del poligono in due, uno su entrambi i lati del meridiano.

In ricerca cognitiva di Azure, le query geospaziali che includono la longitudine di 180 gradi funzioneranno come previsto se la forma della query è rettangolare e le coordinate sono allineate a un layout di griglia lungo la longitudine e la latitudine, ad esempio `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`. In caso contrario, per le forme non rettangolari o non allineate, considerare l'approccio basato sulla suddivisione del poligono.  

### <a name="geo-spatial-functions-and-null"></a>Funzioni geospaziali e `null`

Come tutti gli altri campi non di raccolta in ricerca cognitiva di Azure, i campi di tipo `Edm.GeographyPoint` possono contenere valori di `null`. Quando Azure ricerca cognitiva valuta `geo.intersects` per un campo `null`, il risultato sarà sempre `false`. Il comportamento di `geo.distance` in questo caso dipende dal contesto:

- Nei filtri `geo.distance` di un campo di `null` genera `null`. Ciò significa che il documento non corrisponderà perché `null` rispetto a un valore non null restituisce `false`.
- Quando si ordinano i risultati usando **$OrderBy**, `geo.distance` di un campo `null` determina la distanza massima possibile. I documenti con tale campo vengono ordinati in modo più basso di tutti gli altri quando viene usata la direzione di ordinamento `asc` (impostazione predefinita) e superiore a tutti gli altri quando la direzione è `desc`.

## <a name="examples"></a>esempi

### <a name="filter-examples"></a>Esempi di filtro

Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato (dove location è un campo di tipo `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Trova tutti gli hotel all'interno di un viewport specificato descritto come poligono (dove location è un campo di tipo `Edm.GeographyPoint`). Si noti che il poligono è chiuso (il primo e l'ultimo set di punti devono coincidere) e che [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Esempi di order-by

Ordinare gli hotel in ordine decrescente in base `rating`, quindi in ordine crescente per distanza dalle coordinate specificate:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente in base a `search.score` e `rating`, quindi in ordine crescente in base alla distanza tra le coordinate specificate, in modo che tra due alberghi con valutazioni identiche, il più vicino venga elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
