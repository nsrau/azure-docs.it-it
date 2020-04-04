---
title: Sintassi di query semplice
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento per la sintassi di query semplice usata per le query di ricerca full-text in Ricerca cognitiva di Azure.Reference for the simple query syntax used for full-text search queries in Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
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
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656159"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Ricerca cognitiva di AzureSimple query syntax in Azure Cognitive Search

Ricerca cognitiva di Azure implementa due linguaggi di query basati su Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). In Ricerca cognitiva di Azure la sintassi di query semplice esclude le opzioni fuzzy/slop.  

> [!NOTE]
> La sintassi di query semplice viene utilizzata per le espressioni di query passate nel parametro **di ricerca** dell'API [Cerca documenti,](https://docs.microsoft.com/rest/api/searchservice/search-documents) da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) utilizzata per il [parametro $filter](search-filters.md) di tale API. Queste diverse sintassi hanno regole specifiche per la costruzione di query, l'escizzazione di stringhe e così via.
>
> Ricerca cognitiva di Azure offre una sintassi di [query Lucene completa](query-lucene-syntax.md) alternativa per query più complesse nel parametro di **ricerca.** Per altre informazioni sull'architettura di analisi delle query e sui vantaggi di ogni sintassi, vedere Funzionamento della [ricerca full-text in Ricerca cognitiva](search-lucene-query-architecture.md)di Azure.To learn more about query parsing architecture and benefits of each syntax, see How full text search works in Azure Cognitive Search .

## <a name="how-to-invoke-simple-parsing"></a>Come richiamare l'analisi semplice

La sintassi semplice è quella predefinita. La chiamata è necessaria solo se si sta reimpostando la sintassi da completa a semplice. Per impostare la sintassi in modo esplicito, usare il parametro di ricerca `queryType`. I valori validi includono `simple|full`, con `simple` come impostazione predefinita e `full` per Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie di comportamento delle query

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Ricerca cognitiva di Azure corrisponderà ai documenti contenenti uno o tutti i termini, incluse eventuali variazioni rilevate durante l'analisi del testo. 

Per quanto semplice, esiste un aspetto dell'esecuzione delle query in Ricerca cognitiva di Azure che *potrebbe* produrre risultati imprevisti, aumentando anziché ridurre i risultati della ricerca man mano che più termini e operatori vengono aggiunti alla stringa di input. Che questa espansione si verifichi o meno dipende dall'inclusione di un operatore NOT, combinato con un'impostazione del parametro `searchMode` che determina come NOT viene interpretato dal punto di vista dei comportamenti di AND o OR. Con l'impostazione predefinita `searchMode=Any` e un operatore NOT, l'operazione viene calcolata come azione OR e quindi `"New York" NOT Seattle` restituisce tutte le città diverse da Seattle.  

In genere, è più probabile osservare questi comportamenti nei modelli di interazione utente per le applicazioni che eseguono ricerche sul contenuto, dove è più probabile che gli utenti includano un operatore in una query, a differenza dei siti di e-commerce che hanno strutture di esplorazione più integrate. Per altre informazioni, vedere [Operatore NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatori booleani (AND, OR, NOT) 

È possibile incorporare gli operatori in una stringa di query per creare un set completo di criteri in base ai quali vengono trovati i documenti corrispondenti. 

### <a name="and-operator-"></a>Operatore AND `+`

L'operatore AND è un segno più. Ad esempio, `wifi+luxury` cercherà i documenti contenenti sia `wifi` che `luxury`.

### <a name="or-operator-"></a>Operator OR `|`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi | luxury` cercherà i documenti contenenti `wifi` o `luxury` oppure entrambi.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatore NOT `-`

L'operatore NOT è un segno meno. Ad esempio, `wifi –luxury` cercherà i documenti che contengono il termine `wifi` e/o non contengono `luxury` (e/o sono controllati da `searchMode`).

> [!NOTE]  
>  L'opzione `searchMode` controlla se un termine con l'operatore NOT è associato tramite l'operatore AND o OR agli altri termini nella query in assenza di un operatore `+` o `|`. Si ricordi che `searchMode` può essere impostato su `any` (impostazione predefinita) o su `all`. Se si usa `any`, il livello di richiamo delle query aumenta includendo più risultati e per impostazione predefinita il segno `-` verrà interpretato come "OR NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`. Se si usa `all`, il livello di precisione delle query aumenta includendo meno risultati e per impostazione predefinita il segno - verrà interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento verosimilmente più intuitivo per l'operatore `-`. Valutare quindi l'opportunità di usare `searchMode=all` invece di `searchMode=any` se si vuole ottimizzare il livello di precisione delle ricerche invece che quello di richiamo *e* gli utenti usano spesso l'operatore `-` nelle ricerche.

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>Operatore suffisso `*` per la ricerca dei prefissi

L'operatore suffisso è un asterisco `*`. `cap*`, ad esempio, cercherà i documenti contenenti un termine che inizia con `cap`, ignorando le lettere maiuscole/minuscole. 

Analogamente ai filtri, una query di prefisso cerca una corrispondenza esatta. Di conseguenza, non vi è alcun punteggio di pertinenza (tutti i risultati ricevono un punteggio di ricerca di 1.0). Le query di prefisso possono essere lente, soprattutto se l'indice è di grandi dimensioni e il prefisso è costituito da un numero ridotto di caratteri. 

Se si desidera eseguire una query suffisso, corrispondente nell'ultima parte della stringa, utilizzare una [ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard) e la sintassi Lucene completa.

## <a name="phrase-search-operator"></a>Operatore di ricerca frasi 

L'operatore frase racchiude una `" "`frase tra virgolette . Ad esempio, mentre `Roach Motel` (senza virgolette) cerca i documenti contenenti `Roach` e/o `Motel` ovunque e in qualsiasi ordine, `"Roach Motel"` (con le virgolette) troverà solo i documenti contenenti l'intera frase in quell'ordine specifico (l'analisi del testo è comunque applicabile).

## <a name="precedence-operator"></a>Operatori di precedenza 

L'operatore di precedenza racchiude `( )`la stringa tra parentesi. Ad esempio, `motel+(wifi | luxury)` cercherà i documenti contenenti il termine motel e uno `wifi` o `luxury` (o entrambi).  

## <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

 Per usare i simboli elencati sopra come parte effettiva del testo di ricerca, è necessario che siano preceduti da una barra rovesciata come carattere di escape. Ad esempio, `luxury\+hotel` restituirà il termine `luxury+hotel`. Per semplificare i casi più comuni, sono previste due eccezioni a questa regola, in cui i caratteri di escape non sono necessari:  

- L'operatore NOT `-` deve essere preceduto da un carattere di escape solo se è il primo carattere dopo lo spazio vuoto, non se è all'interno di un termine. `wi-fi`, ad esempio, è un termine singolo, mentre i GUID (ad esempio `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) vengono trattati come un singolo token.
- L'operatore suffisso `*` deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima dello spazio vuoto, non se è all'interno di un termine. `wi*fi`, ad esempio, viene considerato come un singolo token.

> [!NOTE]  
>  Anche se i caratteri di escape mantengono uniti i token, l'analisi del testo potrebbe dividerli, a seconda della modalità di analisi. Per informazioni dettagliate, vedere [Supporto della lingua &#40;&#41;dell'API REST di Ricerca cognitiva di Azure.See Language support &#40;Azure Cognitive Search REST API&#41;](index-add-language-analyzers.md) for details.  

## <a name="see-also"></a>Vedere anche  

+ [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni OData](query-odata-filter-orderby-syntax.md) 
