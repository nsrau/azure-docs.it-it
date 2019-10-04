---
title: Informazioni sui filtri di raccolta OData-ricerca di Azure
description: Informazioni sul funzionamento dei filtri di raccolta OData nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 5c3a0205f5a9ac5115e78f1bc11f70b2c50a9714
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647419"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Informazioni sui filtri di raccolta OData in ricerca di Azure

Per [filtrare](query-odata-filter-orderby-syntax.md) i campi della raccolta in ricerca di Azure, è possibile usare gli [ `any` operatori e `all` ](search-query-odata-collection-operators.md) insieme alle **espressioni lambda**. Le espressioni lambda sono espressioni booleane che fanno riferimento a una **variabile di intervallo**. Gli `any` operatori `all` e sono analoghi a `for` un ciclo nella maggior parte dei linguaggi di programmazione, con la variabile di intervallo che assume il ruolo di variabile del ciclo e l'espressione lambda come corpo del ciclo. La variabile di intervallo accetta il valore "Current" della raccolta durante l'iterazione del ciclo.

Almeno questo è il funzionamento concettuale. In realtà, ricerca di Azure implementa i filtri in modo molto diverso per `for` il funzionamento dei cicli. Idealmente, questa differenza sarebbe invisibile per l'utente, ma in determinate situazioni non lo è. Il risultato finale è la presenza di regole da seguire per la scrittura di espressioni lambda.

Questo articolo illustra il motivo per cui esistono le regole per i filtri di raccolta esplorando il modo in cui ricerca di Azure esegue questi filtri. Se si stanno scrivendo filtri avanzati con espressioni lambda complesse, questo articolo può risultare utile per comprendere le possibili caratteristiche dei filtri e il motivo.

Per informazioni sulle regole per i filtri di raccolta, inclusi esempi, vedere [risoluzione dei problemi relativi ai filtri di raccolta OData in ricerca di Azure](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Perché i filtri della raccolta sono limitati

Esistono tre motivi per cui non tutte le funzionalità di filtro sono supportate per tutti i tipi di raccolte:

1. Solo determinati operatori sono supportati per determinati tipi di dati. Ad esempio, `true` non è sensato confrontare i valori booleani e `false` usando `lt`, `gt`e così via.
1. Ricerca di Azure non supporta la **ricerca correlata** nei campi `Collection(Edm.ComplexType)`di tipo.
1. Ricerca di Azure usa indici invertiti per eseguire filtri su tutti i tipi di dati, incluse le raccolte.

Il primo motivo è solo una conseguenza del modo in cui vengono definiti il linguaggio OData e il sistema di tipi EDM. Le ultime due sono descritte in modo più dettagliato nella parte restante di questo articolo.

## <a name="correlated-versus-uncorrelated-search"></a>Ricerca correlata rispetto a ricerca non correlata

Quando si applicano più criteri di filtro su una raccolta di oggetti complessi, i criteri sono **correlati** perché si applicano a *ogni oggetto nella raccolta*. Ad esempio, il filtro seguente restituirà Alberghi con almeno una stanza Deluxe con una tariffa inferiore a 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se l'applicazionedi filtri non è correlata, il filtro precedente potrebbe restituire gli hotel in cui una stanza è Deluxe e una stanza diversa ha una tariffa di base inferiore a 100. Non avrebbe senso, perché entrambe le clausole dell'espressione lambda si applicano alla stessa variabile di intervallo, ovvero `room`. Questo è il motivo per cui questi filtri sono correlati.

Per la ricerca full-text, tuttavia, non è possibile fare riferimento a una variabile di intervallo specifica. Se si usa la ricerca in campo per emettere una [query Lucene completa](query-lucene-syntax.md) come questa:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

è possibile che si ottenga un hotel in cui una stanza è Deluxe e una stanza diversa indica "City View" nella descrizione. Ad esempio, il documento seguente con `Id` di `1` corrisponderebbe alla query:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Il motivo è che `Rooms/Type` si riferisce a tutti i termini analizzati `Rooms/Type` del campo nell'intero documento e allo stesso `Rooms/Description`modo per, come illustrato nelle tabelle seguenti.

Modalità `Rooms/Type` di archiviazione per la ricerca full-text:

| Termine in`Rooms/Type` | ID documento |
| --- | --- |
| Deluxe | 1, 2 |
| Standard | 1 |

Modalità `Rooms/Description` di archiviazione per la ricerca full-text:

| Termine in`Rooms/Description` | ID documento |
| --- | --- |
| cortile | 2 |
| city | 1 |
| Garden | 1 |
| large | 1 |
| motel | 2 |
| camera | 1, 2 |
| Standard | 1 |
| Suite | 1 |
| visualizza | 1 |

Quindi, a differenza del filtro riportato sopra, che indica in sostanza che "corrisponde ai `Type` documenti in cui una stanza è uguale alla" stanza Deluxe `BaseRate` "e la **stessa stanza** ha meno di 100", la query `Rooms/Type` di ricerca indica "trova i documenti con il termine" Deluxe "e `Rooms/Description` ha la frase" City View ". Nel secondo caso, non esiste alcun concetto di singole chat con campi che possono essere correlati.

> [!NOTE]
> Per visualizzare il supporto per la ricerca correlata aggiunta a ricerca di Azure, votare [questo elemento vocale utente](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Indici e raccolte invertiti

È possibile che si sia notato un numero molto inferiore di restrizioni sulle espressioni lambda rispetto a raccolte complesse rispetto a quelle per `Collection(Edm.Int32)`raccolte `Collection(Edm.GeographyPoint)`semplici come, e così via. Questo perché ricerca di Azure archivia raccolte complesse come raccolte effettive di documenti secondari, mentre le raccolte semplici non vengono archiviate come raccolte.

Si consideri, ad esempio, un campo di `seasons` raccolta di stringhe filtrabile come in un indice per un rivenditore online. Alcuni documenti caricati in questo indice possono avere un aspetto simile al seguente:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

I valori del `seasons` campo vengono archiviati in una struttura denominata **indice invertito**, che ha un aspetto simile al seguente:

| Termine | ID documento |
| --- | --- |
| primavera | 1, 2 |
| estate | 1 |
| rientrano | 1, 2 |
| invernali | 2, 3 |

Questa struttura di dati è progettata per rispondere a una domanda con una velocità elevata: In quali documenti viene visualizzato un determinato termine? La risposta a questa domanda funziona in modo più simile a un controllo di uguaglianza semplice rispetto a un ciclo su una raccolta. Infatti, questo è il motivo per cui per le raccolte di stringhe, `eq` ricerca di Azure consente solo come operatore di confronto `any`all'interno di un'espressione lambda per.

Basandosi sull'uguaglianza, verrà ora esaminato in che modo è possibile combinare più controlli di uguaglianza sulla stessa variabile di intervallo con `or`. Funziona grazie all'algebra e [alla proprietà distributiva dei quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Espressione seguente:

    seasons/any(s: s eq 'winter' or s eq 'fall')

Equivale a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e ognuna delle due `any` sottoespressioni può essere eseguita in modo efficiente tramite l'indice invertito. Inoltre, grazie alla [legge di negazione dei quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation), questa espressione:

    seasons/all(s: s ne 'winter' and s ne 'fall')

Equivale a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

per questo motivo è possibile usare `all` con `ne` e `and`.

> [!NOTE]
> Sebbene i dettagli esulino dall'ambito di questo documento, questi stessi principi si estendono ai [test di distanza e intersezione per le raccolte di punti](search-query-odata-geo-spatial-functions.md) geospaziali. Per questo motivo, in `any`:
>
> - `geo.intersects`non può essere negato
> - `geo.distance`deve essere confrontato utilizzando `lt` o`le`
> - le espressioni devono essere combinate con `or`, non`and`
>
> Le regole opposte si `all`applicano a.

È consentita una varietà più ampia di espressioni quando si filtrano le raccolte di tipi `lt`di `gt`dati `le`che supportano `ge` gli operatori,, `Collection(Edm.Int32)` e, ad esempio. In particolare, è possibile `and` utilizzare `or` e in `any`, purché le espressioni di confronto sottostanti vengano combinate in confronti di `and`intervallo utilizzando, che vengono quindi combinate ulteriormente utilizzando. `or` Questa struttura di espressioni booleane è denominata [disgiuntiva Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), altrimenti nota come "ORS of le". Viceversa, le espressioni lambda per `all` per questi tipi di dati devono essere in [formato congiuntiva normale (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), altrimenti note come "e di ORS". Ricerca di Azure consente i confronti di intervalli perché possono eseguirli usando indici invertiti in modo efficiente, analogamente a quanto avviene con la ricerca a breve termine per le stringhe.

In breve, di seguito sono riportate le regole generali per gli elementi consentiti in un'espressione lambda:

- All' `any`interno di, i *controlli positivi* sono sempre consentiti, ad esempio `geo.intersects`uguaglianza, `geo.distance` confronti `le` tra intervalli, o confrontati con `lt` OR (si può pensare alla "" somiglianza "come uguaglianza quando si tratta di controllare distanza).
- All' `any`interno `or` di, è sempre consentito. È possibile usare `and` solo per i tipi di dati che possono esprimere i controlli di intervallo e solo se si usano gli ORS di i (DNF).
- All' `all`interno di, le regole sono invertite, ma sono consentite solo *verifiche negative* , è possibile usare `and` sempre ed è possibile usare `or` solo per i controlli di intervallo espressi come e di ORS (CNF).

In pratica, questi sono i tipi di filtri che è più probabile usare comunque. È comunque utile comprendere i limiti di ciò che è possibile però.

Per esempi specifici di quali tipi di filtri sono consentiti e quali non sono, vedere [come scrivere filtri di raccolta validi](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passaggi successivi  

- [Risoluzione dei problemi relativi ai filtri di raccolta OData in ricerca di Azure](search-query-troubleshoot-collection-filters.md)
- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
