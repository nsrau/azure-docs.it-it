---
title: Sintassi di query semplice
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento per la sintassi di query semplice usata per le query di ricerca full-text in Ricerca cognitiva di Azure.Reference for the simple query syntax used for full-text search queries in Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258865"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Ricerca cognitiva di AzureSimple query syntax in Azure Cognitive Search

Ricerca cognitiva di Azure implementa due linguaggi di query basati su Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). 

In Ricerca cognitiva di Azure la sintassi di query semplice esclude le operazioni di ricerca fuzzy. Utilizzare invece la sintassi Lucene completa per la [ricerca fuzzy](search-query-fuzzy.md).

> [!NOTE]
> La sintassi di query semplice viene utilizzata per le espressioni di query passate nel parametro **di ricerca** dell'API [Cerca documenti,](https://docs.microsoft.com/rest/api/searchservice/search-documents) da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) utilizzata per il [parametro $filter](search-filters.md) di tale API. Queste diverse sintassi hanno regole specifiche per la costruzione di query, l'escizzazione di stringhe e così via.
>
> Ricerca cognitiva di Azure offre una sintassi di [query Lucene completa](query-lucene-syntax.md) alternativa per query più complesse nel parametro di **ricerca.** Per altre informazioni sull'architettura di analisi delle query e sui vantaggi di ogni sintassi, vedere Funzionamento della [ricerca full-text in Ricerca cognitiva](search-lucene-query-architecture.md)di Azure.To learn more about query parsing architecture and benefits of each syntax, see How full text search works in Azure Cognitive Search .

## <a name="invoke-simple-parsing"></a>Richiamare l'analisi sempliceInvoke simple parsing

La sintassi semplice è quella predefinita. La chiamata è necessaria solo se si sta reimpostando la sintassi da completa a semplice. Per impostare la sintassi in modo esplicito, usare il parametro di ricerca `queryType`. I valori `queryType=simple` `queryType=full`validi `simple` includono o , `full` dove è l'impostazione predefinita e richiama il parser di [query Lucene completo](query-lucene-syntax.md) per query più avanzate. 

## <a name="syntax-fundamentals"></a> Nozioni fondamentali sulla sintassi

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Ricerca cognitiva di Azure corrisponderà ai documenti contenenti uno o tutti i termini, incluse eventuali variazioni rilevate durante l'analisi del testo.

Per quanto semplice, esiste un aspetto dell'esecuzione delle query in Ricerca cognitiva di Azure che *potrebbe* produrre risultati imprevisti, aumentando anziché ridurre i risultati della ricerca man mano che più termini e operatori vengono aggiunti alla stringa di input. Il fatto che questa espansione si verifichi effettivamente dipende dall'inclusione di un operatore NOT, combinato con un'impostazione del parametro **searchMode** che determina il modo in cui NOT viene interpretato in termini di comportamenti AND o OR. Per altre informazioni, vedere [Operatore NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operatori di precedenza (raggruppamento)

È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi||luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

Il raggruppamento di campi è simile, ma definisce un singolo campo come ambito del raggruppamento. Ad esempio, `hotelAmenities:(gym+(wifi||pool))` cerca "gym" e "wifi" oppure "gym" e "pool" nel campo "hotelAmenities".  

### <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

Per utilizzare uno qualsiasi degli operatori di ricerca come parte del testo di ricerca,`\`eseguire l'escape del carattere anteponendolo a una singola barra rovesciata ( ). Ad esempio, per una `https://`ricerca `://` con caratteri jolly in , `search=https\:\/\/*`dove fa parte della stringa di query, è necessario specificare . Allo stesso modo, un modello di `\+1 \(800\) 642\-7676`numero di telefono di escape potrebbe essere simile a questo .

I caratteri speciali che richiedono l'escattività includono quanto segue:`- * ? \ /`  

Per semplificare i casi più comuni, sono previste due eccezioni a questa regola, in cui i caratteri di escape non sono necessari:  

+ L'operatore NOT `-` deve essere preceduto da un carattere di escape solo se è il primo carattere dopo lo spazio vuoto, non se è all'interno di un termine. Ad esempio, il GUID seguente è `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`valido senza il carattere di escape: .

+ L'operatore suffisso `*` deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima dello spazio vuoto, non se è all'interno di un termine. Ad esempio, `4*4=16` non richiede una barra rovesciata.

> [!NOTE]  
> Anche se l'espozione tiene insieme i token, [l'analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante l'indicizzazione può eliminarli. Ad esempio, l'analizzatore Lucene standard eliminerà e interromperà le parole su trattini, spazi vuoti e altri caratteri. Se sono necessari caratteri speciali nella stringa di query, potrebbe essere necessario un analizzatore che li mantiene nell'indice. Alcune opzioni includono [analizzatori del linguaggio](index-add-language-analyzers.md)naturale Microsoft , che conservano le parole sillabate, o un analizzatore personalizzato per modelli più complessi. Per ulteriori informazioni, consultate [Termini parziali, modelli e caratteri speciali.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

Assicurarsi che tutti i caratteri riservati e non sicuri siano codificati in un URL. Ad esempio, ''' è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. '&' e ''' sono esempi di caratteri riservati quando delimitiano i parametri e specificano i valori in Ricerca cognitiva di Azure.'&' and '' are examples of reserved characters as they delimit parameters and specify values in Azure Cognitive Search. Per ulteriori informazioni, vedere [RFC1738: URL (Uniform Resource Locator).](https://www.ietf.org/rfc/rfc1738.txt)

I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limiti delle dimensioni delle query

 Esiste un limite alle dimensioni delle query che è possibile inviare a Ricerca cognitiva di Azure.There is a limit to the size of queries that you can send to Azure Cognitive Search. In particolare, è possibile avere al massimo 1024 clausole (espressioni separate da AND, OR e così via). È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate.  

## <a name="boolean-search"></a>Ricerca booleana

È possibile incorporare operatori booleani (AND, OR, NOT) in una stringa di query per creare un set completo di criteri in base ai quali vengono trovati i documenti corrispondenti. 

### <a name="and-operator-"></a>Operatore AND `+`

L'operatore AND è un segno più. Ad esempio, `wifi+luxury` cercherà i documenti contenenti sia `wifi` che `luxury`.

### <a name="or-operator-"></a>Operator OR `|`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi | luxury` cercherà i documenti contenenti `wifi` o `luxury` oppure entrambi.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatore NOT `-`

L'operatore NOT è un segno meno. Ad esempio, `wifi –luxury` verrà eseguita `wifi` la ricerca di documenti `luxury`con il termine e/o non con .

Il parametro **searchMode** in una richiesta di query controlla se un termine con l'operatore NOT `+` è `|` ANDed o OR con altri termini nella query (presupponendo che non vi sia o operatore negli altri termini). I valori validi sono `any` o `all`.

`searchMode=any`aumenta il richiamo delle query includendo `-` più risultati e per impostazione predefinita verrà interpretato come "OR NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`.

`searchMode=all`aumenta la precisione delle query includendo un numero inferiore di risultati e, per impostazione predefinita, - verrà interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento verosimilmente più intuitivo per l'operatore `-`. Pertanto, è `searchMode=all` consigliabile `searchMode=any` utilizzare anziché se si desidera ottimizzare le ricerche `-` per la precisione anziché il richiamo *e* gli utenti utilizzano spesso l'operatore nelle ricerche.

Quando si decide **un'impostazione searchMode,** considerare i modelli di interazione dell'utente per le query in varie applicazioni. È più probabile che gli utenti che cercano informazioni includano un operatore in una query, a differenza dei siti di e-commerce con strutture di spostamento più incorporate.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Ricerca prefisso

L'operatore suffisso è un asterisco `*`. Ad esempio, `lingui*` troverà "linguistico" o "linguini", ignorando la distinzione tra maiuscole e minuscole. 

Analogamente ai filtri, una query di prefisso cerca una corrispondenza esatta. Di conseguenza, non vi è alcun punteggio di pertinenza (tutti i risultati ricevono un punteggio di ricerca di 1.0). Le query di prefisso possono essere lente, soprattutto se l'indice è di grandi dimensioni e il prefisso è costituito da un numero ridotto di caratteri. 

Se si desidera eseguire una query suffisso, corrispondente nell'ultima parte della stringa, utilizzare una [ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard) e la sintassi Lucene completa.

## <a name="phrase-search-"></a>Ricerca di frasi`"`

Un termine di ricerca è una query per uno o più termini, in cui uno qualsiasi dei termini è considerato una corrispondenza. La ricerca di frasi è una `" "`frase esatta racchiusa tra virgolette . Ad esempio, mentre `Roach Motel` (senza virgolette) cerca i documenti contenenti `Roach` e/o `Motel` ovunque e in qualsiasi ordine, `"Roach Motel"` (con le virgolette) troverà solo i documenti contenenti l'intera frase in quell'ordine specifico (l'analisi del testo è comunque applicabile).

## <a name="see-also"></a>Vedere anche  

+ [Esempi di query per la ricerca semplice](search-query-simple-examples.md)
+ [Esempi di query per la ricerca completa Lucene](search-query-lucene-examples.md)
+ [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni OData](query-odata-filter-orderby-syntax.md) 
