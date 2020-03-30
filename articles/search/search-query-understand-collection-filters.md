---
title: Informazioni sui filtri di raccolta ODataUnderstanding OData collection filters
titleSuffix: Azure Cognitive Search
description: Informazioni sui meccanismi di funzionamento dei filtri di raccolta OData nelle query di Ricerca cognitiva di Azure, incluse limitazioni e comportamenti univoci per le raccolte.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113062"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Informazioni sui filtri della raccolta OData in Ricerca cognitiva di AzureUnderstanding OData collection filters in Azure Cognitive Search

Per [filtrare](query-odata-filter-orderby-syntax.md) i campi di raccolta in Ricerca cognitiva di Azure, è possibile usare gli [ `any` operatori `all` e](search-query-odata-collection-operators.md) insieme alle espressioni **lambda**. Le espressioni lambda sono espressioni booleane che fanno riferimento a una **variabile di intervallo**. Gli `any` `all` operatori e sono analoghi a un `for` ciclo nella maggior parte dei linguaggi di programmazione, con la variabile di intervallo che assume il ruolo di variabile di ciclo e l'espressione lambda come corpo del ciclo. La variabile di intervallo assume il valore "corrente" della raccolta durante l'iterazione del ciclo.

Almeno è così che funziona concettualmente. In realtà, Ricerca cognitiva di Azure implementa `for` i filtri in modo molto diverso da come funzionano i cicli. Idealmente, questa differenza sarebbe invisibile per voi, ma in alcune situazioni non lo è. Il risultato finale è che ci sono regole che devi seguire quando scrivi espressioni lambda.

Questo articolo illustra perché esistono le regole per i filtri di raccolta esplorando il modo in cui Ricerca cognitiva di Azure esegue questi filtri. Se si scrivono filtri avanzati con espressioni lambda complesse, questo articolo può risultare utile per comprendere cosa è possibile eseguire nei filtri e perché.

Per informazioni sulle regole per i filtri di raccolta, inclusi esempi, vedere [Risoluzione dei problemi relativi ai filtri di raccolta OData in Ricerca cognitiva](search-query-troubleshoot-collection-filters.md)di Azure.For information on what the rules for collection filters are, including examples, see Troubleshooting OData collection filters in Azure Cognitive Search .

## <a name="why-collection-filters-are-limited"></a>Perché i filtri di raccolta sono limitati

Esistono tre motivi di fondo per cui non tutte le funzionalità di filtro sono supportate per tutti i tipi di raccolte:

1. Per determinati tipi di dati sono supportati solo determinati operatori. Ad esempio, non ha senso confrontare `true` i `false` `lt`valori `gt`booleani e utilizzare , , e così via.
1. Ricerca cognitiva di Azure non supporta la `Collection(Edm.ComplexType)`ricerca **correlata** nei campi di tipo .
1. Ricerca cognitiva di Azure usa gli indici invertiti per eseguire filtri su tutti i tipi di dati, incluse le raccolte.

Il primo motivo è solo una conseguenza di come vengono definiti il linguaggio OData e il sistema di tipi EDM. Gli ultimi due sono spiegati in modo più dettagliato nel resto di questo articolo.

## <a name="correlated-versus-uncorrelated-search"></a>Ricerca correlata a ricerca non correlata

Quando si applicano più criteri di filtro a una raccolta di oggetti complessi, i criteri vengono **correlati** poiché si applicano a *ogni oggetto della raccolta.* Ad esempio, il seguente filtro restituirà gli hotel con almeno una camera deluxe con una tariffa inferiore a 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se il filtro non è stato *correlato,* il filtro di cui sopra potrebbe restituire alberghi in cui una camera è deluxe e una camera diversa ha una tariffa di base inferiore a 100. Questo non avrebbe senso, poiché entrambe le clausole dell'espressione `room`lambda si applicano alla stessa variabile di intervallo, vale a dire . Questo è il motivo per cui tali filtri sono correlati.

Tuttavia, per la ricerca full-text, non è possibile fare riferimento a una variabile di intervallo specifica. Se si utilizza la ricerca campiata per eseguire una [query Lucene completa](query-lucene-syntax.md) come questa:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

si possono ottenere alberghi indietro dove una camera è deluxe, e una camera diversa menziona "vista città" nella descrizione. Ad esempio, il `Id` documento `1` seguente con di corrisponderebbe alla query:

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

Il motivo `Rooms/Type` è che si riferisce `Rooms/Type` a tutti i termini analizzati del campo nell'intero documento e allo stesso modo per `Rooms/Description`, come illustrato nelle tabelle seguenti.

Come `Rooms/Type` viene memorizzato per la ricerca full-text:

| Termine in`Rooms/Type` | ID documento |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Come `Rooms/Description` viene memorizzato per la ricerca full-text:

| Termine in`Rooms/Description` | ID documento |
| --- | --- |
| Cortile | 2 |
| city | 1 |
| Giardino | 1 |
| grande | 1 |
| motel | 2 |
| Camera | 1, 2 |
| standard | 1 |
| Suite | 1 |
| vista | 1 |

Quindi, a differenza del filtro sopra, che `Type` fondamentalmente dice "corrisponde a documenti in cui una stanza ha uguale a 'Deluxe Room' e **quella stessa stanza** ha `BaseRate` meno di 100", la query di ricerca dice "documenti di corrispondenza dove `Rooms/Type` ha il termine "deluxe" e `Rooms/Description` ha la frase "city view". Non esiste un concetto di singole stanze i cui campi possono essere correlati in quest'ultimo caso.

> [!NOTE]
> Se si desidera visualizzare il supporto per la ricerca correlata aggiunto a Ricerca cognitiva di Azure, si prega di votare per [questo elemento vocale utente](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Indici e raccolte invertiti

Avrete notato che ci sono molte meno restrizioni sulle espressioni lambda su `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)`raccolte complesse rispetto a quelle per raccolte semplici come , e così via. Ciò è dovuto al fatto che Ricerca cognitiva di Azure archivia raccolte complesse come raccolte effettive di documenti secondari, mentre le raccolte semplici non vengono archiviate come raccolte.

Si consideri, ad esempio, `seasons` un campo di raccolta di stringhe filtrabile come in un indice per un rivenditore online. Alcuni documenti caricati in questo indice potrebbero essere simili al seguente:Some documents uploaded to this index might look like this:

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

I valori `seasons` del campo vengono memorizzati in una struttura denominata **indice invertito**, simile alla seguente:

| Termine | ID documento |
| --- | --- |
| Primavera | 1, 2 |
| Estate | 1 |
| cadere | 1, 2 |
| Inverno | 2, 3 |

Questa struttura di dati è progettata per rispondere a una domanda con grande velocità: in quali documenti appare un determinato termine? Rispondere a questa domanda funziona più come un semplice controllo di uguaglianza che un ciclo su una raccolta. Infatti, questo è il motivo per `eq` cui per le raccolte di `any`stringhe, Ricerca cognitiva di Azure consente solo come operatore di confronto all'interno di un'espressione lambda per .

Partendo dall'uguaglianza, si esaminerà come è possibile combinare più controlli di `or`uguaglianza sulla stessa variabile di intervallo con . Funziona grazie all'algebra e [alla proprietà distributiva dei quantificatori.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) Questa espressione:

    seasons/any(s: s eq 'winter' or s eq 'fall')

Equivale a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e ciascuna `any` delle due sottoespressioni può essere eseguita in modo efficiente utilizzando l'indice invertito. Inoltre, grazie alla [legge sulla negazione dei quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation), questa espressione:

    seasons/all(s: s ne 'winter' and s ne 'fall')

Equivale a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

ed è per questo che `all` `ne` è `and`possibile utilizzare con e .

> [!NOTE]
> Anche se i dettagli esulano dall'ambito di questo documento, questi stessi principi si estendono anche ai test di [distanza e intersezione anche per le raccolte di punti geospaziali.](search-query-odata-geo-spatial-functions.md) Questo è il `any`motivo per cui, in :
>
> - `geo.intersects`non può essere negato
> - `geo.distance`deve essere `lt` confrontato utilizzando o`le`
> - le espressioni devono `or`essere combinate con , non`and`
>
> Le regole contrarie `all`si applicano per .

È consentita una gamma più ampia di espressioni quando `lt` `gt`si `le`filtrano raccolte di tipi di dati che supportano gli operatori , , e `ge` , ad `Collection(Edm.Int32)` esempio . In particolare, `and` è possibile `or` `any`utilizzare e in , purché le espressioni `and`di confronto sottostanti `or`siano combinate in confronti di **intervalli** utilizzando , che vengono quindi ulteriormente combinati utilizzando . Questa struttura di espressioni booleane è denominata [Disgiuntive Normal Form (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)altrimenti nota come "OR di AND". Al contrario, le `all` espressioni lambda per questi tipi di dati devono essere in forma normale congiuntiva [(CNF),](https://en.wikipedia.org/wiki/Conjunctive_normal_form)altrimenti noto come "AND di OR". Ricerca cognitiva di Azure consente tali confronti di intervalli perché può eseguirli usando gli indici invertiti in modo efficiente, proprio come può eseguire una ricerca rapida dei termini per le stringhe.

In sintesi, ecco le regole empiriche per ciò che è consentito in un'espressione lambda:In summary, here are the rules of thumb for what's allowed in a lambda expression:

- `any`All'interno, i *controlli positivi* sono sempre `geo.intersects`consentiti, come l'uguaglianza, i confronti di intervalli, o `geo.distance` rispetto `lt` a o (si pensi alla `le` "vicinanza" come a come all'uguaglianza quando si tratta di controllare la distanza).
- All'interno, `any` `or` è sempre consentito. È possibile `and` utilizzare solo per i tipi di dati che possono esprimere i controlli di intervallo e solo se si utilizzano OR di AND (DNF).
- All'interno `all`, le regole sono invertite : sono consentiti `or` solo i controlli *negativi,* è possibile utilizzare `and` sempre ed è possibile utilizzare solo per i controlli di intervallo espressi come AND di OR (CNF).

In pratica, questi sono i tipi di filtri che è più probabile utilizzare comunque. È comunque utile capire i confini di ciò che è possibile però.

Per esempi specifici di quali tipi di filtri sono consentiti e quali no, vedere [Come scrivere filtri di raccolta validi.](search-query-troubleshoot-collection-filters.md#bkmk_examples)

## <a name="next-steps"></a>Passaggi successivi  

- [Risoluzione dei problemi relativi ai filtri di raccolta OData in Ricerca cognitiva di AzureTroubleshooting OData collection filters in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Filtri in Ricerca cognitiva di AzureFilters in Azure Cognitive Search](search-filters.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
