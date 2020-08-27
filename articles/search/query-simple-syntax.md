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
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923382"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintassi di query semplice in Azure ricerca cognitiva

Azure ricerca cognitiva implementa due linguaggi di query basati su Lucene: il parser di query [semplice](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e il [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Il parser semplice è più flessibile e tenterà di interpretare una richiesta anche se non è perfettamente composta. Grazie a questa flessibilità, si tratta dell'impostazione predefinita per le query in Azure ricerca cognitiva. 

La sintassi semplice viene usata per le espressioni di query passate nel `search` parametro di una [richiesta di ricerca dei documenti](/rest/api/searchservice/search-documents), da non confondere con la [sintassi OData](query-odata-filter-orderby-syntax.md) usata per il parametro [$Filter Expressions](search-filters.md) della stessa API di ricerca dei documenti. I `search` `$filter` parametri e hanno una sintassi diversa, con regole proprie per la costruzione di query, l'escape di stringhe e così via.

Sebbene il parser semplice sia basato sulla classe del [parser di query semplice di Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , l'implementazione in Azure ricerca cognitiva esclude la ricerca fuzzy. Se è necessaria la [ricerca fuzzy](search-query-fuzzy.md) o altri moduli di query avanzati, considerare invece la [sintassi di query Lucene completa](query-lucene-syntax.md) alternativa.

## <a name="invoke-simple-parsing"></a>Richiama l'analisi semplice

La sintassi semplice è quella predefinita. La chiamata è necessaria solo se si sta reimpostando la sintassi da completa a semplice. Per impostare la sintassi in modo esplicito, usare il parametro di ricerca `queryType`. I valori validi includono `queryType=simple` o `queryType=full` , dove `simple` è l'impostazione predefinita, e `full` richiama il parser di [query Lucene completo](query-lucene-syntax.md) per query più avanzate. 

## <a name="syntax-fundamentals"></a> Nozioni fondamentali sulla sintassi

Qualsiasi testo con uno o più termini è considerato un valido punto di partenza per l'esecuzione di una query. Azure ricerca cognitiva corrisponderà ai documenti che contengono uno o tutti i termini, incluse eventuali variazioni trovate durante l'analisi del testo.

Con la stessa semplicità di questa operazione, esiste un aspetto dell'esecuzione di query in Azure ricerca cognitiva che *potrebbe* produrre risultati imprevisti, aumentando invece di diminuire i risultati della ricerca, perché vengono aggiunti altri termini e operatori alla stringa di input. Il fatto che questa espansione venga effettivamente eseguita dipende dall'inclusione di un operatore NOT, combinato con un'impostazione del parametro **searchMode** che determina la modalità di interpretazione di not in termini di comportamenti e o o. Per altre informazioni, vedere [Operatore NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operatori di precedenza (raggruppamento)

È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi|luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

Il raggruppamento di campi è simile, ma definisce un singolo campo come ambito del raggruppamento. Ad esempio, `hotelAmenities:(gym+(wifi|pool))` cerca "gym" e "wifi" oppure "gym" e "pool" nel campo "hotelAmenities".  

### <a name="escaping-search-operators"></a>Escape degli operatori di ricerca  

Nella sintassi semplice, gli operatori di ricerca includono i seguenti caratteri: `+ | " ( ) ' \`  

Se uno di questi caratteri fa parte di un token nell'indice, eseguirne l'escape anteponendo una singola barra rovesciata ( `\` ) nella query. Si supponga, ad esempio, di aver usato un analizzatore personalizzato per la suddivisione in token di termini interi e che l'indice contenga la stringa "Luxury + Hotel". Per ottenere una corrispondenza esatta in questo token, inserire un carattere di escape:  `search=luxury\+hotel` . 

Per semplificare le operazioni per i casi più comuni, esistono due eccezioni a questa regola in cui l'escape non è necessario:  

+ L'operatore NOT `-` deve essere preceduto da un carattere di escape solo se è il primo carattere dopo uno spazio vuoto. Se l'oggetto `-` viene visualizzato al centro (ad esempio, in `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), è possibile ignorare l'escape.

+ L'operatore suffisso `*` deve essere preceduto da un carattere di escape solo se è l'ultimo carattere prima di uno spazio vuoto. Se l'oggetto `*` viene visualizzato al centro (ad esempio, in `4*4=16` ), non è necessario alcun escape.

> [!NOTE]  
> Per impostazione predefinita, l'analizzatore standard eliminerà e interromperà le parole su trattini, spazi vuoti, e commerciali e altri caratteri durante l' [analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se sono necessari caratteri speciali per restare nella stringa di query, potrebbe essere necessario un analizzatore che li mantiene nell'indice. Alcune opzioni includono gli [analizzatori del linguaggio](index-add-language-analyzers.md)naturale Microsoft, che conserva le parole con trattino o un analizzatore personalizzato per modelli più complessi. Per ulteriori informazioni, vedere [termini parziali, modelli e caratteri speciali](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

Assicurarsi che tutti i caratteri riservati e non sicuri siano codificati in un URL. Ad esempio,' #' è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. ' &' è =' sono esempi di caratteri riservati in quanto delimitano i parametri e specificano i valori in Azure ricerca cognitiva. Per altri dettagli, vedere [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

### <a name="querying-for-special-characters"></a>Esecuzione di query per i caratteri speciali

In alcuni casi, può essere necessario cercare un carattere speciale, ad esempio il "❤" emoji o il segno "€". In questi casi, assicurarsi che l'analizzatore usato non filtri tali caratteri.  L'analizzatore standard ignora molti dei caratteri speciali in modo che non diventino token nell'indice.

Il primo passaggio consiste quindi nell'assicurarsi di usare un analizzatore che considererà questi token di elementi. Ad esempio, l'analizzatore "spazio" prende in considerazione le sequenze di caratteri separate da spazi vuoti come token, quindi la stringa "❤" verrebbe considerata un token. Un analizzatore come Microsoft English Analyzer ("en. Microsoft"), inoltre, prenderà in considerazione la stringa "€" come token. È possibile [testare un analizzatore](/rest/api/searchservice/test-analyzer) per visualizzare i token generati per una query specifica.

Quando si usano i caratteri Unicode, assicurarsi che i simboli siano preceduti da un carattere di escape nell'URL della query (ad esempio per "❤" utilizzerà la sequenza di escape `%E2%9D%A4+` ). Postazione esegue automaticamente questa conversione.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limiti delle dimensioni delle query

 È previsto un limite per le dimensioni delle query che è possibile inviare al ricerca cognitiva di Azure. In particolare, è possibile avere al massimo 1024 clausole (espressioni separate da AND, OR e così via). È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate.  

## <a name="boolean-search"></a>Ricerca booleana

È possibile incorporare gli operatori booleani (AND, OR e NOT) in una stringa di query per creare un set completo di criteri rispetto a cui vengono trovati i documenti corrispondenti. 

### <a name="and-operator-"></a>Operatore AND `+`

L'operatore AND è un segno più. Ad esempio, `wifi + luxury` cercherà i documenti contenenti sia `wifi` che `luxury`.

### <a name="or-operator-"></a>Operator OR `|`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi | luxury` cercherà i documenti contenenti `wifi` o `luxury` oppure entrambi.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operatore NOT `-`

L'operatore NOT è un segno meno. Ad esempio, `wifi –luxury` cercherà i documenti che hanno il `wifi` termine e/o non hanno `luxury` .

Il parametro **searchMode** su una richiesta di query controlla se un termine con l'operatore Not è individuato o ORed con altri termini nella query (presupponendo che non esista alcun `+` operatore OR negli `|` altri termini). I valori validi sono `any` o `all`.

`searchMode=any` aumenta il richiamo delle query includendo più risultati e per impostazione predefinita `-` viene interpretato come "o not". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` o quelli non contenenti il termine `luxury`.

`searchMode=all` aumenta la precisione delle query includendo un minor numero di risultati e, per impostazione predefinita, viene interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine "luxury". Si tratta di un comportamento verosimilmente più intuitivo per l'operatore `-`. Pertanto, è consigliabile utilizzare `searchMode=all` anziché `searchMode=any` se si desidera ottimizzare la ricerca di precisione anziché richiamare *e* gli utenti utilizzano spesso l' `-` operatore nelle ricerche.

Quando si decide di utilizzare un'impostazione di **searchMode** , considerare i modelli di interazione utente per le query in varie applicazioni. È più probabile che gli utenti che eseguono la ricerca di informazioni includano un operatore in una query, anziché i siti di e-commerce che dispongono di più strutture di navigazione predefinite.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Corrispondenza del prefisso con caratteri jolly (*,?)

Per le query "inizia con" aggiungere un operatore di suffisso come segnaposto per il resto di un termine. Usare un asterisco `*` per più caratteri o `?` per i singoli caratteri. Ad esempio, `lingui*` corrisponderà a "linguistico" o "linguinei", ignorando la distinzione tra maiuscole e minuscole. 

Analogamente ai filtri, una query di prefisso cerca una corrispondenza esatta. Di conseguenza, non esiste alcun punteggio di pertinenza (tutti i risultati ricevono un punteggio di ricerca di 1,0). Tenere presente che le query di prefisso possono essere lente, soprattutto se l'indice è di grandi dimensioni e il prefisso è costituito da un numero limitato di caratteri. Una metodologia alternativa, ad esempio la suddivisione in token n-grammi perimetrale, potrebbe risultare più rapida.

Per altre varianti di query con caratteri jolly, ad esempio il suffisso o la corrispondenza degli infissi rispetto alla fine o al centro di un termine, usare la [sintassi Lucene completa per la ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Ricerca frasi `"`

Una ricerca di termini è una query per uno o più termini, in cui uno dei termini viene considerato una corrispondenza. Una frase di ricerca è una frase esatta racchiusa tra virgolette `" "` . Ad esempio, se `Roach Motel` (senza virgolette) Cerca documenti contenenti `Roach` e/o `Motel` in qualsiasi punto in qualsiasi ordine, `"Roach Motel"` (con virgolette) corrisponderà solo ai documenti che contengono l'intera frase e in questo ordine (l'analisi lessicale si applica comunque).

## <a name="see-also"></a>Vedere anche  

+ [Funzionamento della ricerca full-text in Ricerca cognitiva di Azure](search-lucene-query-architecture.md)
+ [Esempi di query per la ricerca semplice](search-query-simple-examples.md)
+ [Esempi di query per la ricerca Lucene completa](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents) (API REST di Ricerca di documenti)
+ [Sintassi di query Lucene](query-lucene-syntax.md)
+ [Sintassi delle espressioni OData](query-odata-filter-orderby-syntax.md)