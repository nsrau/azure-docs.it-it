---
title: Riferimento alla sintassi delle espressioni OData
titleSuffix: Azure Cognitive Search
description: Grammatica formale e specifica della sintassi per le espressioni OData in Azure ricerca cognitiva query.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793242"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure

Azure ricerca cognitiva usa le [espressioni OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) come parametri nell'intera API. In genere, le espressioni OData vengono utilizzate per i parametri `$orderby` e `$filter`. Queste espressioni possono essere complesse, contenenti più clausole, funzioni e operatori. Tuttavia, anche le espressioni OData semplici come i percorsi delle proprietà vengono usate in molte parti dell'API REST di Azure ricerca cognitiva. Ad esempio, le espressioni di percorso vengono usate per fare riferimento a sottocampi di campi complessi in qualsiasi punto dell'API, ad esempio quando si elencano i sottocampi in un componente di [Suggerimento](index-add-suggesters.md), una [funzione](index-add-scoring-profiles.md)di assegnazione dei punteggi, il `$select` parametro o anche la [ricerca in campo nelle query Lucene ](query-lucene-syntax.md).

Questo articolo descrive tutte queste forme di espressioni OData usando una grammatica formale. È inoltre disponibile un [diagramma interattivo](#syntax-diagram) che consente di esplorare visivamente la grammatica.

## <a name="formal-grammar"></a>Grammatica formale

È possibile descrivere il subset del linguaggio OData supportato da Azure ricerca cognitiva usando una grammatica EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)). Le regole sono elencate "dall'alto verso il basso", a partire dalle espressioni più complesse, e le suddivide in espressioni più primitive. Nella parte superiore sono presenti le regole di grammatica che corrispondono a parametri specifici dell'API REST di Azure ricerca cognitiva:

- [`$filter`](search-query-odata-filter.md), definito dalla regola `filter_expression`.
- [`$orderby`](search-query-odata-orderby.md), definito dalla regola `order_by_expression`.
- [`$select`](search-query-odata-select.md), definito dalla regola `select_expression`.
- Percorsi dei campi, definiti dalla regola di `field_path`. I percorsi dei campi vengono usati nell'API. Possono fare riferimento a campi di primo livello di un indice o a campi secondari con uno o più predecessori di [campo complessi](search-howto-complex-data-types.md) .

Quando EBNF è un [diagramma della sintassi](https://en.wikipedia.org/wiki/Syntax_diagram) esplorabile che consente di esplorare in modo interattivo la grammatica e le relazioni tra le regole.

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

Per esplorare visivamente la grammatica del linguaggio OData supportata da Azure ricerca cognitiva, provare il diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Vedi anche  

- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Azure ricerca cognitiva](query-simple-syntax.md)
