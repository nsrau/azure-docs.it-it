---
title: Riferimento all'operatore di confronto ODataOData comparison operator reference
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso degli operatori di confronto OData (eq, ne, gt, lt, ge e le) nelle query di Ricerca cognitiva di Azure.Syntax and reference documentation for using OData comparison operators (eq, ne, gt, lt, ge, and le) in Azure Cognitive Search queries.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113215"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operatori di confronto OData `eq`in `ne` `gt`Ricerca `lt` `ge`cognitiva di Azure - , , , , e`le`

L'operazione di base in [un'espressione](query-odata-filter-orderby-syntax.md) di filtro OData in Ricerca cognitiva di Azure consiste nel confrontare un campo con un determinato valore. Sono possibili due tipi di confronto: il confronto di uguaglianza e il confronto tra intervalli. È possibile utilizzare gli operatori seguenti per confrontare un campo con un valore costante:

Operatori di uguaglianza:

- `eq`: verifica se un campo è **uguale a** un valore costante
- `ne`: verifica se un campo non è **uguale a** un valore costante

Operatori di intervallo:

- `gt`: verifica se un campo è **maggiore di** un valore costante
- `lt`: verifica se un campo è **minore di** un valore costante
- `ge`: verifica se un campo è **maggiore o uguale a** un valore costante
- `le`: verifica se un campo è **minore o uguale a** un valore costante

È possibile utilizzare gli operatori di intervallo in combinazione con gli [operatori logici](search-query-odata-logical-operators.md) per verificare se un campo si trova all'interno di un determinato intervallo di valori. Vedere gli [esempi](#examples) più avanti in questo articolo.

> [!NOTE]
> Se si preferisce, è possibile inserire il valore costante sul lato sinistro dell'operatore e il nome del campo sul lato destro. Per gli operatori di intervallo, il significato del confronto è invertito. Ad esempio, se il valore costante `gt` si trova a sinistra, verrà verificato se il valore costante è maggiore del campo. È inoltre possibile utilizzare gli operatori di confronto per `geo.distance`confrontare il risultato di una funzione, ad esempio , con un valore . Per le funzioni `search.ismatch`booleane come `true` `false` , il confronto del risultato con o è facoltativo.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione OData che utilizza gli operatori di confronto.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Esistono due forme di espressioni di confronto. L'unica differenza tra loro è se la costante viene visualizzata sul lato sinistro o destro dell'operatore. L'espressione sull'altro lato dell'operatore deve essere una **variabile** o una chiamata di funzione. Una variabile può essere un nome di campo o una variabile di intervallo nel caso di [un'espressione lambda.](search-query-odata-collection-operators.md)

## <a name="data-types-for-comparisons"></a>Tipi di dati per i confronti

I tipi di dati su entrambi i lati di un operatore di confronto devono essere compatibili. Ad esempio, se il lato `Edm.DateTimeOffset`sinistro è un campo di tipo , il lato destro deve essere una costante data-ora. I tipi di dati numerici sono più flessibili. È possibile confrontare variabili e funzioni di qualsiasi tipo numerico con costanti di qualsiasi altro tipo numerico, con alcune limitazioni, come descritto nella tabella seguente.

| Tipo di variabile o funzione | Tipo di valore costante | Limitazioni |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Il confronto è soggetto a [norme `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Costante viene `Edm.Double`convertita in , con conseguente perdita di precisione per valori di grandi dimensioni |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Confronti con `NaN` `-INF`, `INF` , o non sono consentiti |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | Costante viene `Edm.Int64` convertita in prima del confronto |
| `Edm.Int32` | `Edm.Double` | Confronti con `NaN` `-INF`, `INF` , o non sono consentiti |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Per i confronti non consentiti, ad esempio `Edm.Int64` `NaN`il confronto di un campo di tipo con , l'API REST di Ricerca cognitiva di Azure restituirà un errore "HTTP 400: Richiesta non valida".

> [!IMPORTANT]
> Anche se i confronti di tipo numerico sono flessibili, è consigliabile scrivere confronti nei filtri in modo che il valore costante sia dello stesso tipo di dati della variabile o della funzione con cui viene confrontato. Ciò è particolarmente importante quando si combinano valori a virgola mobile e integer, in cui sono possibili conversioni implicite che perdono precisione.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casi speciali `null` per e`NaN`

Quando si usano gli operatori di confronto, è importante ricordare che tutti `null`i campi non di raccolta in Ricerca cognitiva di Azure possono potenzialmente essere . La tabella seguente mostra tutti i possibili risultati per `null`un'espressione di confronto in cui entrambi i lati possono essere:

| Operatore | Risultato quando solo il campo o la variabile è`null` | Risultato quando solo la costante è`null` | Risultato quando sia il campo o la variabile che la costante sono`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `lt` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `ge` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `le` | `false` | HTTP 400: Errore di richiesta non valida | HTTP 400: Errore di richiesta non valida |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In sintesi, `null` è uguale solo a se stesso e non è minore o maggiore di qualsiasi altro valore.

Se l'indice include `Edm.Double` campi `NaN` di tipo e si caricano valori in tali campi, sarà necessario tenerne conto quando si scrivono filtri. Ricerca cognitiva di Azure implementa lo standard `NaN` IEEE 754 per la gestione dei valori e i confronti con tali valori producono risultati non ovvi, come illustrato nella tabella seguente.

| Operatore | Risultato quando almeno un operando è`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

In sintesi, `NaN` non è uguale ad alcun valore, compreso se stesso.

### <a name="comparing-geo-spatial-data"></a>Confronto di dati geospaziali

Non è possibile confrontare direttamente `Edm.GeographyPoint` un campo di tipo con `geo.distance` un valore costante, ma è possibile utilizzare la funzione. Questa funzione restituisce `Edm.Double`un valore di tipo , pertanto è possibile confrontarla con una costante numerica per filtrare in base alla distanza dalle coordinate geospaziali costanti. Vedere gli [esempi](#examples) riportati di seguito.

### <a name="comparing-string-data"></a>Confronto dei dati di stringaComparing string data

Le stringhe possono essere confrontate `eq` nei `ne` filtri per le corrispondenze esatte utilizzando gli operatori e . Questi confronti fanno distinzione tra maiuscole e minuscole.

## <a name="examples"></a>Esempi

Abbina i `Rating` documenti in cui il campo è compreso tra 3 e 5, inclusi:

    Rating ge 3 and Rating le 5

Abbina i `Location` documenti in cui il campo è inferiore a 2 chilometri dalla latitudine e dalla longitudine indicate:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Corrispondenza dei `LastRenovationDate` documenti in cui il campo è maggiore o uguale a 1 gennaio 2015, mezzanotte UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Abbina i `Details/Sku` documenti in `null`cui il campo non è:

    Details/Sku ne null

Abbina i documenti per gli hotel in cui almeno una camera è `Rooms/Type` di tipo "Deluxe Room", dove la stringa del campo corrisponde esattamente al filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
