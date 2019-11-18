---
title: Riferimento all'operatore di confronto OData
titleSuffix: Azure Cognitive Search
description: Sintassi e documentazione di riferimento per l'uso degli operatori di confronto OData (EQ, ne, gt, LT, GE e le) in Azure ricerca cognitiva query.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113215"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operatori di confronto OData in Azure ricerca cognitiva-`eq`, `ne`, `gt`, `lt`, `ge`e `le`

L'operazione più semplice in un' [espressione di filtro OData](query-odata-filter-orderby-syntax.md) in Azure ricerca cognitiva consiste nel confrontare un campo con un valore specificato. Sono possibili due tipi di confronto: confronto di uguaglianza e confronto di intervalli. Per confrontare un campo con un valore costante, è possibile usare gli operatori seguenti:

Operatori di uguaglianza:

- `eq`: verificare se un campo è **uguale a** un valore costante
- `ne`: verificare se un campo **non è uguale a** un valore costante

Operatori di intervallo:

- `gt`: verificare se un campo è **maggiore di** un valore costante
- `lt`: verificare se un campo è **minore di** un valore costante
- `ge`: verificare se un campo è **maggiore o uguale a** un valore costante
- `le`: verificare se un campo è **minore o uguale a** un valore costante

È possibile utilizzare gli operatori di intervallo in combinazione con gli [operatori logici](search-query-odata-logical-operators.md) per verificare se un campo rientra in un determinato intervallo di valori. Vedere gli [esempi](#examples) più avanti in questo articolo.

> [!NOTE]
> Se si preferisce, è possibile inserire il valore della costante sul lato sinistro dell'operatore e il nome del campo sul lato destro. Per gli operatori di intervallo, il significato del confronto è invertito. Se, ad esempio, il valore della costante è a sinistra, `gt` verificherebbe se il valore della costante è maggiore del campo. È inoltre possibile utilizzare gli operatori di confronto per confrontare il risultato di una funzione, ad esempio `geo.distance`, con un valore. Per le funzioni booleane, ad esempio `search.ismatch`, confrontare il risultato con `true` o `false` è facoltativo.

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
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Esistono due forme di espressioni di confronto. L'unica differenza consiste nel fatto che la costante venga visualizzata sul lato sinistro o destro dell'operatore. L'espressione sull'altro lato dell'operatore deve essere una **variabile** o una chiamata di funzione. Una variabile può essere un nome di campo o una variabile di intervallo nel caso di un' [espressione lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipi di dati per i confronti

I tipi di dati su entrambi i lati di un operatore di confronto devono essere compatibili. Se, ad esempio, il lato sinistro è un campo di tipo `Edm.DateTimeOffset`, il lato destro deve essere una costante di data e ora. I tipi di dati numerici sono più flessibili. È possibile confrontare variabili e funzioni di qualsiasi tipo numerico con costanti di qualsiasi altro tipo numerico, con alcune limitazioni, come descritto nella tabella seguente.

| Tipo di funzione o variabile | Tipo di valore costante | Limitazioni |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Il confronto è soggetto a [regole speciali per `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | La costante viene convertita in `Edm.Double`, causando una perdita di precisione per i valori di grandi dimensioni |
| `Edm.Double` | `Edm.Int32` | N/D |
| `Edm.Int64` | `Edm.Double` | I confronti con `NaN`, `-INF`o `INF` non sono consentiti |
| `Edm.Int64` | `Edm.Int64` | N/D |
| `Edm.Int64` | `Edm.Int32` | Costante convertita in `Edm.Int64` prima del confronto |
| `Edm.Int32` | `Edm.Double` | I confronti con `NaN`, `-INF`o `INF` non sono consentiti |
| `Edm.Int32` | `Edm.Int64` | N/D |
| `Edm.Int32` | `Edm.Int32` | N/D |

Per i confronti non consentiti, ad esempio il confronto di un campo di tipo `Edm.Int64` `NaN`, l'API REST di Azure ricerca cognitiva restituirà un errore "HTTP 400: richiesta non valida".

> [!IMPORTANT]
> Sebbene i confronti di tipi numerici siano flessibili, si consiglia vivamente di scrivere confronti nei filtri in modo che il valore costante sia dello stesso tipo di dati della variabile o della funzione a cui viene confrontato. Questa operazione è particolarmente importante quando si combinano valori a virgola mobile e interi, in cui sono possibili conversioni implicite che perdono la precisione.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casi speciali per `null` e `NaN`

Quando si usano gli operatori di confronto, è importante ricordare che tutti i campi non di raccolta in Azure ricerca cognitiva possono essere potenzialmente `null`. La tabella seguente mostra tutti i possibili risultati per un'espressione di confronto in cui possono essere `null`entrambi i lati:

| Operatore | Risultato quando viene `null` solo il campo o la variabile | Risultato quando viene `null` solo la costante | Risultato quando il campo o la variabile e la costante sono `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: errore di richiesta non valida | HTTP 400: errore di richiesta non valida |
| `lt` | `false` | HTTP 400: errore di richiesta non valida | HTTP 400: errore di richiesta non valida |
| `ge` | `false` | HTTP 400: errore di richiesta non valida | HTTP 400: errore di richiesta non valida |
| `le` | `false` | HTTP 400: errore di richiesta non valida | HTTP 400: errore di richiesta non valida |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In breve, `null` è uguale solo a se stesso e non è minore o maggiore di qualsiasi altro valore.

Se l'indice include campi di tipo `Edm.Double` e si caricano `NaN` valori in questi campi, sarà necessario tenere conto di questo durante la scrittura dei filtri. Azure ricerca cognitiva implementa lo standard IEEE 754 per la gestione dei valori `NaN` e i confronti con tali valori producono risultati non evidenti, come illustrato nella tabella seguente.

| Operatore | Risultato quando almeno un operando è `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

In breve, `NaN` non è uguale a qualsiasi valore, incluso se stesso.

### <a name="comparing-geo-spatial-data"></a>Confronto tra dati spaziali

Non è possibile confrontare direttamente un campo di tipo `Edm.GeographyPoint` con un valore costante, ma è possibile usare la funzione `geo.distance`. Questa funzione restituisce un valore di tipo `Edm.Double`, quindi è possibile confrontarla con una costante numerica per filtrare in base alla distanza dalle coordinate geospaziali costanti. Vedere gli [esempi](#examples) seguenti.

### <a name="comparing-string-data"></a>Confronto di dati stringa

Le stringhe possono essere confrontate nei filtri per le corrispondenze esatte usando gli operatori `eq` e `ne`. Questi confronti distinguono tra maiuscole e minuscole.

## <a name="examples"></a>esempi

Trovare la corrispondenza con i documenti in cui il campo `Rating` è compreso tra 3 e 5 inclusi:

    Rating ge 3 and Rating le 5

Trovare la corrispondenza con i documenti in cui il campo `Location` è inferiore a 2 chilometri dalla latitudine e dalla longitudine specificate:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Trova la corrispondenza con i documenti in cui il campo `LastRenovationDate` è maggiore o uguale al 1 ° gennaio 2015, mezzanotte UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Trovare la corrispondenza con i documenti in cui il campo `Details/Sku` non è `null`:

    Details/Sku ne null

Trova la corrispondenza dei documenti per gli hotel in cui almeno una stanza è di tipo "Deluxe Room", dove la stringa del campo `Rooms/Type` corrisponde esattamente al filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passaggi successivi  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
