---
title: Riferimento alla sintassi di OData expression - ricerca di Azure
description: Specifica sintassi e grammatica formale per le espressioni di OData nelle query di ricerca di Azure.
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
ms.openlocfilehash: ebe41ba61ac5136900328db9c35acb8551dcd5b2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428651"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Riferimento alla sintassi di espressione di OData per ricerca di Azure

Ricerca di Azure Usa [espressioni OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) come parametri in tutta l'API. In genere, le espressioni di OData sono usate per la `$orderby` e `$filter` parametri. Queste espressioni possono essere complesse, contenente più clausole, funzioni e operatori. Tuttavia, anche in semplici espressioni OData come proprietà vengono usati percorsi in molte parti dell'API REST di ricerca di Azure. Ad esempio, le espressioni di percorso vengono usate per fare riferimento a campi secondari dei campi complessi ovunque nell'API, ad esempio quando elenco campi secondari in un [dello strumento suggerimenti](index-add-suggesters.md), un [funzione di assegnazione dei punteggi](index-add-scoring-profiles.md), il `$select` parametro , o addirittura [con campo di ricerca nelle query di Lucene](query-lucene-syntax.md).

Questo articolo descrive tutte le queste forme di espressioni di OData tramite una grammatica formale. È inoltre disponibile un' [diagramma interattivo](#syntax-diagram) che aiuta a esplorare visivamente la grammatica.

## <a name="formal-grammar"></a>Grammatica formale

È possibile descrivere il subset del linguaggio OData supportato da ricerca di Azure tramite un EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) grammatica. "Top-down", iniziando con le espressioni più complesse e li scomporre in espressioni più primitive sono elencate le regole. Nella parte superiore sono le regole di grammatica che corrispondono ai parametri specifici dell'API REST di ricerca di Azure:

- [`$filter`](search-query-odata-filter.md), definito dal `filter_expression` regola.
- [`$orderby`](search-query-odata-orderby.md), definito dal `order_by_expression` regola.
- [`$select`](search-query-odata-select.md), definito dal `select_expression` regola.
- I percorsi, definiti dal campo di `field_path` regola. I percorsi di campo vengono usati in tutta l'API. Cui possono fare riferimento a entrambi campi di livello superiore di un indice o campi secondari con uno o più [campo complesso](search-howto-complex-data-types.md) predecessori.

Dopo che è un esplorabile di EBNF [diagramma della sintassi](https://en.wikipedia.org/wiki/Syntax_diagram) che consente di esplorare in modo interattivo la grammatica e le relazioni tra le regole.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Diagramma della sintassi

Per esplorare visivamente la grammatica del linguaggio OData supportata da ricerca di Azure, provare a eseguire il diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Vedere anche  

- [Filtri in Ricerca di Azure](search-filters.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Ricerca di Azure](query-simple-syntax.md)
