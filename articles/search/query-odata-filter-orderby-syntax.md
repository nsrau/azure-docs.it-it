---
title: Panoramica della lingua ODataOData language overview
titleSuffix: Azure Cognitive Search
description: Panoramica del linguaggio OData per filtri, selezione e ordinamento per le query di Ricerca cognitiva di Azure.OData language overview for filters, select, and order-by for Azure Cognitive Search queries.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153877"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Panoramica del `$filter`linguaggio OData per , e in Ricerca cognitiva di AzureOData language overview for , `$orderby`, and `$select` in Azure Cognitive Search

Ricerca cognitiva di Azure supporta un subset della sintassi delle espressioni OData per **$filter**, **$orderby**e **espressioni di $select.** Le espressioni filtro vengono valutate durante l'analisi della query, vincolando la ricerca a campi specifici o aggiungendo criteri di corrispondenza durante le analisi dell'indice. Le espressioni order-by vengono applicate come passaggio di post-elaborazione su un set di risultati per ordinare i documenti restituiti. Le espressioni di selezione determinano quali campi del documento sono inclusi nel set di risultati. La sintassi di queste espressioni è distinta dalla sintassi di query [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md) utilizzata nel parametro **di ricerca,** anche se esiste una sovrapposizione nella sintassi per il riferimento ai campi.

In questo articolo viene fornita una panoramica del linguaggio delle espressioni OData utilizzato nelle espressioni di filtro, order-by e select. Il linguaggio è presentato "dal basso verso l'alto", a partire dagli elementi più basilari e costruendo su di essi. La sintassi di primo livello per ogni parametro è descritta in un articolo separato:The top-level syntax for each parameter is described in a separate article:

- [sintassi $filter](search-query-odata-filter.md)
- [sintassi del $orderby](search-query-odata-orderby.md)
- [sintassi del $select](search-query-odata-select.md)

Le espressioni OData vanno da semplici a altamente complesse, ma condividono tutti elementi comuni. Le parti più basilari di un'espressione OData in Ricerca cognitiva di Azure sono:The most basic parts of an OData expression in Azure Cognitive Search are:

- **Percorsi di campo**, che fanno riferimento a campi specifici dell'indice.
- **Costanti**, che sono valori letterali di un determinato tipo di dati.

> [!NOTE]
> Terminologia in Ricerca cognitiva di Azure differisce dallo [standard OData](https://www.odata.org/documentation/) in diversi modi. Ciò che chiamiamo un **campo** in Ricerca cognitiva di Azure è denominato **proprietà** in OData e in modo analogo per il percorso del **campo** rispetto al percorso **della proprietà**. Un **indice** contenente **documenti** in Ricerca cognitiva di Azure viene definito più in genere in OData come set di **entità** contenente **le entità**. La terminologia di Ricerca cognitiva di Azure viene usata in questo riferimento.

## <a name="field-paths"></a>Percorsi di campo

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica dei percorsi dei campi.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Un percorso di campo è composto da uno o più **identificatori** separati da barre. Ogni identificatore è una sequenza di caratteri che deve iniziare con una lettera ASCII o un carattere di sottolineatura e contenere solo lettere ASCII, cifre o caratteri di sottolineatura. Le lettere possono essere maiuscole o minuscole.

Un identificatore può fare riferimento al nome di un campo o a`any` una `all`variabile di **intervallo** nel contesto di [un'espressione](search-query-odata-collection-operators.md) di raccolta ( o ) in un filtro. Una variabile di intervallo è simile a una variabile di ciclo che rappresenta l'elemento corrente della raccolta. Per le raccolte complesse, tale variabile rappresenta un oggetto, motivo per cui è possibile utilizzare i percorsi di campo per fare riferimento ai sottocampi della variabile. Questo è analogo alla notazione punto in molti linguaggi di programmazione.

Nella tabella seguente sono riportati esempi di percorsi di campo:

| Percorso campo | Descrizione |
| --- | --- |
| `HotelName` | Si riferisce a un campo di primo livello dell'indice |
| `Address/City` | Fa riferimento `City` al sottocampo di un campo complesso nell'indice; `Address` è di `Edm.ComplexType` tipo in questo esempio |
| `Rooms/Type` | Fa riferimento `Type` al sottocampo di un campo di raccolta complesso nell'indice; `Rooms` è di `Collection(Edm.ComplexType)` tipo in questo esempio |
| `Stores/Address/Country` | Fa riferimento `Country` al sottocampo `Address` del sottocampo di un campo di raccolta complesso nell'indice; `Stores` è di `Collection(Edm.ComplexType)` `Address` tipo ed `Edm.ComplexType` è di tipo in questo esempio |
| `room/Type` | Fa riferimento `Type` al sottocampo `room` della variabile di intervallo, ad esempio nell'espressione di filtro`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Fa riferimento `Country` al sottocampo `Address` del sottocampo `store` della variabile di intervallo, ad esempio nell'espressione di filtro`Stores/any(store: store/Address/Country eq 'Canada')` |

Il significato di un percorso di campo varia a seconda del contesto. Nei filtri, un percorso di campo fa riferimento al valore di una *singola istanza* di un campo nel documento corrente. In altri contesti, ad esempio **$orderby**, **$select**o nella [ricerca a campi nella sintassi Lucene completa,](query-lucene-syntax.md#bkmk_fields)un percorso di campo fa riferimento al campo stesso. Questa differenza ha alcune conseguenze sulla modalità di utilizzo dei percorsi di campo nei filtri.

Considerare il `Address/City`percorso del campo . In un filtro, questo si riferisce a una singola città per il documento corrente, ad esempio "San Francisco". Al contrario, `Rooms/Type` `Type` si riferisce al sotto-campo per molte stanze (come "standard" per la prima stanza, "deluxe" per la seconda stanza e così via). Poiché `Rooms/Type` non fa riferimento a una singola `Type` *istanza* del sottocampo , non può essere utilizzato direttamente in un filtro. Al contrario, per filtrare in base al tipo di camera, è necessario usare [un'espressione lambda](search-query-odata-collection-operators.md) con una variabile di intervallo, in questo modo:Instead, to filter on room type, you would use a lambda expression with a range variable, like this:

    Rooms/any(room: room/Type eq 'deluxe')

In questo esempio, `room` la variabile `room/Type` di intervallo viene visualizzata nel percorso del campo. In questo `room/Type` modo, si riferisce al tipo di stanza corrente nel documento corrente. Si tratta di una `Type` singola istanza del sottocampo, pertanto può essere utilizzato direttamente nel filtro.

### <a name="using-field-paths"></a>Utilizzo dei percorsi di campo

I percorsi dei campi vengono usati in molti parametri delle [API REST di Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/)di Azure. Nella tabella seguente sono elencate tutte le posizioni in cui possono essere utilizzate, oltre a eventuali restrizioni sul loro utilizzo:

| API | Nome parametro | Restrizioni |
| --- | --- | --- |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `suggesters/sourceFields` | nessuno |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/text/weights` | Può fare riferimento solo ai campi **ricercabili** |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/functions/fieldName` | Può fare riferimento solo acampi **filtrabili** |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`quando `queryType` è`full` | Può fare riferimento solo ai campi **ricercabili** |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Può fare riferimento solo ai campi **facetable** |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Può fare riferimento solo ai campi **ricercabili** |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Può fare riferimento solo ai campi **ricercabili** |
| [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Può fare riferimento solo a campi che fanno parte di un [suggerimento](index-add-suggesters.md) |
| [Cerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [Completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Può fare riferimento solo acampi **filtrabili** |
| [Cerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Può fare riferimento solo ai campi **ordinabili** |
| [Cerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [Ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Può fare riferimento solo ai campi **recuperabili** |

## <a name="constants"></a>Costanti

Le costanti in OData sono valori letterali di un determinato tipo [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Vedere [Tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) per un elenco dei tipi supportati in Ricerca cognitiva di Azure.See Supported data types for a list of supported types in Azure Cognitive Search. Le costanti dei tipi di raccolta non sono supportate.

The following table shows examples of constants for each of the data types supported by Azure Cognitive Search:

| Tipo di dati | Costanti di esempio |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Escquandoling caratteri speciali nelle costanti stringa

Le costanti stringa in OData sono delimitate da virgolette singole. Se è necessario costruire una query con una costante di stringa che potrebbe contenere virgolette singole, è possibile eseguire l'escape delle virgolette incorporate raddoppiandole.

Ad esempio, una frase con un apostrofo non formattato come "Auto di `'Alice''s car'`Alice" verrebbe rappresentata in OData come costante di stringa .

> [!IMPORTANT]
> Quando si creano filtri a livello di codice, è importante ricordare di eseguire l'escape delle costanti di stringa provenienti dall'input dell'utente. Questo per mitigare la possibilità di attacchi di [iniezione](https://wikipedia.org/wiki/SQL_injection), soprattutto quando si utilizzano filtri per implementare [la limitazione per](search-security-trimming-for-azure-search.md)motivi di sicurezza.

### <a name="constants-syntax"></a>Sintassi delle costanti

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per la maggior parte delle costanti illustrate nella tabella precedente. La grammatica per i tipi geospaziali è disponibile nelle [funzioni geospaziali OData in Ricerca cognitiva](search-query-odata-geo-spatial-functions.md)di Azure.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
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
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

## <a name="building-expressions-from-field-paths-and-constants"></a>Compilazione di espressioni da percorsi di campo e costanti

I percorsi di campo e le costanti sono la parte più semplice di un'espressione OData, ma sono già espressioni complete. Ineffetti, il parametro **$select** in Ricerca cognitiva di Azure non è altro che un elenco separato da virgole di percorsi di campo e **$orderby** non è molto più complicato di **$select**. Se si dispone di un `Edm.Boolean` campo di tipo nell'indice, è anche possibile scrivere un filtro che non è altro che il percorso di tale campo. Le costanti `true` `false` e sono ugualmente filtri validi.

Tuttavia, nella maggior parte dei casi sono necessarie espressioni più complesse che fanno riferimento a più di un campo e costante. Queste espressioni vengono create in modi diversi a seconda del parametro.

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per i parametri **$filter**, **$orderby**e **$select** . Queste sono costituite da espressioni più semplici che fanno riferimento a percorsi di campo e costanti:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

I parametri **$orderby** e **$select** sono entrambi elenchi delimitati da virgole di espressioni più semplici. Il **parametro $filter** è un'espressione booleana composta da sottoespressioni più semplici. Queste sottoespressioni vengono combinate utilizzando operatori logici quali [ `and`, `or`e , `not` ](search-query-odata-logical-operators.md)gli operatori di confronto quali [ `eq`, `lt`, `gt`e così via](search-query-odata-comparison-operators.md), e gli operatori di insieme, ad [ `any` `all` ](search-query-odata-collection-operators.md)esempio e .

I parametri **$filter**, **$orderby**e **$select** vengono esaminati in modo più dettagliato negli articoli seguenti:

- [Sintassi di $filter Di Data in Ricerca cognitiva di AzureOData $filter syntax in Azure Cognitive Search](search-query-odata-filter.md)
- [OData $orderby syntax in Azure Cognitive Search](search-query-odata-orderby.md)
- [OData $select syntax in Azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Vedere anche  

- [Esplorazione in base a facet in Ricerca cognitiva di AzureFaceted navigation in Azure Cognitive Search](search-faceted-navigation.md)
- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Ricerca cognitiva di AzureSimple query syntax in Azure Cognitive Search](query-simple-syntax.md)
