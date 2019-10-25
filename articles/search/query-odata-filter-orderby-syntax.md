---
title: Panoramica del linguaggio OData
titleSuffix: Azure Cognitive Search
description: Panoramica del linguaggio OData per i filtri, Select e order by per le query di Azure ricerca cognitiva.
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
ms.openlocfilehash: e0db41098287ff011416932a0d44a1cb9f76127d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786167"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Panoramica del linguaggio OData per `$filter`, `$orderby`e `$select` in Azure ricerca cognitiva

Azure ricerca cognitiva supporta un subset della sintassi delle espressioni OData per le espressioni **$Filter**, **$OrderBy**e **$Select** . Le espressioni filtro vengono valutate durante l'analisi della query, vincolando la ricerca a campi specifici o aggiungendo criteri di corrispondenza durante le analisi dell'indice. Le espressioni order-by vengono applicate come passaggio di post-elaborazione su un set di risultati per ordinare i documenti restituiti. Le espressioni Select determinano i campi del documento inclusi nel set di risultati. La sintassi di queste espressioni è diversa dalla sintassi di query [semplice](query-simple-syntax.md) o [completa](query-lucene-syntax.md) utilizzata nel parametro di **ricerca** , anche se c'è una sovrapposizione nella sintassi per i campi di riferimento.

In questo articolo viene fornita una panoramica del linguaggio delle espressioni OData utilizzato nei filtri, in order-by e nelle espressioni Select. Il linguaggio viene presentato "dal basso verso l'alto", iniziando dagli elementi più semplici e creandoli. La sintassi di primo livello per ogni parametro è descritta in un articolo separato:

- [sintassi $filter](search-query-odata-filter.md)
- [sintassi $orderby](search-query-odata-orderby.md)
- [sintassi $select](search-query-odata-select.md)

Le espressioni OData variano da semplice a estremamente complesso, ma condividono tutti elementi comuni. Le parti più semplici di un'espressione OData in Azure ricerca cognitiva sono:

- **Percorsi dei campi**, che fanno riferimento a campi specifici dell'indice.
- **Costanti**, che sono valori letterali di un determinato tipo di dati.

> [!NOTE]
> La terminologia in Azure ricerca cognitiva differisce dallo [standard OData](https://www.odata.org/documentation/) in diversi modi. Ciò che chiamiamo un **campo** in Azure ricerca cognitiva è denominato **Proprietà** in OData e in modo analogo per il percorso del **campo** rispetto al **percorso della proprietà**. Un **Indice** contenente **documenti** in Azure ricerca cognitiva viene definito più in generale in OData come **set di entità** che contiene le **entità**. La terminologia del ricerca cognitiva di Azure viene usata in tutto questo riferimento.

## <a name="field-paths"></a>Percorsi dei campi

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica dei percorsi dei campi.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Un percorso di campo è composto da uno o più **identificatori** separati da barre. Ogni identificatore è una sequenza di caratteri che deve iniziare con una lettera ASCII o un carattere di sottolineatura e contenere solo lettere, cifre o caratteri di sottolineatura ASCII. Le lettere possono essere maiuscole o minuscole.

Un identificatore può fare riferimento al nome di un campo o a una variabile di **intervallo** nel contesto di un'espressione di [raccolta](search-query-odata-collection-operators.md) (`any` o `all`) in un filtro. Una variabile di intervallo è analoga a una variabile di ciclo che rappresenta l'elemento corrente della raccolta. Per le raccolte complesse, tale variabile rappresenta un oggetto, motivo per cui è possibile utilizzare i percorsi dei campi per fare riferimento a sottocampi della variabile. Questo è analogo alla notazione del punto in molti linguaggi di programmazione.

Nella tabella seguente sono riportati alcuni esempi di percorsi dei campi:

| Percorso campo | Description |
| --- | --- |
| `HotelName` | Fa riferimento a un campo di primo livello dell'indice |
| `Address/City` | Fa riferimento al sottocampo `City` di un campo complesso nell'indice; `Address` è di tipo `Edm.ComplexType` in questo esempio |
| `Rooms/Type` | Fa riferimento al sottocampo `Type` di un campo di raccolta complesso nell'indice; `Rooms` è di tipo `Collection(Edm.ComplexType)` in questo esempio |
| `Stores/Address/Country` | Fa riferimento al sottocampo `Country` del sottocampo `Address` di un campo di raccolta complesso nell'indice; `Stores` è di tipo `Collection(Edm.ComplexType)` e `Address` è di tipo `Edm.ComplexType` in questo esempio |
| `room/Type` | Fa riferimento al sottocampo `Type` della variabile di intervallo `room`, ad esempio nell'espressione di filtro `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Fa riferimento al sottocampo `Country` del sottocampo `Address` della variabile di intervallo `store`, ad esempio nell'espressione di filtro `Stores/any(store: store/Address/Country eq 'Canada')` |

Il significato di un percorso di campo varia a seconda del contesto. Nei filtri, il percorso di un campo fa riferimento al valore di una *singola istanza* di un campo nel documento corrente. In altri contesti, ad esempio **$OrderBy**, **$SELECT**o nella [ricerca sul campo nella sintassi Lucene completa](query-lucene-syntax.md#bkmk_fields), un percorso di campo fa riferimento al campo stesso. Questa differenza ha alcune conseguenze sull'uso dei percorsi dei campi nei filtri.

Si consideri il percorso campo `Address/City`. In un filtro si fa riferimento a una singola città per il documento corrente, ad esempio "San Francisco". Al contrario, `Rooms/Type` fa riferimento al sottocampo `Type` per molte stanze, ad esempio "standard" per la prima stanza, "Deluxe" per la seconda stanza e così via. Poiché `Rooms/Type` non fa riferimento a una *singola istanza* del campo secondario `Type`, non può essere usato direttamente in un filtro. Per filtrare il tipo di stanza, usare invece un' [espressione lambda](search-query-odata-collection-operators.md) con una variabile di intervallo, come indicato di seguito:

    Rooms/any(room: room/Type eq 'deluxe')

In questo esempio, la variabile di intervallo `room` viene visualizzata nel percorso di `room/Type` campo. In questo modo, `room/Type` si riferisce al tipo di spazio corrente nel documento corrente. Si tratta di una singola istanza del sottocampo `Type`, che può quindi essere utilizzata direttamente nel filtro.

### <a name="using-field-paths"></a>Uso dei percorsi dei campi

I percorsi dei campi vengono usati in molti parametri delle [API REST di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/). Nella tabella seguente sono elencate tutte le posizioni in cui è possibile utilizzare, oltre a eventuali restrizioni relative all'utilizzo:

| API SmartBear Ready! | Nome parametro | Restrizioni |
| --- | --- | --- |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `suggesters/sourceFields` | Nessuno |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/text/weights` | Può fare riferimento solo a campi **ricercabili** |
| [Crea](https://docs.microsoft.com/rest/api/searchservice/create-index) o [Aggiorna](https://docs.microsoft.com/rest/api/searchservice/update-index) indice | `scoringProfiles/functions/fieldName` | Può fare riferimento solo a campi **filtrabili** |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` quando `queryType` è `full` | Può fare riferimento solo a campi **ricercabili** |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Può fare riferimento solo a campi **facet** |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Può fare riferimento solo a campi **ricercabili** |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Può fare riferimento solo a campi **ricercabili** |
| [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Può fare riferimento solo a campi che fanno parte di un componente di [Suggerimento](index-add-suggesters.md) |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Può fare riferimento solo a campi **filtrabili** |
| [Cerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [Suggerisci](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Può fare riferimento solo a campi **ordinabili** |
| [Ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents), [suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Può fare riferimento solo a campi **recuperabili** |

## <a name="constants"></a>Costanti

Le costanti in OData sono valori letterali di un determinato tipo di [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Per un elenco dei tipi supportati in Azure ricerca cognitiva, vedere [tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) . Le costanti dei tipi di raccolta non sono supportate.

La tabella seguente illustra esempi di costanti per ognuno dei tipi di dati supportati da Azure ricerca cognitiva:

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

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per la maggior parte delle costanti mostrate nella tabella precedente. La grammatica per i tipi geospaziali si trova in [funzioni geospaziali OData in Azure ricerca cognitiva](search-query-odata-geo-spatial-functions.md).

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
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

## <a name="building-expressions-from-field-paths-and-constants"></a>Compilazione di espressioni da percorsi e costanti dei campi

Le costanti e i percorsi dei campi rappresentano la parte fondamentale di un'espressione OData, ma sono già espressioni complete. Infatti, il **$Select** parametro in Azure ricerca cognitiva non è altro che un elenco delimitato da virgole di percorsi di campo e **$OrderBy** non è molto più complicato di **$Select**. Se si dispone di un campo di tipo `Edm.Boolean` nell'indice, è anche possibile scrivere un filtro che non sia altro che il percorso di tale campo. Le costanti `true` e `false` sono anche filtri validi.

Tuttavia, nella maggior parte dei casi sono necessarie espressioni più complesse che fanno riferimento a più di un campo e una costante. Queste espressioni sono compilate in modi diversi a seconda del parametro.

Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per i parametri **$Filter**, **$OrderBy**e **$Select** . Queste sono costituite da espressioni più semplici che fanno riferimento a percorsi e costanti dei campi:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

I parametri **$OrderBy** e **$SELECT** sono elenchi delimitati da virgole di espressioni più semplici. Il parametro **$Filter** è un'espressione booleana composta da sottoespressioni più semplici. Queste sottoespressioni vengono combinate utilizzando operatori logici quali [`and`, `or`e `not`](search-query-odata-logical-operators.md), operatori di confronto quali [`eq`, `lt`, `gt`e così via](search-query-odata-comparison-operators.md)e operatori di raccolta come [`any` e `all`](search-query-odata-collection-operators.md).

I parametri **$Filter**, **$OrderBy**e **$Select** vengono esaminati in modo più dettagliato negli articoli seguenti:

- [Sintassi di $filter OData in Azure ricerca cognitiva](search-query-odata-filter.md)
- [Sintassi di $orderby OData in Azure ricerca cognitiva](search-query-odata-orderby.md)
- [Sintassi di $select OData in Azure ricerca cognitiva](search-query-odata-select.md)

## <a name="see-also"></a>Vedi anche  

- [Esplorazione in base a facet in Azure ricerca cognitiva](search-faceted-navigation.md)
- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintassi di query Lucene](query-lucene-syntax.md)
- [Sintassi di query semplice in Azure ricerca cognitiva](query-simple-syntax.md)
