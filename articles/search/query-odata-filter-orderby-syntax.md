---
title: Panoramica del linguaggio di OData - ricerca di Azure
description: Panoramica del linguaggio di OData per i filtri, selezionare e order by per le query di ricerca di Azure.
ms.date: 06/13/2019
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063696"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Panoramica del linguaggio OData `$filter`, `$orderby`, e `$select` in ricerca di Azure

Ricerca di Azure supporta un subset della sintassi dell'espressione OData per **$filter**, **$orderby**, e **$select** espressioni. Le espressioni filtro vengono valutate durante l'analisi della query, vincolando la ricerca a campi specifici o aggiungendo criteri di corrispondenza durante le analisi dell'indice. Le espressioni Order by vengono applicate come passaggio di post-elaborazione su un set di risultati per ordinare i documenti restituiti. Le espressioni selezionate determinano quali campi di documento sono inclusi nel set di risultati. La sintassi di tali espressioni è diversa dal [semplice](query-simple-syntax.md) o [completo](query-lucene-syntax.md) sintassi che viene utilizzata nella query il **ricerca** parametro, anche se è disponibile la sintassi per alcuni aspetti comuni riferimento ai campi.

Questo articolo fornisce una panoramica del linguaggio delle espressioni OData utilizzato nei filtri, order by e le espressioni select. Viene presentato il linguaggio "bottom-up", a partire da elementi più semplici e la compilazione su di essi. La sintassi di primo livello per ogni parametro è descritta in un articolo separato:

- [sintassi $filter](search-query-odata-filter.md)
- [sintassi $orderby](search-query-odata-orderby.md)
- [sintassi $select](search-query-odata-select.md)

Intervallo di espressioni OData da semplici a molto complesse, ma tutti condividono elementi comuni. Le parti più elementare di un'espressione di OData in ricerca di Azure sono:

- **Campo percorsi**, che fanno riferimento ai campi specifici dell'indice.
- **Costanti**, che sono valori letterali di un determinato tipo di dati.

> [!NOTE]
> Terminologia utilizzata in ricerca di Azure è diverso dal [standard di OData](https://www.odata.org/documentation/) in diversi modi. Quelle che chiamiamo un **campo** in ricerca di Azure viene chiamato un **proprietà** in OData e analogamente delle **percorso campo** rispetto **percorso proprietà**. Un' **indice** contenente **documenti** in ricerca di Azure viene definito in termini più generali in OData come un' **set di entità** contenente **entità**. In questo riferimento viene usata la terminologia di ricerca di Azure.

## <a name="field-paths"></a>Percorsi di campo

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica di percorsi di campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

È composto da un percorso del campo di uno o più **identificatori** separati da barre. Ogni identificatore è una sequenza di caratteri che deve iniziare con una lettera ASCII o un carattere di sottolineatura e contenere solo lettere ASCII, cifre o caratteri di sottolineatura. Le lettere possono essere superiore o lettere minuscole.

Un identificatore può fare riferimento al nome di un campo o a un **variabile di intervallo** nel contesto di un [espressione di raccolta](search-query-odata-collection-operators.md) (`any` o `all`) in un filtro. Una variabile di intervallo è simile a una variabile di ciclo che rappresenta l'elemento corrente della raccolta. Per le raccolte complesse, questa variabile rappresenta un oggetto, motivo per cui è possibile usare i percorsi di campo per fare riferimento a campi secondari della variabile. Questo comportamento è analogo per la notazione del punto in molti linguaggi di programmazione.

Nella tabella seguente sono riportati alcuni esempi di percorsi di campo:

| Percorso campo | Descrizione |
| --- | --- |
| `HotelName` | Fa riferimento a un campo di primo livello dell'indice |
| `Address/City` | Si intende il `City` il sottocampo di un campo complesso in corrispondenza dell'indice. `Address` JE typu `Edm.ComplexType` in questo esempio |
| `Rooms/Type` | Si intende il `Type` il sottocampo di un campo di raccolta complessa in corrispondenza dell'indice. `Rooms` JE typu `Collection(Edm.ComplexType)` in questo esempio |
| `Stores/Address/Country` | Si intende il `Country` il sottocampo del `Address` il sottocampo di un campo di raccolta complessa in corrispondenza dell'indice. `Stores` JE typu `Collection(Edm.ComplexType)` e `Address` JE typu `Edm.ComplexType` in questo esempio |
| `room/Type` | Si intende il `Type` il sottocampo del `room` variabile di intervallo, ad esempio nell'espressione di filtro `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Fa riferimento al `Country` il sottocampo del `Address` il sottocampo del `store` variabile di intervallo, ad esempio nell'espressione di filtro `Stores/any(store: store/Address/Country eq 'Canada')` |

Il significato di un percorso del campo è diverso a seconda del contesto. Nei filtri di un percorso campo fa riferimento al valore di una *singola istanza* di un campo nel documento corrente. In altri contesti, ad esempio **$orderby**, **$select**, o nella [con campo di ricerca nella sintassi Lucene full](query-lucene-syntax.md#bkmk_fields), un percorso campo fa riferimento al campo stesso. Questa differenza comporta alcune conseguenze per l'utilizzo di percorsi di campo nei filtri.

Prendere in considerazione il percorso del campo `Address/City`. In un filtro, si riferisce a una città singola per il documento corrente, ad esempio "Livorno". Al contrario, `Rooms/Type` si intende il `Type` il sottocampo per molti sale (ad esempio, "standard" per il primo spazio, "deluxe" per il secondo chat e così via). Poiché `Rooms/Type` non fa riferimento a un *istanza singola* del campo secondarie `Type`, non può essere usato direttamente in un filtro. In alternativa, per filtrare in base al tipo di camera, si utilizzerebbe un [espressione lambda](search-query-odata-collection-operators.md) con una variabile di intervallo, simile al seguente:

    Rooms/any(room: room/Type eq 'deluxe')

In questo esempio, la variabile di intervallo `room` viene visualizzato nei `room/Type` percorso del campo. In questo modo, `room/Type` fa riferimento al tipo della chat room corrente nel documento corrente. Si tratta di una singola istanza del `Type` sottocampo, in modo che può essere utilizzato direttamente nel filtro.

### <a name="using-field-paths"></a>Uso di percorsi di campo

Vengono usati in numerosi parametri dei percorsi di campo la [API di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Nella tabella seguente sono elencate tutte le posizioni in cui possono essere utilizzati, oltre a eventuali restrizioni sul loro utilizzo:

| API | Nome parametro | Restrizioni |
| --- | --- | --- |
| [Creare](https://docs.microsoft.com/rest/api/searchservice/create-index) oppure [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `suggesters/sourceFields` | Nessuna |
| [Creare](https://docs.microsoft.com/rest/api/searchservice/create-index) oppure [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/text/weights` | Può fare riferimento solo a **ricercabili** campi |
| [Creare](https://docs.microsoft.com/rest/api/searchservice/create-index) oppure [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/functions/fieldName` | Può fare riferimento solo a **filtrabile** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` Quando si `queryType` è `full` | Può fare riferimento solo a **ricercabili** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Può fare riferimento solo a **facetable** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Può fare riferimento solo a **ricercabili** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Può fare riferimento solo a **ricercabili** campi |
| [Suggerire](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Può fare riferimento solo a campi che fanno parte di un [dello strumento suggerimenti](index-add-suggesters.md) |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [suggerire](https://docs.microsoft.com/rest/api/searchservice/suggestions), e [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Può fare riferimento solo a **filtrabile** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [suggerire](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Può fare riferimento solo a **ordinabile** campi |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [suggerire](https://docs.microsoft.com/rest/api/searchservice/suggestions), e [Lookup](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Può fare riferimento solo a **recuperabili** campi |

## <a name="constants"></a>Costanti

Le costanti in OData sono valori letterali di un determinato [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) tipo (EDM). Visualizzare [tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) per un elenco dei tipi supportati in ricerca di Azure. Le costanti dei tipi di raccolta non sono supportate.

Nella tabella seguente vengono illustrati esempi di costanti per ognuno dei tipi di dati supportati da ricerca di Azure:

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

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per la maggior parte delle costanti riportate nella tabella precedente. La grammatica per i tipi di geo-spaziali è reperibile nel [OData funzioni geospaziali in ricerca di Azure](search-query-odata-geo-spatial-functions.md).

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

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Creazione di espressioni di costanti e i percorsi di campo

Le costanti e i percorsi di campo sono la parte più importante di un'espressione di OData, ma sono già espressioni complete. In effetti, il **$select** parametro in ricerca di Azure non è altro che un elenco delimitato da virgole di percorsi di campo, e **$orderby** non è molto più complicato **$select**. Se si dispone di un campo di tipo `Edm.Boolean` nell'indice, è anche possibile scrivere un filtro che non è altro che il percorso di quel campo. Le costanti `true` e `false` sono allo stesso modo filtri validi.

Tuttavia, la maggior parte dei casi sarà necessario espressioni più complesse che fanno riferimento a più di un campo e costante. Queste espressioni vengono compilate in modi diversi a seconda del parametro.

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per i **$filter**, **$orderby**, e **$select** parametri. Queste si basano su espressioni più semplici che fanno riferimento a costanti e i percorsi di campo:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Il **$orderby** e **$select** i parametri sono entrambi gli elenchi delimitati da virgole di espressioni più semplici. Il **$filter** parametro è un'espressione booleana che è costituito da delle sottoespressioni più semplice. Le sottoespressioni vengono combinati usando gli operatori logici, ad esempio [ `and`, `or`, e `not` ](search-query-odata-logical-operators.md), gli operatori di confronto, ad esempio [ `eq`, `lt`, `gt`e così via](search-query-odata-comparison-operators.md)e gli operatori di raccolta, ad esempio [ `any` e `all` ](search-query-odata-collection-operators.md).

Il **$filter**, **$orderby**, e **$select** vengono presi in esame i parametri in modo più dettagliato negli articoli seguenti:

- [Sintassi di OData $filter in ricerca di Azure](search-query-odata-filter.md)
- [Sintassi di OData $orderby in ricerca di Azure](search-query-odata-orderby.md)
- [Sintassi di OData $select in ricerca di Azure](search-query-odata-select.md)

## <a name="see-also"></a>Vedere anche  

- [Navigazione sfaccettata nella ricerca di Azure](search-faceted-navigation.md)
- [Filtri in Ricerca di Azure](search-filters.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Ricerca di Azure](query-simple-syntax.md)
