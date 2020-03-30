---
title: Risoluzione dei problemi relativi ai filtri di raccolta ODataTroubleshooting OData collection filters
titleSuffix: Azure Cognitive Search
description: Informazioni sugli approcci per la risoluzione degli errori di filtro della raccolta OData nelle query di Ricerca cognitiva di Azure.Learn approaches for resolving OData collection filter errors in Azure Cognitive Search queries.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113088"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Risoluzione dei problemi relativi ai filtri di raccolta OData in Ricerca cognitiva di AzureTroubleshooting OData collection filters in Azure Cognitive Search

Per [filtrare](query-odata-filter-orderby-syntax.md) i campi di raccolta in Ricerca cognitiva di Azure, è possibile usare gli [ `any` operatori `all` e](search-query-odata-collection-operators.md) insieme alle espressioni **lambda**. Un'espressione lambda è un sottofiltro che viene applicato a ogni elemento di una raccolta.

Non tutte le funzionalità delle espressioni di filtro sono disponibili all'interno di un'espressione lambda. Le funzionalità disponibili variano a seconda del tipo di dati del campo di raccolta che si desidera filtrare. Ciò può causare un errore se si tenta di utilizzare una funzionalità in un'espressione lambda che non è supportata in tale contesto. Se si verificano tali errori durante il tentativo di scrivere un filtro complesso sui campi di raccolta, questo articolo consente di risolvere il problema.

## <a name="common-collection-filter-errors"></a>Errori comuni del filtro di raccolta

Nella tabella seguente sono elencati gli errori che possono verificarsi quando si tenta di eseguire un filtro di raccolta. Questi errori si verificano quando si usa una funzionalità di espressioni di filtro non supportata all'interno di un'espressione lambda. Ogni errore fornisce alcune indicazioni su come è possibile riscrivere il filtro per evitare l'errore. La tabella include anche un collegamento alla sezione pertinente di questo articolo che fornisce ulteriori informazioni su come evitare tale errore.

| Messaggio di errore | Situazione | Per ulteriori informazioni, vedere |
| --- | --- | --- |
| La funzione 'ismatch' non ha parametri associati alla variabile di intervallo 's'. Solo i riferimenti ai campi associati sono supportati all'interno di espressioni lambda ('any' o 'all'). Modificare il filtro in modo che la funzione 'ismatch' si esterno all'espressione lambda e riprovare. | Uso `search.ismatch` `search.ismatchscoring` o all'interno di un'espressione lambdaUsing or inside a lambda expression | [Regole per filtrare raccolte complesse](#bkmk_complex) |
| Espressione lambda non valida. Trovato un test per l'uguaglianza o la disuguaglianza in cui era previsto l'opposto in un'espressione lambda che scorre un campo di tipo Collection(Edm.String). Per 'any', si prega di utilizzare espressioni nel formato 'x eq y' o 'search.in(...)'. Per 'all', si prega di utilizzare espressioni nel formato 'x ne y', 'non (x eq y)', o 'not search.in(...)'. | Filtraggio in base a un campo di tipo`Collection(Edm.String)` | [Regole per filtrare le raccolte di stringheRules for filtering string collections](#bkmk_strings) |
| Espressione lambda non valida. Trovata una forma non supportata di espressione booleana complessa. Per 'any', si prega di utilizzare espressioni che sono 'OR di ANDs', noto anche come Disjunctive Normal Form. Ad esempio: '(a e b) o (c e d)' dove a, b, c e d sono sottoespressioni di confronto o di uguaglianza. Per 'all', si prega di utilizzare espressioni che sono 'ANDs di OR', noto anche come Conjunctive Normal Form. Ad esempio: '(a o b) e (c o d)' dove a, b, c e d sono sottoespressioni di confronto o disuguaglianza. Esempi di espressioni di confronto: 'x gt 5', 'x le 2'. Esempio di espressione di uguaglianza: 'x eq 5'. Esempio di espressione di disuguaglianza: 'x ne 5'. | Filtraggio in `Collection(Edm.DateTimeOffset)`base `Collection(Edm.Double)` `Collection(Edm.Int32)`a campi di tipo , , o`Collection(Edm.Int64)` | [Regole per filtrare raccolte comparabili](#bkmk_comparables) |
| Espressione lambda non valida. Trovato un uso non supportato di geo.distance() o geo.intersects() in un'espressione lambda che scorre un campo di tipo Collection(Edm.GeographyPoint). Per 'any', assicurarsi di confrontare geo.distance() utilizzando gli operatori 'lt' o 'le' e assicurarsi che qualsiasi utilizzo di geo.intersects() non sia negato. Per 'all', assicuratevi di confrontare geo.distance() utilizzando gli operatori 'gt' o 'ge' e assicuratevi che qualsiasi utilizzo di geo.intersects() sia negato. | Filtraggio in base a un campo di tipo`Collection(Edm.GeographyPoint)` | [Regole per filtrare le raccolte GeographyPointRules for filtering GeographyPoint collections](#bkmk_geopoints) |
| Espressione lambda non valida. Espressioni booleane complesse non sono supportate nelle espressioni lambda che scorrono i campi di tipo Collection(Edm.GeographyPoint). Per 'qualsiasi', si prega di unire sottoespressioni con 'o'; 'e' non è supportato. Per 'all', unire sottoespressioni con 'and'; 'o' non è supportato. | Filtraggio su `Collection(Edm.String)` campi di tipo o`Collection(Edm.GeographyPoint)` | [Regole per filtrare le raccolte di stringheRules for filtering string collections](#bkmk_strings) <br/><br/> [Regole per filtrare le raccolte GeographyPointRules for filtering GeographyPoint collections](#bkmk_geopoints) |
| Espressione lambda non valida. Trovato un operatore di confronto (uno dei valori 'lt', 'le', 'gt' o 'ge'). Nelle espressioni lambda sono consentiti solo operatori di uguaglianza che scorrono i campi di tipo Collection(Edm.String). Per 'any', si prega di utilizzare espressioni del modulo 'x eq y'. Per 'all', si prega di utilizzare espressioni nel formato 'x ne y' o 'not (x eq y)'. | Filtraggio in base a un campo di tipo`Collection(Edm.String)` | [Regole per filtrare le raccolte di stringheRules for filtering string collections](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Come scrivere filtri di raccolta validi

Le regole per la scrittura di filtri di raccolta validi sono diverse per ogni tipo di dati. Nelle sezioni seguenti vengono descritte le regole mostrando esempi di quali funzionalità di filtro sono supportate e quali no:

- [Regole per filtrare le raccolte di stringheRules for filtering string collections](#bkmk_strings)
- [Regole per filtrare le raccolte booleaneRules for filtering Boolean collections](#bkmk_bools)
- [Regole per filtrare le raccolte GeographyPointRules for filtering GeographyPoint collections](#bkmk_geopoints)
- [Regole per filtrare raccolte comparabili](#bkmk_comparables)
- [Regole per filtrare raccolte complesse](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regole per filtrare le raccolte di stringheRules for filtering string collections

All'interno delle espressioni lambda per le raccolte `eq` di `ne`stringhe, gli unici operatori di confronto che possono essere utilizzati sono e .

> [!NOTE]
> Ricerca cognitiva di `lt` / `le` / `gt` / `ge` Azure non supporta gli operatori per le stringhe, all'interno o all'esterno di un'espressione lambda.

Il corpo `any` di un può solo verificare `all` l'uguaglianza, mentre il corpo di un può solo verificare la disuguaglianza.

È anche possibile combinare più `or` espressioni tramite `any`nel corpo `and` di un `all`oggetto , e tramite nel corpo di un oggetto . Poiché `search.in` la funzione equivale alla `or`combinazione di controlli di uguaglianza `any`con , è consentita anche nel corpo di un oggetto . Al contrario, `not search.in` è consentito `all`nel corpo di un file .

Ad esempio, queste espressioni sono consentite:For example, these expressions are allowed:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

mentre queste espressioni non sono consentite:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regole per filtrare le raccolte booleaneRules for filtering Boolean collections

Il `Edm.Boolean` tipo supporta `eq` solo `ne` gli operatori e . Come tale, non ha molto senso consentire la combinazione di tali `and` / `or` clausole che controllano la stessa variabile di intervallo con poiché ciò porterebbe sempre a tautologie o contraddizioni.

Di seguito sono riportati alcuni esempi di filtri sulle raccolte booleane consentiti:Here are some examples of filters on Boolean collections that are allowed:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

A differenza delle raccolte di stringhe, le raccolte booleane non hanno limiti su quale operatore può essere utilizzato in quale tipo di espressione lambda. Entrambi `eq` `ne` e possono essere utilizzati nel corpo di `any` o `all`.

Espressioni come le seguenti non sono consentite per le raccolte booleane:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regole per filtrare le raccolte GeographyPointRules for filtering GeographyPoint collections

I valori `Edm.GeographyPoint` di tipo in una raccolta non possono essere confrontati direttamente tra loro. Al contrario, devono essere `geo.distance` utilizzati come parametri per le funzioni e `geo.intersects` . La `geo.distance` funzione a sua volta deve essere confrontata `lt` `le`con `gt`un `ge`valore di distanza utilizzando uno degli operatori di confronto , , , o . Queste regole si applicano anche ai campi Edm.GeographyPoint non di raccolta.

Come le `Edm.GeographyPoint` raccolte di stringhe, le raccolte hanno alcune regole per il modo in cui le funzioni geospaziali possono essere usate e combinate nei diversi tipi di espressioni lambda:As string collections, collections have some rules for how the geo-spatial functions can be used and combined in the different types of lambda expressions:

- Gli operatori di confronto `geo.distance` che è possibile usare con la funzione dipendono dal tipo di espressione lambda. Per `any`, è `lt` possibile `le`utilizzare solo o . Per `all`, è `gt` possibile `ge`utilizzare solo o . È possibile negare `geo.distance`le espressioni che coinvolgono ,`geo.distance(...) lt x` ma `not (geo.distance(...) ge x)` `geo.distance(...) le x` sarà `not (geo.distance(...) gt x)`necessario modificare l'operatore di confronto ( diventa e diventa ).
- Nel corpo di `all`un `geo.intersects` oggetto , la funzione deve essere negata. Al contrario, nel corpo `any`di `geo.intersects` un oggetto , la funzione non deve essere negata.
- Nel corpo di `any`un oggetto , le `or`espressioni geospaziali possono essere combinate utilizzando . Nel corpo di `all`un oggetto , `and`tali espressioni possono essere combinate utilizzando .

Le limitazioni di cui sopra esistono per motivi simili come la limitazione di uguaglianza/disuguaglianza per le raccolte di stringhe. Per informazioni più approfondite, vedere Informazioni sui filtri di [raccolta OData in Ricerca cognitiva](search-query-understand-collection-filters.md) di Azure per informazioni più approfondite su questi motivi.

Di seguito sono riportati alcuni esempi di filtri per le raccolte consentiti:Here are some examples of filters on `Edm.GeographyPoint` collections that are allowed:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Espressioni come le seguenti non `Edm.GeographyPoint` sono consentite per le raccolte:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regole per filtrare raccolte comparabili

Questa sezione si applica a tutti i tipi di dati seguenti:This section applies to all the following data types:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Tipi quali `Edm.Int32` `Edm.DateTimeOffset` e supportano tutti e `eq` `ne`sei `lt` `le`gli `gt`operatori `ge`di confronto: , , , , , e . Le espressioni lambda su raccolte di questi tipi possono contenere espressioni semplici usando uno di questi operatori. Questo vale `any` sia `all`per entrambi che per . Ad esempio, questi filtri sono consentiti:For example, these filters are allowed:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Tuttavia, esistono limitazioni su come tali espressioni di confronto possono essere combinate in espressioni più complesse all'interno di un'espressione lambda:However, there are limitations on how such comparison expressions can be combined into more complex expressions inside a lambda expression:

- Regole `any`per :
  - Le espressioni di disuguaglianza semplici non possono essere utilmente combinate con altre espressioni. Ad esempio, questa espressione è consentita:For example, this expression is allowed:
    - `ratings/any(r: r ne 5)`

    ma questa espressione non lo è:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e mentre questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Semplici espressioni `eq`di `lt` `le`confronto `gt`che `ge` coinvolgono `and` / `or`, , , , o possono essere combinate con . Ad esempio:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Le espressioni `and` di confronto combinate con `or`(congiunzioni) possono essere ulteriormente combinate utilizzando . Questo modulo è noto nella logica booleana come "[Disgiuntive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Ad esempio:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regole `all`per :
  - Le espressioni di uguaglianza semplici non possono essere utilmente combinate con altre espressioni. Ad esempio, questa espressione è consentita:For example, this expression is allowed:
    - `ratings/all(r: r eq 5)`

    ma questa espressione non lo è:
    - `ratings/all(r: r eq 5 or r le 2)`

    e mentre questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Semplici espressioni `ne`di `lt` `le`confronto `gt`che `ge` coinvolgono `and` / `or`, , , , o possono essere combinate con . Ad esempio:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Le espressioni `or` di confronto combinate con (disgiunzioni) possono essere ulteriormente combinate utilizzando `and`. Questa forma è nota nella logica booleana come "[Conjunctive Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Ad esempio:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regole per filtrare raccolte complesse

Le espressioni lambda su raccolte complesse supportano una sintassi molto più flessibile rispetto alle espressioni lambda rispetto alle raccolte di tipi primitivi. È possibile usare qualsiasi costrutto di filtro all'interno di un'espressione lambda che è possibile usare all'esterno di una, con solo due eccezioni.

In primo `search.ismatch` luogo, le funzioni e `search.ismatchscoring` non sono supportati all'interno di espressioni lambda. Per altre informazioni, vedere [Informazioni sui filtri di raccolta OData in Ricerca cognitiva](search-query-understand-collection-filters.md)di Azure.For more information, see Understanding OData collection filters in Azure Cognitive Search .

In secondo luogo, non è consentito fare riferimento a campi *non associati* alla variabile di intervallo (le cosiddette *variabili libere*). Si considerino, ad esempio, le due espressioni di filtro OData equivalenti seguenti:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

La prima espressione sarà consentita, mentre la `details/margin` seconda forma verrà rifiutata perché non è associata alla variabile `s`di intervallo .

Questa regola si estende anche alle espressioni con variabili associate in un ambito esterno. Tali variabili sono libere rispetto all'ambito in cui appaiono. Ad esempio, la prima espressione è consentita, mentre `s/name` la seconda espressione equivalente non è `a`consentita perché è libera rispetto all'ambito della variabile di intervallo :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Questa limitazione non dovrebbe essere un problema nella pratica poiché è sempre possibile costruire filtri in modo che le espressioni lambda contengano solo variabili associate.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Foglio trucco per le regole di filtro di raccolta

Nella tabella seguente vengono riepilogate le regole per la creazione di filtri validi per ogni tipo di dati della raccolta.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Per esempi su come costruire filtri validi per ogni caso, vedere [Come scrivere filtri di raccolta validi.](#bkmk_examples)

Se si scrivono spesso filtri e comprendere le regole dei primi principi non è sufficiente memorizzarli, vedere Informazioni sui filtri di [raccolta OData in Ricerca cognitiva di Azure](search-query-understand-collection-filters.md).If you write filters often, and understanding the rules from first principles would help you more than just memorizing them, see Understanding OData collection filters in Azure Cognitive Search .

## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sui filtri della raccolta OData in Ricerca cognitiva di AzureUnderstanding OData collection filters in Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
