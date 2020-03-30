---
title: Riferimento alla funzione geospaziale OData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso di funzioni geospaziali OData, geo.distance e geo.intersects, nelle query di Ricerca cognitiva di Azure.Syntax and reference documentation for using OData geo-spatial functions, geo.distance and geo.intersects, in Azure Cognitive Search queries.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113168"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funzioni geospaziali OData in Ricerca `geo.distance` cognitiva di Azure e`geo.intersects`

Ricerca cognitiva di Azure supporta le query geospaziali `geo.intersects` nelle espressioni di filtro [OData](query-odata-filter-orderby-syntax.md) tramite le `geo.distance` funzioni e . La `geo.distance` funzione restituisce la distanza in chilometri tra due punti, uno è una variabile di campo o di intervallo e uno è una costante passata come parte del filtro. La `geo.intersects` funzione `true` restituisce se un determinato punto si trova all'interno di un determinato poligono, dove il punto è una variabile di campo o di intervallo e il poligono viene specificato come costante passata come parte del filtro.

La `geo.distance` funzione può essere utilizzata anche nel [parametro **$orderby** ](search-query-odata-orderby.md) per ordinare i risultati della ricerca in base alla distanza da un determinato punto. La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando `geo.distance` viene utilizzato in **$orderby**, il campo `Edm.GeographyPoint` a cui si applica deve essere di tipo e deve essere **ordinabile.**

> [!NOTE]
> Quando `geo.distance` si utilizza il parametro **$orderby,** il campo passato alla funzione deve contenere un solo punto geografico. In altre parole, deve `Edm.GeographyPoint` essere `Collection(Edm.GeographyPoint)`di tipo e non . Non è possibile ordinare i campi di raccolta in Ricerca cognitiva di Azure.It is not possible to sort on collection fields in Azure Cognitive Search.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `geo.distance` ) `geo.intersects` definisce la grammatica delle funzioni e, nonché i valori geospaziali su cui operano:

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

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

### <a name="geodistance"></a>geo.distance

La `geo.distance` funzione accetta due `Edm.GeographyPoint` parametri `Edm.Double` di tipo e restituisce un valore che è la distanza tra di essi in chilometri. Questo comportamento è diverso da altri servizi che supportano le operazioni geospaziali OData, che in genere restituiscono distanze in metri.

Uno dei parametri `geo.distance` deve essere una costante del punto geografico e l'altro deve essere un percorso di campo (o una variabile di intervallo nel caso di un filtro che scorre un campo di tipo `Collection(Edm.GeographyPoint)`). L'ordine di questi parametri non è importante.

La costante del punto geografico ha forma `geography'POINT(<longitude> <latitude>)'`, dove la longitudine e la latitudine sono costanti numeriche.

> [!NOTE]
> Quando `geo.distance` si utilizza in un filtro, è necessario confrontare `lt`la `le` `gt`distanza `ge`restituita dalla funzione con una costante utilizzando , , o . Gli operatori `eq` e `ne` non sono supportati quando si confrontano le distanze. Ad esempio, si tratta `geo.distance`di `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`un utilizzo corretto di : .

### <a name="geointersects"></a>geo.intersects

La `geo.intersects` funzione accetta una `Edm.GeographyPoint` variabile `Edm.GeographyPolygon` di tipo `Edm.Boolean`  --  `true` e una costante e restituisce `false` un se il punto si trova all'interno dei limiti del poligono, in caso contrario.

Il poligono è una superficie bidimensionale memorizzata come una sequenza di punti che definiscono un anello di delimitazione (vedere gli [esempi](#examples) seguenti). Il poligono deve essere chiuso, quindi il primo e l'ultimo set di punti devono coincidere. [I punti in un poligono devono essere in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Query geospaziali e poligoni che coprono il 180o meridiano

Per molte librerie di query geospaziali che formulano una query che include il 180esimo meridiano (vicino alla dataline) è off-limits o richiede una soluzione alternativa, ad esempio la suddivisione del poligono in due, una su entrambi i lati del meridiano.

In Ricerca cognitiva di Azure le query geospaziali che includono longitudine di 180 gradi funzioneranno come previsto se la forma `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`della query è rettangolare e le coordinate si allineano a un layout di griglia lungo longitudine e latitudine (ad esempio, ). In caso contrario, per le forme non rettangolari o non allineate, considerare l'approccio basato sulla suddivisione del poligono.  

### <a name="geo-spatial-functions-and-null"></a>Funzioni geospaziali e`null`

Come tutti gli altri campi non di raccolta `Edm.GeographyPoint` in `null` Ricerca cognitiva di Azure, i campi di tipo possono contenere valori. Quando Ricerca cognitiva `geo.intersects` di Azure `null`valuta un campo `false`che è , il risultato sarà sempre . Il comportamento `geo.distance` di in questo caso dipende dal contesto:

- Nei filtri, `geo.distance` `null` di un `null`campo risulta in . Ciò significa che il `null` documento non corrisponderà perché `false`rispetto a qualsiasi valore non null restituisce .
- Quando si ordinano `geo.distance` i `null` risultati utilizzando **$orderby**di un campo si ottiene la distanza massima possibile. I documenti con un campo di questo tipo `asc` verranno ordinati più in basso rispetto a `desc`tutti gli altri quando viene utilizzata la direzione di ordinamento (impostazione predefinita) e superiore a tutti gli altri quando la direzione è .

## <a name="examples"></a>Esempi

### <a name="filter-examples"></a>Esempi di filtro

Trova tutti gli hotel entro 10 chilometri da un determinato `Edm.GeographyPoint`punto di riferimento (dove posizione è un campo di tipo ):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Trova tutti gli hotel all'interno di una determinata finestra `Edm.GeographyPoint`descritta come un poligono (dove location è un campo di tipo ). Si noti che il poligono è chiuso (il primo e l'ultimo set di punti devono coincidere) e che [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Esempi di order-by

Ordinare gli hotel `rating`in discesa , quindi salire in base alla distanza dalle coordinate specificate:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordinare gli hotel in `search.score` `rating`ordine decrescente per e , quindi in ordine crescente in base alla distanza dalle coordinate specificate in modo che tra due hotel con valutazioni identiche, quello più vicino sia elencato per primo:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
