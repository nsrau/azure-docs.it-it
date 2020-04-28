---
title: Sintassi di query semplice
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento per la sintassi di query semplice utilizzata per le query di ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194942"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Azure ricerca cognitiva

Azure ricerca cognitiva implementa due linguaggi di query basati su Lucene: il parser di query [semplice](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e il [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Il parser semplice è più flessibile e tenterà di interpretare una richiesta anche se non è perfettamente composta. Grazie a questa flessibilità, si tratta dell'impostazione predefinita per le query in Azure ricerca cognitiva. 

La sintassi semplice viene usata per le espressioni di query passate `search` nel parametro di una [richiesta di ricerca dei documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents), da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) usata per il parametro [$Filter Expressions](search-filters.md) della stessa API di ricerca dei documenti. I `search` parametri `$filter` e hanno una sintassi diversa, con regole proprie per la costruzione di query, l'escape di stringhe e così via.

Sebbene il parser semplice sia basato sulla classe del [parser di query semplice di Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , l'implementazione in Azure ricerca cognitiva esclude la ricerca fuzzy. Se è necessaria la [ricerca fuzzy](search-query-fuzzy.md) o altri moduli di query avanzati, considerare invece la [sintassi di query Lucene completa](query-lucene-syntax.md) alternativa.

## <a name="invoke-simple-parsing"></a>Richiama l'analisi semplice

La sintassi semplice è quella predefinita. La chiamata è necessaria solo se si sta reimpostando la sintassi da completa a semplice. Per impostare la sintassi in modo esplicito, usare il parametro di ricerca `queryType`. I valori validi `queryType=simple` includono `queryType=full`o, `simple` dove è l'impostazione predefinita `full` , e richiama il [parser di query Lucene completo](query-lucene-syntax.md) per query più avanzate. 

## <a name="syntax-fundamentals"></a> Nozioni fondamentali sulla sintassi

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Azure ricerca cognitiva corrisponderà ai documenti che contengono uno o tutti i termini, incluse eventuali variazioni trovate durante l'analisi del testo.

Con la stessa semplicità di questa operazione, esiste un aspetto dell'esecuzione di query in Azure ricerca cognitiva che *potrebbe* produrre risultati imprevisti, aumentando invece di diminuire i risultati della ricerca, perché vengono aggiunti altri termini e operatori alla stringa di input. Il fatto che questa espansione venga effettivamente eseguita dipende dall'inclusione di un operatore NOT, combinato con un'impostazione del parametro **searchMode** che determina la modalità di interpretazione di not in termini di comportamenti e o o. Per altre informazioni, vedere [Operatore NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operatori di precedenza (raggruppamento)

È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi|luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

Il raggruppamento di campi è simile, ma definisce un singolo campo come ambito del raggruppamento. Ad esempio, `hotelAmenities:(gym+(wifi|pool))` cerca "gym" e "wifi" oppure "gym" e "pool" nel campo "hotelAmenities".  

### <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

Nella sintassi semplice, gli operatori di ricerca includono i seguenti caratteri:`+ | " ( ) ' \`  

Se uno di questi caratteri fa parte di un token nell'indice, eseguirne l'escape anteponendo una singola barra rovesciata (`\`) nella query. Si supponga, ad esempio, di aver usato un analizzatore personalizzato per la suddivisione in token di termini interi e che l'indice contenga la stringa "Luxury + Hotel". Per ottenere una corrispondenza esatta in questo token, inserire un carattere di escape `search=luxury\+hotel`:. 

Per semplificare le operazioni per i casi più comuni, esistono due eccezioni a questa regola in cui l'escape non è necessario:  

+ L'operatore `-` not deve essere preceduto da un carattere di escape solo se è il primo carattere dopo uno spazio vuoto. Se l' `-` oggetto viene visualizzato al centro (ad esempio, `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`in), è possibile ignorare l'escape.

+ L'operatore `*` suffisso deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima di uno spazio vuoto. Se l' `*` oggetto viene visualizzato al centro (ad esempio, `4*4=16`in), non è necessario alcun escape.

> [!NOTE]  
> Per impostazione predefinita, l'analizzatore standard eliminerà e interromperà le parole su trattini, spazi vuoti, e commerciali e altri caratteri durante l' [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se sono necessari caratteri speciali per restare nella stringa di query, potrebbe essere necessario un analizzatore che li mantiene nell'indice. Alcune opzioni includono gli [analizzatori del linguaggio](index-add-language-analyzers.md)naturale Microsoft, che conserva le parole con trattino o un analizzatore personalizzato per modelli più complessi. Per ulteriori informazioni, vedere [termini parziali, modelli e caratteri speciali](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

Assicurarsi che tutti i caratteri riservati e non sicuri siano codificati in un URL. Ad esempio,' #' è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. ' &' è =' sono esempi di caratteri riservati in quanto delimitano i parametri e specificano i valori in Azure ricerca cognitiva. Per altri dettagli, vedere [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limiti delle dimensioni delle query

 È previsto un limite per le dimensioni delle query che è possibile inviare al ricerca cognitiva di Azure. In particolare, è possibile avere al massimo 1024 clausole (espressioni separate da AND, OR e così via). È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate.  

## <a name="boolean-search"></a>Ricerca booleana

È possibile incorporare gli operatori booleani (AND, OR e NOT) in una stringa di query per creare un set completo di criteri rispetto a cui vengono trovati i documenti corrispondenti. 

### <a name="and-operator-"></a>Operatore AND `+`

L'operatore AND è un segno più. Ad esempio, `wifi + luxury` cercherà i documenti contenenti sia `wifi` che `luxury`.

### <a name="or-operator-"></a>Operator OR `|`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi | luxury` cercherà i documenti contenenti `wifi` o `luxury` oppure entrambi.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatore NOT `-`

L'operatore NOT è un segno meno. Ad esempio, `wifi –luxury` cercherà i documenti che hanno il `wifi` termine e/o non hanno `luxury`.

Il parametro **searchMode** su una richiesta di query controlla se un termine con l'operatore Not è individuato o ORed con altri termini nella query (presupponendo che `+` non `|` esista alcun operatore OR negli altri termini). I valori validi sono `any` o `all`.

`searchMode=any`aumenta il richiamo delle query includendo più risultati e per impostazione predefinita `-` viene interpretato come "o not". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`.

`searchMode=all`aumenta la precisione delle query includendo un minor numero di risultati e, per impostazione predefinita, viene interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento verosimilmente più intuitivo per l'operatore `-`. Pertanto, è consigliabile `searchMode=all` utilizzare anziché `searchMode=any` se si desidera ottimizzare la ricerca di precisione anziché richiamare *e* gli utenti utilizzano spesso l' `-` operatore nelle ricerche.

Quando si decide di utilizzare un'impostazione di **searchMode** , considerare i modelli di interazione utente per le query in varie applicazioni. È più probabile che gli utenti che eseguono la ricerca di informazioni includano un operatore in una query, anziché i siti di e-commerce che dispongono di più strutture di navigazione predefinite.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Cerca prefisso

L'operatore suffisso è un `*`asterisco. Ad esempio, `lingui*` troverà "linguistico" o "linguinei", ignorando la distinzione tra maiuscole e minuscole. 

Analogamente ai filtri, una query di prefisso cerca una corrispondenza esatta. Di conseguenza, non esiste alcun punteggio di pertinenza (tutti i risultati ricevono un punteggio di ricerca di 1,0). Le query di prefisso possono essere lente, soprattutto se l'indice è di grandi dimensioni e il prefisso è costituito da un numero limitato di caratteri. 

Se si vuole eseguire una query con suffisso, che corrisponde all'ultima parte della stringa, usare una [ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard) e la sintassi Lucene completa.

## <a name="phrase-search-"></a>Ricerca frasi`"`

Una ricerca di termini è una query per uno o più termini, in cui uno dei termini viene considerato una corrispondenza. Una frase di ricerca è una frase esatta racchiusa tra `" "`virgolette. Ad esempio, mentre `Roach Motel` (senza virgolette) cerca i documenti contenenti `Roach` e/o `Motel` ovunque e in qualsiasi ordine, `"Roach Motel"` (con le virgolette) troverà solo i documenti contenenti l'intera frase in quell'ordine specifico (l'analisi del testo è comunque applicabile).

## <a name="see-also"></a>Vedere anche  

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Esempi di query per la ricerca semplice](search-query-simple-examples.md)
+ [Esempi di query per la ricerca Lucene completa](search-query-lucene-examples.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (API REST di Ricerca di documenti)
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni OData](query-odata-filter-orderby-syntax.md) 
