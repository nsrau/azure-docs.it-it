---
title: Sintassi delle espressioni OData per filtri e clausole order-by - Ricerca di Azure
description: Sintassi delle espressioni OData dei filtri e delle clausole order-by per le query di Ricerca di Azure.
ms.date: 03/27/2019
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
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317101"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Sintassi delle espressioni OData filter e order-by in Ricerca di Azure

Ricerca di Azure supporta un subset della sintassi delle espressioni OData per le espressioni **$filter** e **$orderby**. Le espressioni filtro vengono valutate durante l'analisi della query, vincolando la ricerca a campi specifici o aggiungendo criteri di corrispondenza durante le analisi dell'indice. Le espressioni order-by vengono applicate come passaggio di post-elaborazione su un set di risultati. Sia i filtri che le espressioni order-by sono incluse in una richiesta di query, rispettando una sintassi OData indipendente dalla sintassi della query [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md) usata in un parametro **search**. Questo articolo fornisce la documentazione di riferimento per le espressioni OData usate nei filtri e nelle espressioni di ordinamento.

## <a name="filter-syntax"></a>Sintassi dei filtri

Un'espressione **$filter** può essere eseguita in modo autonomo come query espressa completamente o affinare una query con parametri aggiuntivi. Gli esempi seguenti illustrano alcuni scenari chiave. Nel primo esempio il filtro è l'elemento fondamentale della query.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Un altro caso d'uso comune è la combinazione dei filtri con i facet, in cui il filtro riduce la superficie di attacco della query secondo una selezione dell'esplorazione in base a facet gestita dall'utente:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Operatori di filtro  

- Operatori logici (and, or, not).  

- Espressioni di confronto (`eq, ne, gt, lt, ge, le`). Per i confronti tra stringhe viene fatta distinzione tra maiuscole e minuscole.  

- Costanti dei tipi [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) supportati. Per un elenco dei tipi supportati, vedere [Supported data types &#40;Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) (Tipi di dati supportati - Ricerca di Azure). Le costanti dei tipi di raccolte non sono supportate.  

- Riferimenti ai nomi di campo. Nelle espressioni filtro si possono usare solo campi `filterable`.  

- `any` senza parametri per testare se un campo di tipo `Collection(Edm.String)` contiene elementi.  

- `any` e `all` con supporto di espressioni lambda limitato. 
    
  -   `any/all` sono supportati nei campi di tipo `Collection(Edm.String)`. 
    
  -   `any` può essere usato con espressioni di uguaglianza semplici o con una funzione `search.in`. Le espressioni semplici sono costituite da un confronto tra un singolo campo e un valore letterale, ad esempio `Title eq 'Magna Carta'`.
    
  -   `all` può essere usato con espressioni di disuguaglianza semplici o con `not search.in`.   

- Funzioni geospaziali `geo.distance` e `geo.intersects`. La funzione `geo.distance` restituisce la distanza in chilometri tra due punti, di cui uno è un campo e l'altro è una costante passata come parte del filtro. La funzione `geo.intersects` restituisce true se un punto specificato si trova all'interno di un poligono specificato, dove il punto è un campo e il poligono è specificato come costante passata come parte del filtro.  

  Il poligono è una superficie bidimensionale archiviata come sequenza di punti che definiscono un anello delimitatore (vedere l'esempio seguente). Il poligono deve essere chiuso, quindi il primo e l'ultimo set di punti devono coincidere. [I punti in un poligono devono essere in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` restituisce la distanza in chilometri in Ricerca di Azure, diversamente che negli altri servizi che supportano le operazioni geospaziali OData, che in genere restituiscono le distanze in metri.  

  > [!NOTE]  
  >  Quando si usa geo.distance in un filtro, è necessario confrontare la distanza restituita dalla funzione con una costante usando `lt`, `le`, `gt` o `ge`. Gli operatori `eq` e `ne` non sono supportati quando si confrontano le distanze. Questo, ad esempio, è un utilizzo corretto di geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- La funzione `search.in` verifica se il campo di una determinata stringa è uguale a quello di un determinato elenco di valori. Può essere usata anche in any o in all per confrontare un singolo valore di un campo di una raccolta di stringhe con un determinato elenco di valori. Per determinare l'uguaglianza tra il campo e ogni valore dell'elenco viene applicata la distinzione tra minuscole e maiuscole, come per l'operatore `eq`. Un'espressione come `search.in(myfield, 'a, b, c')` equivale quindi a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, tranne per il fatto che `search.in` garantisce prestazioni di gran lunga migliori. 

   Il primo parametro della funzione `search.in` è il riferimento di campo di tipo stringa (o una variabile di intervallo in un campo di una raccolta di stringhe se `search.in` viene usata in un'espressione `any` o `all`). 
  
   Il secondo parametro è una stringa che contiene l'elenco di valori, separati da spazi e/o virgole. 
  
   Il terzo parametro è una stringa in cui ogni carattere della stringa o subset di questa stringa viene considerato come un separatore per analizzare l'elenco di valori nel secondo parametro. Se è necessario usare separatori diversi da spazi e virgole perché i valori includono tali caratteri, è possibile specificare un terzo parametro facoltativo per `search.in`. 

  > [!NOTE]   
  > Alcuni scenari richiedono di confrontare un campo con un numero elevato di valori costanti. Ad esempio, l'implementazione della limitazione per motivi di sicurezza con i filtri potrebbe richiedere di confrontare il campo dell'ID del documento con un elenco di ID per cui il richiedente ottiene l'accesso in lettura. In scenari come questo è consigliabile usare la funzione `search.in` invece di una disgiunzione più complessa di espressioni di uguaglianza. Usare, ad esempio, `search.in(Id, '123, 456, ...')` invece di `Id eq 123 or Id eq 456 or ....`. 
  >
  > Se si usa `search.in`, è previsto un tempo di risposta inferiore al secondo quando il secondo parametro contiene un elenco di centinaia o migliaia di valori. Si noti che non esistono limiti espliciti al numero di elementi che è possibile passare a `search.in`, anche se esiste un limite per le dimensioni massime della richiesta. Tuttavia, la latenza aumenterà man mano che aumenta il numero di valori.

- La funzione `search.ismatch` valuta la query di ricerca come parte di un'espressione filtro. I documenti che corrispondono alla query di ricerca verranno restituiti nel set di risultati. Sono disponibili gli overload seguenti di questa funzione:
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  dove: 
  
  - `search`: query di ricerca (nella sintassi di query [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md)). 
  - `queryType`: "simple" o "full". Il valore predefinito è "simple". Specifica il linguaggio di query usato nel parametro `search`.
  - `searchFields`: elenco di campi ricercabili in cui eseguire la ricerca. Il valore predefinito corrisponde a tutti i campi ricercabili nell'indice.    
  - `searchMode`: "any" o "all". Il valore predefinito è "any". Indica se è necessario trovare una corrispondenza con tutti i termini di ricerca o con uno qualsiasi per includere il documento nelle corrispondenze.

  Tutti i parametri precedenti sono equivalenti ai corrispondenti [parametri della richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents).

- La funzione `search.ismatchscoring`, come la funzione `search.ismatch`, restituisce true per i documenti corrispondenti alla query di ricerca passata come parametro. La differenza tra di esse è che il punteggio di pertinenza dei documenti corrispondenti alla query `search.ismatchscoring` contribuirà al punteggio complessivo dei documenti, mentre nel caso di `search.ismatch`, il punteggio dei documenti non verrà modificato. Sono disponibili gli overload seguenti di questa funzione con parametri identici a quelli di `search.ismatch`:
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  Le funzioni `search.ismatch` e `search.ismatchscoring` sono completamente ortogonali tra loro e con il resto dell'algebra dei filtri. Ciò significa che entrambe le funzioni possono essere usate nella stessa espressione filtro. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Query geospaziali e poligoni che si estendono al 180° meridiano  
 Molte librerie di query geospaziali che formulano una query che include il 180° meridiano (vicino alla linea del cambiamento di data) sono inaccessibili o richiedono una soluzione alternativa, ad esempio la suddivisione del poligono in due parti, una su entrambi i lati del meridiano.  

 In Ricerca di Azure le query geospaziali che includono una longitudine di 180 gradi funzioneranno come previsto se la forma della query è rettangolare e le coordinate sono allineate a un layout di griglia lungo la longitudine e la latitudine (ad esempio, `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). In caso contrario, per le forme non rettangolari o non allineate, considerare l'approccio basato sulla suddivisione del poligono.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Limitazioni relative alle dimensioni dei filtri 

 Esistono limiti alle dimensioni e alla complessità delle espressioni filtro che è possibile inviare a Ricerca di Azure. I limiti si basano approssimativamente sul numero di clausole nell'espressione filtro. In genere, se si hanno centinaia di clausole, è probabile che si superi il limite. È consigliabile progettare l'applicazione in modo che non generi filtri con dimensioni non vincolate.  


## <a name="filter-examples"></a>Esempi di filtro  

 Trovare tutti gli alberghi con una tariffa di base inferiore a 200 dollari che sono classificati 4 o superiore:  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 Trovare tutti gli alberghi diversi da "Roach Motel" ristrutturati a partire dal 2010:  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Trovare tutti gli alberghi con una tariffa di base inferiore a 200 dollari che sono stati rimodernati dal 2010 con un valore letterale datetime include informazioni sul fuso orario dell'ora solare Pacifico:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 Trovare tutti gli alberghi per non fumatori, con parcheggio incluso:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- OPPURE -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Trovare tutti gli alberghi di lusso o con parcheggio incluso e di categoria 5 o superiore:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Trovare tutti gli alberghi con il tag "wifi", in cui ogni albergo ha i tag archiviati in un campo Collection(Edm.String):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Trovare tutti gli alberghi con il tag "motel":  

```
$filter=tags/all(t: t ne 'motel')
```

 Trovare tutti gli alberghi con qualsiasi tag:  

```
$filter=tags/any()
```

Trovare tutti gli alberghi che non hanno tag:  

```
$filter=not tags/any()
```


 Trovare tutti gli alberghi entro 10 chilometri da un punto di riferimento specificato (in cui la località è un campo di tipo Edm.GeographyPoint):  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Trovare tutti gli alberghi entro un determinato riquadro di visualizzazione designato come poligono (in cui la località è un campo di tipo Edm.GeographyPoint). Si noti che il poligono è chiuso (il primo e l'ultimo set di punti devono coincidere) e che [i punti devono essere elencati in senso antiorario](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Trovare tutti gli alberghi senza valori nel campo "description" o con il valore esplicitamente impostato su null:  

```
$filter=description eq null
```

Trovare tutti gli alberghi con nome uguale a 'Motel rocca' o 'hotel Budget'). Frasi contengono spazi, che è un delimitatore di impostazione predefinita. È possibile specicfy un delimitatore alternativo tra virgolette singole come terzo parametro di stringa:  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Trovare tutti gli alberghi con nome uguale a "Roach motel" o "Budget hotel" separati da "|":  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Trovare tutti gli alberghi con il tag "wifi" o "pool":  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Trovare una corrispondenza in frasi all'interno di una raccolta, ad esempio 'towel riscaldato rack' o 'asciugacapelli incluso' nei tag. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

Trovare tutti gli alberghi senza il tag "motel" o "cabin":  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Trovare documenti con la parola "waterfront". Questa query di filtro è identica a una [richiesta di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) con `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Trovare i documenti con la parola "hostel" e classificazione 4 o superiore oppure i documenti con la parola "motel" e classificazione 5. Si noti che questa richiesta non potrebbe essere espressa senza la funzione `search.ismatchscoring`.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Trovare i documenti senza la parola "luxury".

```
$filter=not search.ismatch('luxury') 
```

Trovare i documenti con la frase "ocean view" o classificazione 5. La query `search.ismatchscoring` verrà eseguita solo sui campi `hotelName` e `description`.
Si noti che verranno restituiti anche i documenti che corrispondono solo alla seconda clausola della disgiunzione, ovvero gli alberghi di categoria 5 o superiore. Per indicare che tali documenti non corrispondono ad alcuna delle parti classificate dell'espressione, verranno restituiti con punteggio pari a zero.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Trovare i documenti in cui i termini "hotel" e "airport" sono separati da non più di 5 parole nella descrizione dell'albergo e in cui non è consentito fumare. Questa query usa il [linguaggio di query Lucene completo](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Sintassi order-by

Il parametro **$orderby** accetta un elenco contenente fino a 32 espressioni separate da virgole in formato `sort-criteria [asc|desc]`. I criteri di ordinamento possono essere il nome di un campo `sortable` oppure una chiamata alle funzioni `geo.distance` o `search.score`. È possibile usare `asc` o `desc` per specificare in modo esplicito l'ordinamento. L'ordine predefinito è crescente.

Se più documenti hanno gli stessi criteri di ordinamento e la funzione `search.score` non è usata (ad esempio, se si ordina in base a un campo `rating` numerico e tre documenti hanno la classificazione 4), i collegamenti verranno interrotti in base al punteggio dei documenti in ordine decrescente. Quando i punteggi dei documenti sono uguali (ad esempio, quando nella richiesta non sono specificate query di ricerca full-text), l'ordinamento relativo dei documenti correlati è indeterminato.
 
È possibile specificare più criteri di ordinamento. L'ordine delle espressioni determina l'ordinamento finale. Ad esempio, per ordinare per punteggio decrescente, seguito dalla classificazione, la sintassi sarà `$orderby=search.score() desc,rating desc`.

La sintassi per `geo.distance` in **$orderby** è la stessa che in **$filter**. Quando si usa `geo.distance` in **$orderby**, il campo a cui si applica deve essere di tipo `Edm.GeographyPoint` e deve anche essere `sortable`.  

La sintassi per `search.score` in **$orderby** è `search.score()`. La funzione `search.score` non accetta parametri.  
 

## <a name="order-by-examples"></a>Esempi di order-by

Ordinare gli alberghi per tariffa di base crescente:

```
$orderby=baseRate asc
```

Ordinare gli alberghi per classificazione decrescente, quindi per tariffa di base crescente. Tenere presente che l'ordinamento predefinito è crescente:

```
$orderby=rating desc,baseRate
```

Ordinare gli alberghi per classificazione decrescente, quindi per distanza crescente dalle coordinate specificate:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Ordinare gli alberghi per search.score e classificazione decrescenti e quindi per distanza crescente dalle coordinate specificate in modo che, tra due hotel con classificazioni identiche, il più vicino sia elencato per primo:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Sintassi OData non supportata

-   Espressioni aritmetiche  

-   Funzioni (tranne le funzioni geospaziali distance e intersects)  

-   `any/all` con espressioni lambda arbitrarie  

## <a name="see-also"></a>Vedere anche   

+ [Navigazione sfaccettata nella ricerca di Azure](search-faceted-navigation.md) 
+ [Filtri in Ricerca di Azure](search-filters.md) 
+ [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure) 
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi di query semplice in Ricerca di Azure](query-simple-syntax.md)   
