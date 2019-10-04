---
title: Guida di riferimento alle funzioni geospaziali OData-ricerca di Azure
description: Funzioni geospaziali OData, Geo. distance e geo. Intersects, nelle query di ricerca di Azure.
ms.date: 09/13/2019
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
ms.openlocfilehash: 03220786c65ab510a632252b20d593cd96a90494
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003453"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Funzioni geospaziali OData in ricerca di Azure `geo.distance` : e`geo.intersects`

Ricerca di Azure supporta le query geospaziali nelle [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite le `geo.distance` funzioni e. `geo.intersects` La `geo.distance` funzione restituisce la distanza in chilometri tra due punti, uno costituito da un campo o una variabile di intervallo e l'altro è una costante passata come parte del filtro. La `geo.intersects` funzione restituisce `true` se un punto specificato si trova all'interno di un poligono specificato, dove il punto è un campo o una variabile di intervallo e il poligono viene specificato come una costante passata come parte del filtro.

La `geo.distance` funzione può essere usata anche nel parametro [ **$OrderBy** ](search-query-odata-orderby.md) per ordinare i risultati della ricerca in base alla distanza da un determinato punto. La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si `geo.distance` utilizza in **$OrderBy**, il campo a cui si applica deve essere di `Edm.GeographyPoint` tipo e deve anche essere **ordinabile**.

> [!NOTE]
> Quando si `geo.distance` USA nel parametro **$OrderBy** , il campo passato alla funzione deve contenere solo un singolo punto geografico. In altre parole, deve essere di tipo `Edm.GeographyPoint` e non. `Collection(Edm.GeographyPoint)` Non è possibile ordinare i campi di raccolta in ricerca di Azure.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica delle `geo.distance` funzioni `geo.intersects` e, nonché i valori geospaziali su cui operano:

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
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

### <a name="geodistance"></a>distanza geografica

La `geo.distance` funzione accetta due parametri di tipo `Edm.GeographyPoint` e restituisce un `Edm.Double` valore che rappresenta la distanza tra di essi in chilometri. Questo comportamento è diverso da quello degli altri servizi che supportano le operazioni Geo-spaziali OData, che in genere restituiscono le distanze in metri.

Uno dei parametri per `geo.distance` deve essere una costante di punti geografici e l'altro deve essere un percorso di campo (o una variabile di intervallo in caso di iterazione di un filtro su un campo di tipo `Collection(Edm.GeographyPoint)`). L'ordine di questi parametri non è rilevante.

La costante del punto geografico è nel `geography'POINT(<longitude> <latitude>)'`formato, in cui la longitudine e la latitudine sono costanti numeriche.

> [!NOTE]
> Quando si `geo.distance` utilizza in un filtro, è necessario confrontare la distanza restituita dalla funzione con una costante `lt`utilizzando `le`, `gt`, o `ge`. Gli operatori `eq` e `ne` non sono supportati quando si confrontano le distanze. Ad esempio, si tratta di un utilizzo corretto `geo.distance`di `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`:.

### <a name="geointersects"></a>intersecazione geografica

La `geo.intersects` funzione accetta una variabile di tipo `Edm.GeographyPoint` e una costante `Edm.GeographyPolygon` e restituisce un `Edm.Boolean`  --  `true` se il punto si trova all'interno dei limiti del poligono `false` ; in caso contrario,.

Il poligono è una superficie bidimensionale archiviata come una sequenza di punti che definiscono un anello di delimitazione (vedere gli [esempi](#examples) riportati di seguito). Il poligono deve essere chiuso, quindi il primo e l'ultimo set di punti devono coincidere. [I punti in un poligono devono essere in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Query geospaziali e poligoni che si estendono sul meridiano 180th

Per molte librerie di query geospaziali, la formulazione di una query che include il meridiano 180th (near the Dateline) è disattivata o richiede una soluzione alternativa, ad esempio la suddivisione del poligono in due, uno su entrambi i lati del meridiano.

In ricerca di Azure, le query geospaziali che includono la longitudine di 180 gradi funzioneranno come previsto se la forma della query è rettangolare e le coordinate sono allineate a un layout di griglia `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`lungo la longitudine e la latitudine (ad esempio,). In caso contrario, per le forme non rettangolari o non allineate, considerare l'approccio basato sulla suddivisione del poligono.  

### <a name="geo-spatial-functions-and-null"></a>Funzioni geospaziali e`null`

Come tutti gli altri campi non di raccolta in ricerca di Azure, i `Edm.GeographyPoint` campi di `null` tipo possono contenere valori. Quando ricerca `geo.intersects` di Azure valuta un campo `null`, `false`il risultato sarà sempre. Il comportamento di `geo.distance` in questo caso dipende dal contesto:

- Nei filtri, `geo.distance` di un `null` campo viene restituito `null`. Ciò significa che il documento non corrisponderà `null` perché rispetto a qualsiasi valore non null viene restituito. `false`
- Quando si ordinano irisultati usando `geo.distance` $OrderBy, `null` un campo determina la distanza massima possibile. I documenti con tale campo vengono ordinati in modo più basso rispetto a tutti gli `asc` altri quando viene usata la direzione di ordinamento (impostazione predefinita) e superiore a tutti `desc`gli altri quando la direzione è.

## <a name="examples"></a>Esempi

### <a name="filter-examples"></a>Esempi di filtro

Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato (dove location è un campo `Edm.GeographyPoint`di tipo):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Trova tutti gli hotel all'interno di un viewport specificato descritto come poligono (dove location è un `Edm.GeographyPoint`campo di tipo). Si noti che il poligono è chiuso (il primo e l'ultimo set di punti devono coincidere) e che [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Esempi di order-by

Ordinare gli hotel in ordine `rating`decrescente per, quindi ascendere per distanza dalle coordinate specificate:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente `search.score` in `rating`base a e, quindi in ordine crescente in base alla distanza tra le coordinate specificate, in modo che tra due alberghi con valutazioni identiche, il più vicino venga elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
