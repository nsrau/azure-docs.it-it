---
title: Riferimento alla sintassi delle espressioni ODataOData expression syntax reference
titleSuffix: Azure Cognitive Search
description: Specifica formale della grammatica e della sintassi per le espressioni OData nelle query di Ricerca cognitiva di Azure.Formal grammar and syntax specification for OData expressions in Azure Cognitive Search queries.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793242"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>OData expression syntax reference for Azure Cognitive Search

Ricerca cognitiva di Azure usa [le espressioni OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) come parametri in tutta l'API. In genere, le espressioni OData vengono utilizzate per i `$orderby` parametri e `$filter` . Queste espressioni possono essere complesse e contenenti più clausole, funzioni e operatori. Tuttavia, anche semplici espressioni OData come i percorsi di proprietà vengono usate in molte parti dell'API REST di Ricerca cognitiva di Azure.However, even simple OData expressions like property paths are used in many parts of the Azure Cognitive Search REST API. Ad esempio, le espressioni di percorso vengono utilizzate per fare riferimento ai sottocampi di campi complessi in qualsiasi `$select` punto dell'API, ad esempio quando si elencano i sottocampi in un [suggerimento,](index-add-suggesters.md)una funzione di [punteggio,](index-add-scoring-profiles.md)il parametro o persino la [ricerca in campi nelle query Lucene](query-lucene-syntax.md).

In questo articolo vengono descritte tutte queste forme di espressioni OData utilizzando una grammatica formale. C'è anche un [diagramma interattivo](#syntax-diagram) per aiutare a esplorare visivamente la grammatica.

## <a name="formal-grammar"></a>Grammatica formale

È possibile descrivere il sottoinsieme del linguaggio OData supportato da Ricerca cognitiva di Azure usando una grammatica EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)). Le regole sono elencate "dall'alto verso il basso", a partire dalle espressioni più complesse e suddividendele in espressioni più primitive. Nella parte superiore sono le regole grammaticali che corrispondono a parametri specifici dell'API REST di Ricerca cognitiva di Azure:At the top are the grammar rules that correspond to specific parameters of the Azure Cognitive Search REST API:

- [`$filter`](search-query-odata-filter.md), definito `filter_expression` dalla regola.
- [`$orderby`](search-query-odata-orderby.md), definito `order_by_expression` dalla regola.
- [`$select`](search-query-odata-select.md), definito `select_expression` dalla regola.
- Percorsi dei campi, definiti dalla `field_path` regola. I percorsi dei campi vengono utilizzati in tutta l'API. Possono fare riferimento a campi di primo livello di un indice o a sottocampi con uno o più predecessori di [campo complessi.](search-howto-complex-data-types.md)

Dopo l'EBNF è un diagramma di [sintassi](https://en.wikipedia.org/wiki/Syntax_diagram) esplorabile che consente di esplorare in modo interattivo la grammatica e le relazioni tra le sue regole.

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

Per esplorare visivamente la grammatica del linguaggio OData supportata da Ricerca cognitiva di Azure, provare il diagramma della sintassi interattiva:To visually explore the OData language grammar supported by Azure Cognitive Search, try the interactive syntax diagram:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Vedere anche  

- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Ricerca cognitiva di AzureSimple query syntax in Azure Cognitive Search](query-simple-syntax.md)
