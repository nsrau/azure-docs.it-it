---
title: Guida di riferimento agli operatori di confronto OData-ricerca di Azure
description: Operatori di confronto OData, EQ, ne, gt, LT, GE e le nelle query di ricerca di Azure.
ms.date: 06/13/2019
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648069"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operatori di confronto OData in ricerca di `eq`Azure `ne`: `gt`, `lt`, `ge`,, e`le`

L'operazione più semplice in un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) in ricerca di Azure consiste nel confrontare un campo con un valore specificato. Sono possibili due tipi di confronto: confronto di uguaglianza e confronto di intervalli. Per confrontare un campo con un valore costante, è possibile usare gli operatori seguenti:

Operatori di uguaglianza:

- `eq`: Verificare se un campo è **uguale a** un valore costante
- `ne`: Verificare se un campo **non è uguale a** un valore costante

Operatori di intervallo:

- `gt`: Verificare se un campo è **maggiore di** un valore costante
- `lt`: Verificare se un campo è **minore di** un valore costante
- `ge`: Verificare se un campo è **maggiore o uguale a** un valore costante
- `le`: Verificare se un campo è **minore o uguale a** un valore costante

È possibile utilizzare gli operatori di intervallo in combinazione con gli [operatori logici](search-query-odata-logical-operators.md) per verificare se un campo rientra in un determinato intervallo di valori. Vedere gli [esempi](#examples) più avanti in questo articolo.

> [!NOTE]
> Se si preferisce, è possibile inserire il valore della costante sul lato sinistro dell'operatore e il nome del campo sul lato destro. Per gli operatori di intervallo, il significato del confronto è invertito. Se, ad esempio, il valore della costante è a sinistra `gt` , verifica se il valore della costante è maggiore del campo. È inoltre possibile utilizzare gli operatori di confronto per confrontare il risultato di una funzione, ad `geo.distance`esempio, con un valore. Per le funzioni booleane come `search.ismatch`, il confronto tra il `true` risultato `false` e o è facoltativo.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che usa gli operatori di confronto.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

Esistono due forme di espressioni di confronto. L'unica differenza consiste nel fatto che la costante venga visualizzata sul lato sinistro o destro dell'operatore. L'espressione sull'altro lato dell'operatore deve essere una **variabile** o una chiamata di funzione. Una variabile può essere un nome di campo o una variabile di intervallo nel caso di un' [espressione lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipi di dati per i confronti

I tipi di dati su entrambi i lati di un operatore di confronto devono essere compatibili. Se, ad esempio, il lato sinistro è un campo di `Edm.DateTimeOffset`tipo, il lato destro deve essere una costante di data e ora. I tipi di dati numerici sono più flessibili. È possibile confrontare variabili e funzioni di qualsiasi tipo numerico con costanti di qualsiasi altro tipo numerico, con alcune limitazioni, come descritto nella tabella seguente.

| Tipo di funzione o variabile | Tipo di valore costante | Limitazioni |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Il confronto è soggetto a [regole speciali `NaN` per](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | La costante viene convertita `Edm.Double`in, causando una perdita di precisione per i valori di grandi dimensioni |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | I confronti `-INF`con `NaN`, `INF` o non sono consentiti |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | Costante convertita in `Edm.Int64` prima del confronto |
| `Edm.Int32` | `Edm.Double` | I confronti `-INF`con `NaN`, `INF` o non sono consentiti |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Per i confronti non consentiti, ad esempio il confronto di `Edm.Int64` un `NaN`campo di tipo a, l'API REST di ricerca di Azure restituirà "http 400: Errore di richiesta non valida.

> [!IMPORTANT]
> Sebbene i confronti di tipi numerici siano flessibili, si consiglia vivamente di scrivere confronti nei filtri in modo che il valore costante sia dello stesso tipo di dati della variabile o della funzione a cui viene confrontato. Questa operazione è particolarmente importante quando si combinano valori a virgola mobile e interi, in cui sono possibili conversioni implicite che perdono la precisione.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casi speciali per `null` e`NaN`

Quando si usano gli operatori di confronto, è importante ricordare che tutti i campi non di raccolta in ricerca di Azure `null`possono essere potenzialmente. La tabella seguente mostra tutti i possibili risultati per un'espressione di confronto in cui entrambi i lati possono `null`essere:

| Operator | Risultato quando solo il campo o la variabile è`null` | Risultato quando solo la costante è`null` | Risultato quando il campo o la variabile e la costante sono`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `lt` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `ge` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `le` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In breve, `null` è uguale solo a se stesso e non è minore o maggiore di qualsiasi altro valore.

Se l'indice include campi di tipo `Edm.Double` e si caricano `NaN` i valori in questi campi, sarà necessario tenere conto di questo durante la scrittura dei filtri. Ricerca di Azure implementa lo standard IEEE 754 per `NaN` la gestione dei valori e i confronti con tali valori producono risultati non evidenti, come illustrato nella tabella seguente.

| Operator | Risultato quando almeno un operando è`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

In breve, `NaN` non è uguale a qualsiasi valore, incluso se stesso.

### <a name="comparing-geo-spatial-data"></a>Confronto tra dati spaziali

Non è possibile confrontare direttamente un campo di `Edm.GeographyPoint` tipo con un valore costante, ma è possibile usare `geo.distance` la funzione. Questa funzione restituisce un valore di tipo `Edm.Double`, quindi è possibile confrontarla con una costante numerica per filtrare in base alla distanza dalle coordinate geospaziali costanti. Vedere gli [esempi](#examples) seguenti.

### <a name="comparing-string-data"></a>Confronto di dati stringa

Le stringhe possono essere confrontate nei filtri per le `eq` corrispondenze esatte usando gli operatori e. `ne` Questi confronti distinguono tra maiuscole e minuscole.

## <a name="examples"></a>Esempi

Trova la corrispondenza dei `Rating` documenti in cui il campo è compreso tra 3 e 5 inclusi:

    Rating ge 3 and Rating le 5

Trovare la corrispondenza con `Location` i documenti in cui il campo è inferiore a 2 chilometri dalla latitudine e dalla longitudine specificate:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Trova la corrispondenza dei `LastRenovationDate` documenti in cui il campo è maggiore o uguale al 1 ° gennaio 2015, mezzanotte UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Trova la corrispondenza dei `Details/Sku` documenti in cui `null`il campo non è:

    Details/Sku ne null

Trova la corrispondenza dei documenti per gli hotel in cui almeno una stanza è di tipo "Deluxe Room", `Rooms/Type` dove la stringa del campo corrisponde esattamente al filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
