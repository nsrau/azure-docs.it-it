---
title: Sintassi di query semplice
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento per la sintassi di query semplice utilizzata per le query di ricerca full-text in Azure ricerca cognitiva.
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
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152670"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Azure ricerca cognitiva

Azure ricerca cognitiva implementa due linguaggi di query basati su Lucene: il parser di query [semplice](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e il [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). In ricerca cognitiva di Azure, la sintassi di query semplice esclude le opzioni fuzzy/slop.  

> [!NOTE]
> La sintassi di query semplice viene usata per le espressioni di query passate nel parametro di **ricerca** dell'API di [ricerca dei documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents) , da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) usata per il parametro [$Filter](search-filters.md) di tale API. Queste diverse sintassi hanno regole proprie per la costruzione di query, l'escape di stringhe e così via.
>
> Azure ricerca cognitiva offre una [sintassi di query Lucene completa](query-lucene-syntax.md) alternativa per query più complesse nel parametro **Search** . Per altre informazioni sull'architettura di analisi delle query e sui vantaggi di ogni sintassi, vedere funzionamento della [ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Come richiamare l'analisi semplice

La sintassi semplice è quella predefinita. La chiamata è necessaria solo se si sta reimpostando la sintassi da completa a semplice. Per impostare la sintassi in modo esplicito, usare il parametro di ricerca `queryType`. I valori validi includono `simple|full`, con `simple` come impostazione predefinita e `full` per Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie di comportamento delle query

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Azure ricerca cognitiva corrisponderà ai documenti che contengono uno o tutti i termini, incluse eventuali variazioni trovate durante l'analisi del testo. 

Con la stessa semplicità di questa operazione, esiste un aspetto dell'esecuzione di query in Azure ricerca cognitiva che *potrebbe* produrre risultati imprevisti, aumentando invece di diminuire i risultati della ricerca, perché vengono aggiunti altri termini e operatori alla stringa di input. Che questa espansione si verifichi o meno dipende dall'inclusione di un operatore NOT, combinato con un'impostazione del parametro `searchMode` che determina come NOT viene interpretato dal punto di vista dei comportamenti di AND o OR. Con l'impostazione predefinita `searchMode=Any` e un operatore NOT, l'operazione viene calcolata come azione OR e quindi `"New York" NOT Seattle` restituisce tutte le città diverse da Seattle.  

In genere, è più probabile osservare questi comportamenti nei modelli di interazione utente per le applicazioni che eseguono ricerche sul contenuto, dove è più probabile che gli utenti includano un operatore in una query, a differenza dei siti di e-commerce che hanno strutture di esplorazione più integrate. Per altre informazioni, vedere [Operatore NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatori booleani (AND, OR, NOT) 

È possibile incorporare gli operatori in una stringa di query per creare un set completo di criteri in base al quale vengono trovati i documenti corrispondenti. 

### <a name="and-operator-"></a>Operatore AND `+`

L'operatore AND è un segno più. Ad esempio, `wifi+luxury` cercherà i documenti contenenti sia `wifi` che `luxury`.

### <a name="or-operator-"></a>Operator OR `|`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi | luxury` cercherà i documenti contenenti `wifi` o `luxury` oppure entrambi.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatore NOT `-`

L'operatore NOT è un segno meno. Ad esempio, `wifi –luxury` cercherà i documenti che contengono il termine `wifi` e/o non contengono `luxury` (e/o sono controllati da `searchMode`).

> [!NOTE]  
>  L'opzione `searchMode` controlla se un termine con l'operatore NOT viene individuato con l'operatore AND o OR con l'altro termine nella query se non è presente un operatore `+` o `|`. Tenere presente che `searchMode` può essere impostato su `any` (impostazione predefinita) o `all`. Se si usa `any`, le query restituiranno un numero maggiore di risultati e, per impostazione predefinita, `-` verrà interpretato come "OR NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`. Se si usa `all`, le query saranno più precise e restituiranno un numero minore di risultati e, per impostazione predefinita, - verrà interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento più intuitivo per l'operatore `-`. Valutare quindi l'opportunità di usare `searchMode=all` invece di `searchMode=any` se si vuole ottimizzare il livello di precisione delle ricerche invece che quello di richiamo *e* gli utenti usano spesso l'operatore `-` nelle ricerche.

## <a name="suffix-operator"></a>Operatore suffisso

L'operatore suffisso è un asterisco `*`. `lux*`, ad esempio, cercherà i documenti contenenti un termine che inizia con `lux`, ignorando le lettere maiuscole/minuscole.  

## <a name="phrase-search-operator"></a>Operatore di ricerca frasi

L'operatore phrase racchiude una frase tra virgolette `" "`. Ad esempio, mentre `Roach Motel` (senza virgolette) cerca i documenti contenenti `Roach` e/o `Motel` ovunque e in qualsiasi ordine, `"Roach Motel"` (con le virgolette) troverà solo i documenti contenenti l'intera frase in quell'ordine specifico (l'analisi del testo è comunque applicabile).

## <a name="precedence-operator"></a>Operatore di precedenza

L'operatore di precedenza racchiude la stringa tra parentesi `( )`. `motel+(wifi | luxury)`, ad esempio, cercherà i documenti che contengono il termine del Motel e `wifi` o `luxury` (o entrambi).  

## <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

 Per usare i simboli precedenti come parte effettiva del testo della ricerca, dovranno essere preceduti da una barra rovesciata come carattere di escape. Ad esempio, `luxury\+hotel` restituirà il termine `luxury+hotel`. Per semplificare le operazioni per i casi più comuni, esistono due eccezioni a questa regola in base alla quale non è necessario usare un carattere di escape:  

- L'operatore NOT `-` deve essere preceduto da un carattere di escape solo se è il primo carattere dopo lo spazio vuoto, non se è all'interno di un termine. `wi-fi`, ad esempio, è un termine singolo, mentre i GUID (ad esempio `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) vengono trattati come un singolo token.
- L'operatore suffisso `*` deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima dello spazio vuoto, non se è all'interno di un termine. `wi*fi`, ad esempio, viene considerato come un singolo token.

> [!NOTE]  
>  Anche se i caratteri di escape mantengono uniti i token, l'analisi del testo potrebbe dividerli, a seconda della modalità di analisi. Per informazioni dettagliate, vedere [supporto &#40;per&#41; la lingua di Azure ricerca cognitiva API REST](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Vedere anche  

+ [Eseguire ricerche &#40;nei documenti ricerca cognitiva API REST di Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni OData](query-odata-filter-orderby-syntax.md) 
