---
title: Risoluzione dei problemi di filtri di raccolta OData - ricerca di Azure
description: Risoluzione dei problemi relativi a errori di filtro OData insieme nelle query di ricerca di Azure.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079626"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Risoluzione dei problemi di filtri di raccolta OData in ricerca di Azure

Per [filtro](query-odata-filter-orderby-syntax.md) nei campi della raccolta in ricerca di Azure, è possibile utilizzare il [ `any` e `all` operatori](search-query-odata-collection-operators.md) assieme **espressioni lambda**. Un'espressione lambda è un filtro secondario che viene applicato a ogni elemento della raccolta.

Non tutte le funzionalità delle espressioni di filtro sono disponibile all'interno di un'espressione lambda. Quali funzionalità sono disponibili varia a seconda del tipo di dati del campo della raccolta che si desidera filtrare. Ciò può comportare un errore se si prova a usare una funzionalità in un'espressione lambda che non è supportata in tale contesto. Se tali errori potrebbero verificarsi durante il tentativo di scrivere un filtro complesso tramite i campi della raccolta, questo articolo consente di risolvere il problema.

## <a name="common-collection-filter-errors"></a>Errori di filtro comuni di raccolta

Nella tabella seguente sono elencati gli errori che possono verificarsi durante il tentativo di eseguire un filtro dalla raccolta. Questi errori si verificano quando si usa una funzionalità delle espressioni di filtro che non è supportata all'interno di un'espressione lambda. Ogni errore offre alcune indicazioni su come è possibile riscrivere il filtro per evitare l'errore. La tabella include anche un collegamento alla sezione rilevante di questo articolo che fornisce altre informazioni su come evitare tale errore.

| Messaggio di errore | Situazione | Per altre informazioni, vedere |
| --- | --- | --- |
| La funzione 'ismatch' non dispone di parametri associati alla variabile di intervallo '. Associato solo all'interno di espressioni lambda ('any' o 'tutti') sono supportati i riferimenti di campo. . Modificare il filtro in modo che la funzione 'ismatch' è fuori dall'espressione lambda e riprovare. | Usando `search.ismatch` o `search.ismatchscoring` all'interno di un'espressione lambda | [Regole per il filtro raccolte complesse](#bkmk_complex) |
| Espressione lambda non è valido. Trovato un test per verificarne l'uguaglianza o disuguaglianza mentre era previsto l'opposto in un'espressione lambda che esegue l'iterazione in un campo di tipo Collection. Per 'any', utilizzare le espressioni del form 'x eq y' o 'search.in(...)'. Per "all", usare le espressioni del modulo 'x ne y', 'non (x y eq)' o 'non search.in(...)'. | Il filtro su un campo di tipo `Collection(Edm.String)` | [Regole per il filtro delle raccolte di stringhe](#bkmk_strings) |
| Espressione lambda non è valido. Trovare un modulo non supportato delle espressioni booleane complesse. Per 'any', utilizzare le espressioni 'OR di and', noto anche come disgiuntiva a normale. Ad esempio: '(a and b) o (c e d)', b, c e d sono le sottoespressioni di confronto o verificarne l'uguaglianza. Per "all", usare le espressioni 'And di operatori OR', noto anche come congiunzione Form normale. Ad esempio: '(a or b) e (c o d)', b, c e d sono le sottoespressioni di confronto o la disuguaglianza. Esempi di espressioni di confronto: ' x gt 5', ' x le 2'. Esempio di un'espressione di uguaglianza: ' x eq 5'. Esempio di un'espressione di ineguaglianza: ' x ne 5'. | Filtrando i campi di tipo `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, o `Collection(Edm.Int64)` | [Regole per il filtro raccolte confrontabili](#bkmk_comparables) |
| Espressione lambda non è valido. Trovare un utilizzo non supportato di geo.distance() o geo.intersects() in un'espressione lambda che esegue l'iterazione in un campo di tipo Collection(Edm.GeographyPoint). Per 'any', assicurarsi che il confronto geo.distance() usando gli operatori 'lt' o 'le' e assicurarsi che qualsiasi utilizzo dei geo.intersects() non è negativo. Per "all", assicurarsi che il confronto geo.distance() usando gli operatori 'gt' o 'ge' e assicurarsi che qualsiasi utilizzo dei geo.intersects() è negativo. | Il filtro su un campo di tipo `Collection(Edm.GeographyPoint)` | [Regole per il filtro GeographyPoint raccolte](#bkmk_geopoints) |
| Espressione lambda non è valido. Espressioni booleane complesse non sono supportate nelle espressioni lambda che i campi di tipo Collection(Edm.GeographyPoint) l'iterazione. Per 'any', partecipa sottoespressioni con 'o'; 'e' non è supportato. Per 'tutti', partecipa sottoespressioni con 'e'; 'o' non è supportato. | Filtrando i campi di tipo `Collection(Edm.String)` o `Collection(Edm.GeographyPoint)` | [Regole per il filtro delle raccolte di stringhe](#bkmk_strings) <br/><br/> [Regole per il filtro GeographyPoint raccolte](#bkmk_geopoints) |
| Espressione lambda non è valido. Trovare un operatore di confronto (uno di 'lt', 'le', 'gt' o 'ge'). Solo gli operatori di uguaglianza sono consentiti nelle espressioni lambda che scorrono i campi di tipo Collection. Per 'any', utilizzare le espressioni nel formato 'x eq y'. Per "all", usare le espressioni di tipo 'x ne y' o 'non (x y eq)'. | Il filtro su un campo di tipo `Collection(Edm.String)` | [Regole per il filtro delle raccolte di stringhe](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Come scrivere i filtri di raccolta valida

Le regole per la scrittura di filtri di raccolta validi sono diverse per ogni tipo di dati. Le sezioni seguenti descrivono le regole tramite la visualizzazione degli esempi di filtro sono supportate le funzionalità e che non sono:

- [Regole per il filtro delle raccolte di stringhe](#bkmk_strings)
- [Regole per il filtro booleane raccolte](#bkmk_bools)
- [Regole per il filtro GeographyPoint raccolte](#bkmk_geopoints)
- [Regole per il filtro raccolte confrontabili](#bkmk_comparables)
- [Regole per il filtro raccolte complesse](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regole per il filtro delle raccolte di stringhe

All'interno di espressioni lambda per raccolte di stringhe, sono gli operatori di confronto unico che possono essere utilizzati `eq` e `ne`.

> [!NOTE]
> Ricerca di Azure non supporta il `lt` / `le` / `gt` / `ge` operatori per le stringhe, se all'interno o all'esterno di un'espressione lambda.

Il corpo di un' `any` solo possibile verificare l'uguaglianza durante il corpo di un `all` possibile testare solo per verificare la disuguaglianza.

È anche possibile combinare più espressioni tramite `or` nel corpo di un' `any`e tramite `and` nel corpo di un `all`. Poiché il `search.in` funzione è equivalente alla combinazione di controlli di uguaglianza con `or`, è inoltre consentito nel corpo di un `any`. Al contrario, `not search.in` è consentito nel corpo di un `all`.

Ad esempio, queste espressioni sono consentite:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

anche se queste espressioni non sono consentite:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regole per il filtro booleane raccolte

Il tipo `Edm.Boolean` supporta solo le `eq` e `ne` operatori. Di conseguenza, non ha molto senso per consentire la combinazione di tali clausole che consentono di controllare la stessa variabile di intervallo con `and` / `or` dal momento che causerebbe sempre tautologies o le contraddizioni.

Di seguito sono riportati alcuni esempi di filtri nelle raccolte booleane consentiti:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

A differenza delle raccolte di stringhe, booleane raccolte non sono previsti limiti in cui l'operatore può essere usato in quale tipo di espressione lambda. Entrambe `eq` e `ne` può essere usato nel corpo della `any` o `all`.

Per le raccolte booleane non sono consentite espressioni simili alla seguente:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regole per il filtro GeographyPoint raccolte

I valori di tipo `Edm.GeographyPoint` in una raccolta non può essere confrontati direttamente tra loro. Invece, devono essere usati come parametri per il `geo.distance` e `geo.intersects` funzioni. Il `geo.distance` funzione a sua volta deve essere confrontata con un valore di distanza usando uno degli operatori di confronto `lt`, `le`, `gt`, o `ge`. Queste regole si applicano anche ai campi di EDM. geographypoint diverso dalla raccolta.

Ad esempio raccolte di stringhe, `Edm.GeographyPoint` raccolte presentano alcune regole relative a come le funzioni geospaziali possono essere utilizzate e combinate in diversi tipi di espressioni lambda:

- Quale è possibile usare con gli operatori di confronto di `geo.distance` funzione dipende dal tipo di espressione lambda. Per la `any`, è possibile usare solo `lt` o `le`. Per la `all`, è possibile usare solo `gt` o `ge`. È possibile negare le espressioni che implica `geo.distance`, ma sarà necessario modificare l'operatore di confronto (`geo.distance(...) lt x` diventa `not (geo.distance(...) ge x)` e `geo.distance(...) le x` diventa `not (geo.distance(...) gt x)`).
- Nel corpo di un' `all`, il `geo.intersects` funzione deve essere negata. Al contrario, nel corpo di un' `any`, il `geo.intersects` funzione non deve essere negata.
- Nel corpo di un' `any`, è possibile combinare le espressioni di ricerca geospaziale usando `or`. Nel corpo di un' `all`, queste espressioni possono essere combinate utilizzando `and`.

Sono presenti le limitazioni precedente per motivi analoghi come la limitazione di uguaglianza/disuguaglianza in raccolte di stringhe. Visualizzare [filtri di raccolta di informazioni su OData in ricerca di Azure](search-query-understand-collection-filters.md) per un approfondimento su questi motivi.

Di seguito sono riportati alcuni esempi di filtri su `Edm.GeographyPoint` raccolte consentite:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Espressioni simili alla seguente non sono consentite per `Edm.GeographyPoint` raccolte:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regole per il filtro raccolte confrontabili

In questa sezione si applica a tutti i tipi di dati seguenti:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

I tipi, ad esempio `Edm.Int32` e `Edm.DateTimeOffset` supportano tutti i sei degli operatori di confronto: `eq`, `ne`, `lt`, `le`, `gt`, e `ge`. Le espressioni lambda nelle raccolte di questi tipi possono contenere espressioni semplici usando uno di questi operatori. Questo vale per entrambi `any` e `all`. Ad esempio, sono consentiti questi filtri:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Tuttavia, esistono limitazioni sul modo in cui tali espressioni di confronto possono essere combinate in espressioni più complesse all'interno di un'espressione lambda:

- Le regole per `any`:
  - Espressioni di disuguaglianza semplici non possono essere utili combinate con le altre espressioni. Ad esempio, questa espressione è consentita:
    - `ratings/any(r: r ne 5)`

    ma non è questa espressione:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e benché questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Espressioni di confronto semplice che coinvolgono `eq`, `lt`, `le`, `gt`, o `ge` può essere combinato con `and` / `or`. Ad esempio:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Espressioni di confronto associata `and` (congiunzioni) possono essere combinati ulteriormente utilizzando `or`. Questa modalità è noto in booleano per la logica come "[normale disgiuntiva](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Ad esempio:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Le regole per `all`:
  - Espressioni di uguaglianza semplici non possono essere utili combinate con le altre espressioni. Ad esempio, questa espressione è consentita:
    - `ratings/all(r: r eq 5)`

    ma non è questa espressione:
    - `ratings/all(r: r eq 5 or r le 2)`

    e benché questa espressione è consentita, non è utile perché le condizioni si sovrappongono:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Espressioni di confronto semplice che coinvolgono `ne`, `lt`, `le`, `gt`, o `ge` può essere combinato con `and` / `or`. Ad esempio:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Espressioni di confronto associata `or` (disgiunzioni) possono essere combinati ulteriormente utilizzando `and`. Questa modalità è noto in booleano per la logica come "[congiunzione Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Ad esempio:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regole per il filtro raccolte complesse

Le espressioni lambda nelle raccolte complesse supportano una sintassi molto più flessibile rispetto alle espressioni lambda nelle raccolte di tipi primitivi. È possibile usare qualsiasi costrutto di filtro all'interno di questo tipo di un'espressione lambda che è possibile usare quella esterna, con solo due eccezioni.

Per prima cosa, le funzioni `search.ismatch` e `search.ismatchscoring` non sono supportati all'interno di espressioni lambda. Per altre informazioni, vedere [filtri di raccolta di informazioni su OData in ricerca di Azure](search-query-understand-collection-filters.md).

In secondo luogo, che fanno riferimento a campi che non sono *associata* alla variabile di intervallo (le cosiddette *libero variabili*) non è consentito. Ad esempio, prendere in considerazione le seguenti due equivalente OData espressioni di filtro:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

La prima espressione potrà essere, mentre il secondo form verranno rifiutati in quanto `details/margin` non è associato alla variabile di intervallo `s`.

Questa regola si estende anche alle espressioni che sono variabili associate in un ambito esterno. Tali variabili sono gratuite per quanto riguarda l'ambito in cui vengono visualizzati. Ad esempio, la prima espressione è consentita, mentre l'espressione equivalente in secondo luogo non è consentita perché `s/name` gratuità per quanto riguarda l'ambito della variabile di intervallo `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Questa limitazione non deve essere un problema in pratica, perché è sempre possibile costruire i filtri in modo che le espressioni lambda contengono solo le variabili associate.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Riferimento rapido per le regole di filtro di raccolta

Nella tabella seguente riepiloga le regole per la costruzione di filtri validi per ogni tipo di raccolta dati.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Per esempi di come costruire i filtri validi per ogni case, vedere [come raccolta valida i filtri di scrittura](#bkmk_examples).

Se si scrive spesso i filtri e comprendere le regole dai principi prima può essere utile per più di studiare appena essi, vedere [filtri di raccolta di informazioni su OData in ricerca di Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sui filtri di raccolta OData in ricerca di Azure](search-query-understand-collection-filters.md)
- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
