---
title: Riferimento di operatore di confronto OData - ricerca di Azure
description: Gli operatori di confronto OData, eq, ne, gt, lt, ge e le, nelle query di ricerca di Azure.
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079925"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Gli operatori di confronto OData in ricerca di Azure - `eq`, `ne`, `gt`, `lt`, `ge`, e `le`

L'operazione più semplice in un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) in ricerca di Azure consiste nel confrontare un campo in un determinato valore. Due tipi di confronto sono possibili: confronto di uguaglianza e confronto di intervalli. È possibile usare gli operatori seguenti per confrontare un campo su valore costante:

Operatori di uguaglianza:

- `eq`: Verificare se è un campo **uguale a** un valore costante
- `ne`: Verificare se è un campo **non è uguale a** un valore costante

Operatori di intervallo:

- `gt`: Verificare se è un campo **maggiore** un valore costante
- `lt`: Verificare se è un campo **minore di** un valore costante
- `ge`: Verificare se è un campo **maggiore o uguale a** un valore costante
- `le`: Verificare se è un campo **minore o uguale a** un valore costante

È possibile usare gli operatori di intervallo in combinazione con il [gli operatori logici](search-query-odata-logical-operators.md) per verificare se è un campo all'interno di un determinato intervallo di valori. Vedere le [esempi](#examples) più avanti in questo articolo.

> [!NOTE]
> Se si preferisce, è possibile inserire il valore costante sul lato sinistro dell'operatore e il nome del campo sul lato destro. Per gli operatori di intervallo, il significato del confronto è invertito. Ad esempio, se il valore della costante è a sinistra, `gt` verifica se il valore della costante è maggiore del campo. È anche possibile usare gli operatori di confronto per confrontare il risultato di una funzione, ad esempio `geo.distance`, con un valore. Le funzioni, ad esempio per un valore booleano `search.ismatch`, il confronto del risultato a `true` o `false` è facoltativo.

## <a name="syntax"></a>Sintassi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di un'espressione di OData che utilizza gli operatori di confronto.

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
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Esistono due forme di espressioni di confronto. L'unica differenza tra di essi è se la costante viene visualizzato nei left - o destro lato dell'operatore. L'espressione su altro lato dell'operatore deve essere un **variabile** o una chiamata di funzione. Una variabile può essere un nome di campo o una variabile di intervallo nel caso di un [espressione lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipi di dati per i confronti

I tipi di dati su entrambi i lati di un operatore di confronto devono essere compatibili. Ad esempio, se il lato sinistro è un campo di tipo `Edm.DateTimeOffset`, lato destro deve essere una costante di data e ora. Tipi di dati numerici sono più flessibili. È possibile confrontare le variabili e funzioni di qualsiasi tipo numerico con costanti di qualsiasi altro tipo numerico, con alcune limitazioni, come descritto nella tabella seguente.

| Tipo di funzione o variabile | Tipo di valore costante | Limitazioni |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Il confronto è soggetto a [regole particolari di `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Costante viene convertita in `Edm.Double`, con conseguente perdita di precisione per i valori dell'ordine di grandezza |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Per i confronti `NaN`, `-INF`, o `INF` non sono consentiti |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | Costante viene convertita in `Edm.Int64` prima del confronto |
| `Edm.Int32` | `Edm.Double` | Per i confronti `NaN`, `-INF`, o `INF` non sono consentiti |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Per i confronti che non sono consentiti, ad esempio il confronto di un campo di tipo `Edm.Int64` a `NaN`, API REST di ricerca di Azure restituirà un "HTTP 400: Errore di richiesta non valida".

> [!IMPORTANT]
> Anche se i confronti di tipo numerico sono flessibili, è consigliabile la scrittura di confronti nei filtri in modo che il valore della costante è dello stesso tipo di dati della variabile o funzione con cui è confrontato. Ciò è particolarmente importante quando si unisce a virgola mobile e interi, dove sono possibili le conversioni implicite che perdono la precisione.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Per i casi speciali `null` e `NaN`

Quando si usano gli operatori di confronto, è importante tenere presente che tutti i campi non di raccolta in ricerca di Azure possono essere potenzialmente `null`. La tabella seguente illustra tutti i possibili risultati di un'espressione di confronto in cui possono essere entrambi i lati `null`:

| Operator | Risultato quando solo il campo o la variabile è `null` | Risultato quando è solo la costante `null` | Risultato quando sia il campo o una variabile e costante sono `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Errore di richiesta non valido | HTTP 400: Errore di richiesta non valido |
| `lt` | `false` | HTTP 400: Errore di richiesta non valido | HTTP 400: Errore di richiesta non valido |
| `ge` | `false` | HTTP 400: Errore di richiesta non valido | HTTP 400: Errore di richiesta non valido |
| `le` | `false` | HTTP 400: Errore di richiesta non valido | HTTP 400: Errore di richiesta non valido |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In sintesi, `null` è uguale solo a se stesso e non minore o maggiore di qualsiasi altro valore.

Se l'indice includa i campi di tipo `Edm.Double` ed è caricare `NaN` valori ai suddetti campi, è necessario tenere conto che durante la scrittura di filtri. Ricerca di Azure implementa lo standard IEEE 754 per la gestione `NaN` valori e i confronti con tali valori producono risultati poco chiaro, come illustrato nella tabella seguente.

| Operator | Risultato quando è presente almeno un operando `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

In sintesi, `NaN` non corrisponde ad alcun valore, incluso se stesso.

### <a name="comparing-geo-spatial-data"></a>Confronto dei dati geospaziali

Non è possibile confrontare direttamente un campo di tipo `Edm.GeographyPoint` con un valore costante, ma è possibile usare il `geo.distance` (funzione). Questa funzione restituisce un valore di tipo `Edm.Double`, pertanto è possibile confrontarlo con un valore numerico costante per filtrare in base alla distanza dalle coordinate di geo-spaziali costante. Vedere le [esempi](#examples) sotto.

### <a name="comparing-string-data"></a>Confronto di dati stringa

È possibile confrontare le stringhe nei filtri per corrispondenze esatte usando il `eq` e `ne` operatori. Questi confronti sono tra maiuscole e minuscole.

## <a name="examples"></a>Esempi

Corrispondenza documenti dove il `Rating` campo è compreso tra 3 e 5, inclusi:

    Rating ge 3 and Rating le 5

Corrispondenza documenti dove il `Location` campo è minore di 2 km dalla latitudine e longitudine:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Corrispondenza documenti dove il `LastRenovationDate` campo è maggiore o uguale al 1 ° gennaio 2015, mezzanotte UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Corrispondenza documenti dove il `Details/Sku` campo non `null`:

    Details/Sku ne null

Documenti per gli hotel in cui almeno una chat room ha tipo di "Chat Deluxe", dove la stringa del `Rooms/Type` campo corrisponde esattamente al filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
