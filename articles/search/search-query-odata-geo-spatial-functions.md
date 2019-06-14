---
title: Riferimento alle funzioni geospaziali di OData - ricerca di Azure
description: Funzioni geospaziali OData, Distance e intersects, nelle query di ricerca di Azure.
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079795"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData funzioni geospaziali in ricerca di Azure - `geo.distance` e `geo.intersects`

Ricerca di Azure supporta le query geospaziali nel [espressioni di filtro OData](query-odata-filter-orderby-syntax.md) tramite il `geo.distance` e `geo.intersects` funzioni. Il `geo.distance` funzione restituisce la distanza in chilometri tra due punti, uno è un campo o variabile di intervallo e uno è una costante passata come parte del filtro. Il `geo.intersects` funzione restituisce `true` se un punto specificato si trova all'interno di un poligono specificato, in cui il punto è una campo o variabile di intervallo e il poligono è specificato come costante passata come parte del filtro.

Il `geo.distance` funzione può essere usata anche nel [ **$orderby** parametro](search-query-odata-orderby.md) per ordinare i risultati di ricerca dalla distanza da un punto specificato. La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` nelle **$orderby**, il campo a cui si riferisce deve essere di tipo `Edm.GeographyPoint` e deve anche essere **ordinabile**.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica del `geo.distance` e `geo.intersects` funzioni, nonché i valori geospaziali in cui operano:

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
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

### <a name="geodistance"></a>geo.distance

Il `geo.distance` funzione accetta due parametri di tipo `Edm.GeographyPoint` e restituisce un `Edm.Double` valore, ovvero la distanza tra loro in chilometri. Questo comportamento è diverso da altri servizi che supportano operazioni OData geospaziali, in genere restituiscono le distanza in metri.

Uno dei parametri `geo.distance` deve essere una costante di punto di geografia e l'altro deve essere un percorso del campo (o l'iterazione di una variabile di intervallo nel caso di un filtro su un campo di tipo `Collection(Edm.GeographyPoint)`). Non è importante l'ordine dei parametri seguenti.

La costante di punto di geografia è nel formato `geography'POINT(<longitude> <latitude>)'`, dove la longitudine e latitudine sono costanti numeriche.

> [!NOTE]
> Quando si usa `geo.distance` in un filtro, è necessario confrontare la distanza restituita dalla funzione con una costante usando `lt`, `le`, `gt`, o `ge`. Gli operatori `eq` e `ne` non sono supportati quando si confrontano le distanze. Ad esempio, si tratta di un utilizzo corretto delle `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.intersects

Il `geo.intersects` funzione accetta una variabile di tipo `Edm.GeographyPoint` e una costante `Edm.GeographyPolygon` e restituisce un' `Edm.Boolean`  --  `true` se il punto è all'interno del poligono `false` in caso contrario.

Il poligono è una superficie bidimensionale archiviata come una sequenza di punti che definiscono un anello di delimitazione (vedere la [esempi](#examples) sotto). Il poligono deve essere chiuso, quindi il primo e l'ultimo set di punti devono coincidere. [I punti in un poligono devono essere in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Query geospaziali e poligoni che si estende i meridiani 180th

Per molte query geo-spaziali librerie formulare una query che include i meridiani 180th (quasi la solare linea cambiamento data) sono inaccessibile o è necessario risolvere il problema, ad esempio la suddivisione del poligono in due, uno su entrambi i lati dei meridiani.

In ricerca di Azure, le query geospaziali che includono la longitudine di 180 gradi funzionerà come previsto se la forma della query è rettangolare e allineare le coordinate a un layout griglia nella parte di longitudine e latitudine (ad esempio, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). In caso contrario, per le forme non rettangolari o non allineate, considerare l'approccio basato sulla suddivisione del poligono.  

### <a name="geo-spatial-functions-and-null"></a>Funzioni geospaziali e `null`

Come tutti gli altri campi non di raccolta in ricerca di Azure, i campi di tipo `Edm.GeographyPoint` può contenere `null` valori. Quando ricerca di Azure viene valutata `geo.intersects` per un campo che rappresenta `null`, il risultato sarà sempre `false`. Il comportamento di `geo.distance` in questo caso dipende dal contesto:

- Nei filtri `geo.distance` di un `null` campo i risultati in `null`. Ciò significa che il documento non corrisponderà perché `null` valore confrontato qualsiasi valore non null restituisce `false`.
- Ordinare i risultati usando **$orderby**, `geo.distance` di un `null` campo determina la distanza massima possibile. I documenti con tale campo verranno ordinato inferiore rispetto a tutti gli altri quando la direzione di ordinamento `asc` viene usata (impostazione predefinita) e versioni successive rispetto a tutti gli altri quando la direzione è `desc`.

## <a name="examples"></a>Esempi

### <a name="filter-examples"></a>Esempi di filtro

Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato (in cui posizione è un campo di tipo `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Trovare tutti gli alberghi entro un determinato riquadro di visualizzazione designato come poligono (dove location è un campo di tipo `Edm.GeographyPoint`). Si noti che il poligono è chiuso (il primo e l'ultimo set di punti devono coincidere) e che [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Esempi di order-by

Ordinamento decrescente in base gli hotel `rating`, quindi crescente di distanza dalle coordinate specificate:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in ordine decrescente tramite `search.score` e `rating`e quindi in ordine crescente in base distanza dalle coordinate specificate in modo che tra i due hotel con classificazioni identici, il più vicino è elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
