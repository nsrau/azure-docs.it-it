---
title: Informazioni sui filtri di raccolta OData - ricerca di Azure
description: Informazioni sul funzionamento dei filtri di raccolta di OData nelle query di ricerca di Azure.
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
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079600"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Informazioni sui filtri di raccolta OData in ricerca di Azure

Per [filtro](query-odata-filter-orderby-syntax.md) nei campi della raccolta in ricerca di Azure, è possibile utilizzare il [ `any` e `all` operatori](search-query-odata-collection-operators.md) assieme **espressioni lambda**. Le espressioni lambda sono espressioni booleane che fanno riferimento a un **variabile di intervallo**. Il `any` e `all` sono analoghi agli operatori un `for` ciclo nella maggior parte dei linguaggi di programmazione, con la variabile di intervallo richiede il ruolo della variabile di ciclo e l'espressione lambda come corpo del ciclo. La variabile di intervallo assume il valore "corrente" della raccolta durante l'iterazione del ciclo.

Questa è almeno come funziona a livello concettuale. In realtà, ricerca di Azure implementa i filtri in modo molto diverso come `for` esegue un ciclo di lavoro. In teoria, questa differenza sarà invisibile all'utente, ma in alcuni casi non lo è. Il risultato finale è che non esistono regole che è necessario seguire durante la scrittura di espressioni lambda.

Questo articolo illustra il motivo per cui le regole per i filtri di raccolta esistono esplorando la modalità di esecuzione questi filtri in ricerca di Azure. Se si scrivono filtri avanzati con le espressioni lambda complesse, utili in questo articolo nella creazione la comprensione di ciò che è possibile nei filtri e perché.

Per informazioni su ciò che le regole per i filtri di raccolta sono, inclusi gli esempi, vedere [filtri di raccolta OData di risoluzione dei problemi in ricerca di Azure](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Il motivo per cui i filtri di raccolta sono limitati

Esistono tre motivi sottostanti perché non sono supportate tutte le funzionalità di filtro per tutti i tipi di raccolte:

1. Solo determinati operatori sono supportati per determinati tipi di dati. Ad esempio, non ha senso per confrontare i valori booleani `true` e `false` utilizzando `lt`, `gt`e così via.
1. Ricerca di Azure non supporta **ricerca correlata** sui campi di tipo `Collection(Edm.ComplexType)`.
1. Azure Usa ricerca invertita indici per l'esecuzione di filtri su tutti i tipi di dati, ad esempio le raccolte.

Il primo motivo è semplicemente una conseguenza del modo in cui la lingua di OData e il sistema di tipi EDM sono definiti. Gli ultimi due sono spiegati più dettagliatamente nel resto di questo articolo.

## <a name="correlated-versus-uncorrelated-search"></a>Correlato e la ricerca non correlata

Quando si applica più criteri di filtro su una raccolta di oggetti complessi, i criteri vengono **correlato** poiché sono applicabili ai *ogni oggetto nella raccolta*. Ad esempio, il filtro seguente restituisce gli hotel con almeno una chat room deluxe con una frequenza minore di 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se il filtro è stata *non correlate*, il filtro precedente potrebbe restituire gli hotel in cui un unico ambiente è deluxe e un diverso spazio ha una base velocità inferiore a 100. Che avrebbe senso, perché entrambe le clausole di espressione lambda si applicano alla stessa variabile di intervallo, vale a dire `room`. Ecco perché tali filtri sono correlati.

Tuttavia, per la ricerca full-text non è per fare riferimento a una variabile di intervallo specifico. Se si usa con campo di ricerca per emettere un [query completa Lucene](query-lucene-syntax.md) come questo:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

è possibile ricevere nuovamente hotel in cui un unico ambiente deluxe e una chat room diversi parla di "visualizzazione per città" nella descrizione. Ad esempio, il documento seguente con `Id` di `1` corrisponderebbe la query:

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

Il motivo è che `Rooms/Type` fa riferimento a tutti i termini analizzati del `Rooms/Type` campo nell'intero documento e analogamente delle `Rooms/Description`, come illustrato nelle tabelle seguenti.

Come `Rooms/Type` viene archiviato per la ricerca full-text:

| Termine in `Rooms/Type` | ID di documento |
| --- | --- |
| deluxe | 1, 2 |
| standard | 1 |

Come `Rooms/Description` viene archiviato per la ricerca full-text:

| Termine in `Rooms/Description` | ID di documento |
| --- | --- |
| cortile | 2 |
| city | 1 |
| garden | 1 |
| Large | 1 |
| motel | 2 |
| Chat room | 1, 2 |
| standard | 1 |
| Suite | 1 |
| view | 1 |

Pertanto, a differenza del filtro precedente, che sostanzialmente indica "documenti in cui ha una chat room `Type` uguale a 'Deluxe chat Room' e **tale stessa stanza** ha `BaseRate` meno di 100", afferma di query di ricerca "corrispondenza documenti dove `Rooms/Type`contiene il termine "deluxe" e `Rooms/Description` dispone la frase "Vista city". Non vi è alcun concetto di singole stanze possono essere in correlazione i cui campi nel secondo caso.

> [!NOTE]
> Se si vuole vedere supporto per la ricerca correlato aggiunto alla ricerca di Azure, votarla [questo elemento di User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Le raccolte e indici invertiti

Si è notato che non esistono meno restrizioni sulle espressioni lambda nelle raccolte complesse maggiore disponibili, ad esempio raccolte semplici `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`e così via. Questo avviene perché ricerca di Azure archivia le raccolte complesse come raccolte effettive di documenti secondari, mentre raccolte semplici non vengono archiviate come raccolte affatto.

Si consideri ad esempio un campo collection filtrabile stringa, ad esempio `seasons` in un indice per un rivenditore online. Alcuni documenti caricati in questo indice potrebbe essere simile al seguente:

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

I valori del `seasons` campo vengono archiviati in una struttura denominata un' **indice invertito**, quale simile al seguente:

| Nome | ID di documento |
| --- | --- |
| Spring | 1, 2 |
| summer | 1 |
| cadere | 1, 2 |
| inverno | 2, 3 |

Questa struttura di dati è progettata per rispondere a una domanda con grande rapidità: In documenti che viene visualizzato un termine specifico? Rispondere a questa domanda più funziona come un controllo di uguaglianza semplici rispetto a un ciclo su una raccolta. In effetti, questo è il motivo per cui per raccolte di stringhe, ricerca di Azure consente solo `eq` come operatore di confronto all'interno di un'espressione lambda per `any`.

Compila da uguaglianza, quindi vedremo come è possibile combinare più controlli di uguaglianza sulla stessa variabile di intervallo con `or`. Funziona grazie all'algebra e [la proprietà distributiva di quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Questa espressione:

    seasons/any(s: s eq 'winter' or s eq 'fall')

Equivale a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e ognuno dei due `any` sottoespressioni possono essere eseguiti in modo efficiente tramite l'indice invertito. Inoltre, grazie a [legge la negazione di quantificatori](https://en.wikipedia.org/wiki/Existential_quantification#Negation), questa espressione:

    seasons/all(s: s ne 'winter' and s ne 'fall')

Equivale a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

motivo per cui è possibile usare `all` con `ne` e `and`.

> [!NOTE]
> Anche se i dettagli sono esula dall'ambito di questo documento, estendono questi stessi principi [distanza e intersezione test per le raccolte di punti geo-spaziali](search-query-odata-geo-spatial-functions.md) anche. È per questo motivo, in `any`:
>
> - `geo.intersects` non può essere negata
> - `geo.distance` devono essere confrontati utilizzando `lt` o `le`
> - le espressioni devono essere combinate con `or`, non `and`
>
> Si applicano le regole opposte per `all`.

Un'ampia gamma di espressioni sono consentiti durante l'applicazione di filtri alle raccolte di dati, i tipi che supportano il `lt`, `gt`, `le`, e `ge` operatori, ad esempio `Collection(Edm.Int32)` , ad esempio. In particolare, è possibile usare `and` nonché `or` in `any`, purché le espressioni di confronto sottostanti vengono combinate nelle **intervallo confronti** usando `and`, che sono quindi ulteriormente combinate usando `or`. Questa struttura di espressioni booleane, detta [rimandate Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), altrimenti noto come "Or di and". Per contro, le espressioni lambda `all` per i dati di questi tipi devono essere [congiunzione Normal Form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), altrimenti noto come "And di operatori OR". Ricerca di Azure consente a questi confronti di intervallo poiché è possibile eseguirli usando invertito gli indici in modo efficiente, proprio come l'attività possa eseguire ricerca termini veloci per le stringhe.

In sintesi, ecco le regole generali per ciò che è consentito in un'espressione lambda:

- All'interno `any`, *controlli positivi* sono sempre consentiti, come l'uguaglianza, i confronti degli intervalli, `geo.intersects`, o `geo.distance` confrontato con `lt` o `le` (può essere considerata la "vicinanza" come, ad esempio uguaglianza quando si tratta di controllo della distanza).
- All'interno `any`, `or` è sempre consentito. È possibile usare `and` solo per i tipi di dati che è possono esprimere i controlli di intervallo e solo se si utilizza operatori OR di and (DNF).
- All'interno `all`, le regole vengono invertite, solo *negativi controlli* sono consentiti, è possibile usare `and` sempre, ed è possibile usare `or` solo per i controlli intervallo espresso come and di operatori OR (CNF).

In pratica, questi sono i tipi di filtri che quali si potrebbe utilizzare comunque. È comunque utile conoscere i limiti di ciò che è possibile tuttavia.

Per esempi specifici di quali tipi di filtri sono consentiti e che non sono, vedere [come raccolta valida i filtri di scrittura](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passaggi successivi  

- [Risoluzione dei problemi di filtri di raccolta OData in ricerca di Azure](search-query-troubleshoot-collection-filters.md)
- [Filtri in Ricerca di Azure](search-filters.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
