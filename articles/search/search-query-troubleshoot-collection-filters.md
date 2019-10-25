---
title: Risoluzione dei problemi relativi ai filtri di raccolta OData
titleSuffix: Azure Cognitive Search
description: Risoluzione degli errori di filtro della raccolta OData in Azure ricerca cognitiva query.
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
ms.openlocfilehash: 0af2525a15618c6bfd9022b4388c547209ee957b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793180"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Risoluzione dei problemi relativi ai filtri di raccolta OData in Azure ricerca cognitiva

Per [filtrare](query-odata-filter-orderby-syntax.md) i campi di raccolta in ricerca cognitiva di Azure, è possibile usare gli [operatori`any` e `all`](search-query-odata-collection-operators.md) insieme alle **espressioni lambda**. Un'espressione lambda è un filtro secondario applicato a ogni elemento di una raccolta.

Non tutte le funzionalità delle espressioni di filtro sono disponibili all'interno di un'espressione lambda. Le funzionalità disponibili variano a seconda del tipo di dati del campo della raccolta che si desidera filtrare. Questo può generare un errore se si tenta di usare una funzionalità in un'espressione lambda non supportata in tale contesto. Se si verificano errori di questo tipo durante il tentativo di scrivere un filtro complesso sui campi della raccolta, questo articolo consentirà di risolvere il problema.

## <a name="common-collection-filter-errors"></a>Errori comuni di filtro della raccolta

Nella tabella seguente sono elencati gli errori che potrebbero verificarsi durante il tentativo di eseguire un filtro di raccolta. Questi errori si verificano quando si usa una funzionalità di espressioni di filtro che non è supportata in un'espressione lambda. Ogni errore fornisce alcune indicazioni su come è possibile riscrivere il filtro per evitare l'errore. La tabella include anche un collegamento alla sezione pertinente di questo articolo che fornisce ulteriori informazioni su come evitare tale errore.

| Messaggio di errore | Situazione | Per altre informazioni, vedere |
| --- | --- | --- |
| Alla funzione ' IsMatch ' non è associato alcun parametro alla variabile di intervallo ''. Solo i riferimenti ai campi associati sono supportati nelle espressioni lambda (' any ' o ' all'). Modificare il filtro in modo che la funzione ' IsMatch ' si trovi all'esterno dell'espressione lambda e riprovare. | Uso di `search.ismatch` o `search.ismatchscoring` all'interno di un'espressione lambda | [Regole per il filtro di raccolte complesse](#bkmk_complex) |
| Espressione lambda non valida. È stato trovato un test per verificarne l'uguaglianza o la disuguaglianza dove era previsto il contrario in un'espressione lambda che scorre un campo di tipo Collection (EDM. String). Per ' any ' usare espressioni nel formato ' x EQ y ' o ' search.in (...)'. Per ' all', usare espressioni nel formato ' x ne y ',' not (x EQ y)' o ' not search.in (...)'. | Applicazione di filtri a un campo di tipo `Collection(Edm.String)` | [Regole per filtrare le raccolte di stringhe](#bkmk_strings) |
| Espressione lambda non valida. È stata rilevata una forma non supportata di espressione booleana complessa. Per "any", usare espressioni che sono "ORs di i", noto anche come formato normale disgiuntiva. Ad esempio:' (a e b) o (c e d)' dove a, b, c e d sono sottoespressioni di confronto o uguaglianza. Per ' all', usare le espressioni che sono ' e di ORs ', note anche come congiuntiva Normal Form. Ad esempio:' (a o b) e (c o d)' dove a, b, c e d sono sottoespressioni di confronto o di disuguaglianza. Esempi di espressioni di confronto:' x gt 5',' x le 2'. Esempio di espressione di uguaglianza: "x EQ 5". Esempio di espressione di disuguaglianza: "x ne 5". | Filtro per campi di tipo `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`o `Collection(Edm.Int64)` | [Regole per filtrare le raccolte confrontabili](#bkmk_comparables) |
| Espressione lambda non valida. È stato rilevato un uso non supportato di geo. distance () o Geo. Intersects () in un'espressione lambda che esegue l'iterazione su un campo di tipo Collection (EDM. GeographyPoint). Per ' any ', assicurarsi di confrontare Geo. distance () usando gli operatori ' lt ' o ' le ' e assicurarsi che l'utilizzo di geo. Intersects () non venga negato. Per "tutti", assicurarsi di confrontare Geo. distance () usando gli operatori "gt" o "GE" e assicurarsi che l'utilizzo di geo. Intersects () venga negato. | Applicazione di filtri a un campo di tipo `Collection(Edm.GeographyPoint)` | [Regole per il filtro delle raccolte GeographyPoint](#bkmk_geopoints) |
| Espressione lambda non valida. Le espressioni booleane complesse non sono supportate nelle espressioni lambda che scorrono i campi di tipo Collection (EDM. GeographyPoint). Per ' any ', unire le sottoespressioni con ' or '; ' and ' non è supportato. Per ' all', unire le sottoespressioni con ' and '; ' or ' non è supportato. | Filtro per campi di tipo `Collection(Edm.String)` o `Collection(Edm.GeographyPoint)` | [Regole per filtrare le raccolte di stringhe](#bkmk_strings) <br/><br/> [Regole per il filtro delle raccolte GeographyPoint](#bkmk_geopoints) |
| Espressione lambda non valida. Trovato un operatore di confronto (uno tra' lt ',' le ',' gt ' o ' GE '). Nelle espressioni lambda sono consentiti solo operatori di uguaglianza che scorrono campi di tipo Collection (EDM. String). Per ' any ' usare espressioni nel formato ' x EQ y '. Per ' all', usare espressioni nel formato ' x ne y ' o ' not (x EQ y)'. | Applicazione di filtri a un campo di tipo `Collection(Edm.String)` | [Regole per filtrare le raccolte di stringhe](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Come scrivere filtri di raccolta validi

Le regole per la scrittura di filtri di raccolta validi sono diverse per ogni tipo di dati. Le sezioni seguenti descrivono le regole mostrando esempi di quali funzionalità di filtro sono supportate e quali non sono:

- [Regole per filtrare le raccolte di stringhe](#bkmk_strings)
- [Regole per il filtro delle raccolte booleane](#bkmk_bools)
- [Regole per il filtro delle raccolte GeographyPoint](#bkmk_geopoints)
- [Regole per filtrare le raccolte confrontabili](#bkmk_comparables)
- [Regole per il filtro di raccolte complesse](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regole per filtrare le raccolte di stringhe

All'interno delle espressioni lambda per le raccolte di stringhe, gli unici operatori di confronto che possono essere usati sono `eq` e `ne`.

> [!NOTE]
> Ricerca cognitiva di Azure non supporta il `lt`/`le`/`gt`gli operatori/per le stringhe, sia all'interno che all'esterno di un'espressione lambda.

Il corpo di un `any` può verificare l'uguaglianza solo se il corpo di un `all` può solo verificare la disuguaglianza.

È anche possibile combinare più espressioni tramite `or` nel corpo di un `any`e tramite `and` nel corpo di una `all`. Poiché la funzione `search.in` equivale a combinare i controlli di uguaglianza con `or`, è consentito anche nel corpo di un `any`. Viceversa, `not search.in` è consentito nel corpo di un `all`.

Ad esempio, queste espressioni sono consentite:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

le espressioni non sono consentite:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regole per il filtro delle raccolte booleane

Il tipo `Edm.Boolean` supporta solo gli operatori `eq` e `ne`. Di conseguenza, non è molto sensato consentire la combinazione di tali clausole che controllano la stessa variabile di intervallo con `and`/`or` poiché questo porterebbe sempre a tautologie o contraddizioni.

Di seguito sono riportati alcuni esempi di filtri sulle raccolte booleane consentite:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Diversamente dalle raccolte di stringhe, le raccolte booleane non hanno limiti per l'operatore che può essere usato nel tipo di espressione lambda. È possibile usare sia `eq` che `ne` nel corpo di `any` o `all`.

Non sono consentite espressioni come le seguenti per le raccolte booleane:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regole per il filtro delle raccolte GeographyPoint

I valori di tipo `Edm.GeographyPoint` in una raccolta non possono essere confrontati direttamente tra loro. Ma devono essere usati come parametri per le funzioni `geo.distance` e `geo.intersects`. La funzione `geo.distance` a sua volta deve essere confrontata con un valore di distanza utilizzando uno degli operatori di confronto `lt`, `le`, `gt`o `ge`. Queste regole si applicano anche ai campi EDM. GeographyPoint non di raccolta.

Analogamente alle raccolte di stringhe, le raccolte di `Edm.GeographyPoint` hanno alcune regole per il modo in cui le funzioni geospaziali possono essere utilizzate e combinate nei diversi tipi di espressioni lambda:

- Gli operatori di confronto che è possibile usare con la funzione `geo.distance` dipendono dal tipo di espressione lambda. Per `any`, è possibile utilizzare solo `lt` o `le`. Per `all`, è possibile utilizzare solo `gt` o `ge`. È possibile negare le espressioni che interessano `geo.distance`, ma è necessario modificare l'operatore di confronto (`geo.distance(...) lt x` diventa `not (geo.distance(...) ge x)` e `geo.distance(...) le x` diventa `not (geo.distance(...) gt x)`).
- Nel corpo di un `all`è necessario che la funzione `geo.intersects` venga negata. Viceversa, nel corpo di un `any`, la funzione `geo.intersects` non deve essere negata.
- Nel corpo di un `any`, le espressioni geospaziali possono essere combinate usando `or`. Nel corpo di un `all`, tali espressioni possono essere combinate utilizzando `and`.

Le limitazioni sopra riportate esistono per motivi analoghi come la limitazione di uguaglianza/disuguaglianza per le raccolte di stringhe. Per un'analisi più approfondita di questi motivi, vedere [informazioni sui filtri di raccolta OData in Azure ricerca cognitiva](search-query-understand-collection-filters.md) .

Di seguito sono riportati alcuni esempi di filtri sulle raccolte di `Edm.GeographyPoint` consentite:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Non sono consentite espressioni come le seguenti per le raccolte di `Edm.GeographyPoint`:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regole per filtrare le raccolte confrontabili

Questa sezione si applica a tutti i tipi di dati seguenti:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

I tipi come `Edm.Int32` e `Edm.DateTimeOffset` supportano tutti e sei gli operatori di confronto: `eq`, `ne`, `lt`, `le`, `gt`e `ge`. Le espressioni lambda sulle raccolte di questi tipi possono contenere espressioni semplici usando uno di questi operatori. Questa condizione si applica sia a `any` che a `all`. Ad esempio, questi filtri sono consentiti:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Esistono tuttavia alcune limitazioni relative al modo in cui tali espressioni di confronto possono essere combinate in espressioni più complesse all'interno di un'espressione lambda:

- Regole per `any`:
  - Le espressioni di disuguaglianza semplici non possono essere utilizzate in combinazione con altre espressioni. Questa espressione, ad esempio, è consentita:
    - `ratings/any(r: r ne 5)`

    ma questa espressione non è:
    - `ratings/any(r: r ne 5 and r gt 2)`

    mentre questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Le espressioni di confronto semplici che coinvolgono `eq`, `lt`, `le`, `gt`o `ge` possono essere combinate con `and`/`or`. ad esempio:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Le espressioni di confronto combinate con `and` (Conjunctions) possono essere combinate ulteriormente usando `or`. Questo form è noto in logica booleana come "[disgiuntiva Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). ad esempio:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regole per `all`:
  - Le espressioni di uguaglianza semplici non possono essere utilizzate in combinazione con altre espressioni. Questa espressione, ad esempio, è consentita:
    - `ratings/all(r: r eq 5)`

    ma questa espressione non è:
    - `ratings/all(r: r eq 5 or r le 2)`

    mentre questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Le espressioni di confronto semplici che coinvolgono `ne`, `lt`, `le`, `gt`o `ge` possono essere combinate con `and`/`or`. ad esempio:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Le espressioni di confronto combinate con `or` (disgiunzioni) possono essere combinate ulteriormente utilizzando `and`. Questo formato è noto in logica booleana come "[congiuntiva Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). ad esempio:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regole per il filtro di raccolte complesse

Le espressioni lambda sulle raccolte complesse supportano una sintassi molto più flessibile rispetto alle espressioni lambda sulle raccolte di tipi primitivi. È possibile utilizzare qualsiasi costrutto di filtro all'interno di un'espressione lambda che è possibile utilizzare al di fuori di uno, con solo due eccezioni.

In primo luogo, le funzioni `search.ismatch` e `search.ismatchscoring` non sono supportate nelle espressioni lambda. Per ulteriori informazioni, vedere informazioni sui [filtri di raccolta OData in Azure ricerca cognitiva](search-query-understand-collection-filters.md).

In secondo luogo, non è consentito fare riferimento a campi che non sono *associati* alla variabile di intervallo (le cosiddette *variabili gratuite*). Si considerino, ad esempio, le due espressioni di filtro OData equivalenti seguenti:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

La prima espressione sarà consentita, mentre il secondo form verrà rifiutato perché `details/margin` non è associato alla variabile di intervallo `s`.

Questa regola si estende anche a espressioni con variabili associate in un ambito esterno. Tali variabili sono gratuite rispetto all'ambito in cui vengono visualizzate. La prima espressione, ad esempio, è consentita, mentre la seconda espressione equivalente non è consentita perché `s/name` è gratuita rispetto all'ambito della variabile di intervallo `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Questa limitazione non dovrebbe essere un problema in pratica perché è sempre possibile creare filtri in modo che le espressioni lambda contengano solo variabili associate.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Foglio informativo per le regole di filtro della raccolta

Nella tabella seguente sono riepilogate le regole per la creazione di filtri validi per ogni tipo di dati della raccolta.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Per esempi relativi alla creazione di filtri validi per ogni case, vedere [come scrivere filtri di raccolta validi](#bkmk_examples).

Se si scrivono spesso i filtri e si comprendono le regole dei primi principi, non basta semplicemente memorizzarli, vedere [informazioni sui filtri di raccolta OData in Azure ricerca cognitiva](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sui filtri di raccolta OData in Azure ricerca cognitiva](search-query-understand-collection-filters.md)
- [Filtri in ricerca cognitiva di Azure](search-filters.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
