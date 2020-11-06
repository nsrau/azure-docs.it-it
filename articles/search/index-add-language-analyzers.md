---
title: Aggiungere analizzatori di lingua ai campi stringa
titleSuffix: Azure Cognitive Search
description: Analisi lessicale multilingue per query e indici non in lingua inglese in ricerca cognitiva di Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: e763dbd15ea443ad3c8f6295b37999c748db7e6c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422332"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Aggiungere analizzatori di lingua ai campi stringa in un indice di ricerca cognitiva di Azure

L' *analizzatore del linguaggio* è un tipo specifico di [analizzatore di testo](search-analyzers.md) che esegue l'analisi lessicale usando le regole linguistiche della lingua di destinazione. Ogni campo che consente la ricerca ha una proprietà **analyzer**. Se il contenuto è costituito da stringhe tradotte, ad esempio campi separati per testo in lingua inglese e cinese, è possibile specificare gli analizzatori di lingua in ogni campo per accedere alle funzionalità linguistiche avanzate degli analizzatori.

## <a name="when-to-use-a-language-analyzer"></a>Quando usare un analizzatore del linguaggio

È consigliabile prendere in considerazione un analizzatore del linguaggio quando il riconoscimento della struttura di parole o frasi aggiunge valore all'analisi del testo. Un esempio comune è costituito dall'associazione di forme di verbi irregolari ("Bring" e "Bring" o plurale ("mice" e "mouse"). Senza la consapevolezza linguistica, queste stringhe vengono analizzate solo sulle caratteristiche fisiche, che non riescono a intercettare la connessione. Poiché è più probabile che i blocchi di testo di grandi dimensioni abbiano questo contenuto, i campi costituiti da descrizioni, revisioni o riepiloghi sono validi candidati per un analizzatore di linguaggio.

È inoltre consigliabile considerare gli analizzatori del linguaggio quando il contenuto è costituito da stringhe di lingua non occidentali. Mentre l' [analizzatore predefinito](search-analyzers.md#default-analyzer) è indipendente dalla lingua, il concetto di uso di spazi e caratteri speciali (trattini e barre) per separare le stringhe tende a essere più applicabile alle lingue occidentali rispetto a quelle non occidentali. 

Ad esempio, in cinese, giapponese, coreano (CJK) e in altre lingue asiatiche, uno spazio non è necessariamente un delimitatore di parola. Si consideri la seguente stringa giapponese. Poiché non contiene spazi, un analizzatore indipendente dalla lingua analizza probabilmente l'intera stringa come un token, quando in realtà la stringa è effettivamente una frase.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Per l'esempio precedente, una query con esito positivo dovrebbe includere il token completo o un token parziale usando un carattere jolly del suffisso, causando un'esperienza di ricerca non naturale e limitante.

Un'esperienza migliore consiste nel cercare singole parole: 明るい (Bright), 私たちの (Our), 銀河系 (Galaxy). L'uso di uno degli analizzatori giapponesi disponibili in ricerca cognitiva è più probabile per sbloccare questo comportamento, perché questi analizzatori sono più idonei a suddividere il blocco di testo in parole significative nella lingua di destinazione.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Confronto tra Lucene e analizzatori Microsoft

Azure ricerca cognitiva supporta gli analizzatori di lingua 35 supportati da Lucene e gli analizzatori di linguaggio 50 supportati dalla tecnologia di elaborazione del linguaggio naturale Microsoft usata in Office e Bing.

Alcuni sviluppatori potrebbero preferire la soluzione open source di Lucene, più semplice e familiare. Gli analizzatori del linguaggio Lucene sono più veloci, ma gli analizzatori Microsoft dispongono di funzionalità avanzate, ad esempio lemmatizzazione, scomposizione delle parole (in lingue come tedesco, danese, olandese, svedese, norvegese, estone, finlandese, ungherese, slovacco) e riconoscimento di entità (URL, messaggi di posta elettronica, date, numeri). Si consiglia di confrontare, se possibile, gli analizzatori Microsoft e Lucene per scegliere la soluzione più adatta al proprio caso. 

L'indicizzazione con gli analizzatori Microsoft è in media da due a tre volte più lenta rispetto agli analizzatori Lucene corrispondenti, a seconda della lingua. Le prestazioni della ricerca non devono essere influenzate in modo significativo per le query di dimensioni medie. 

### <a name="english-analyzers"></a>Analizzatori per la lingua inglese

L'analizzatore predefinito è Lucene standard, che funziona bene per la lingua inglese, ma probabilmente non altrettanto quanto l'analizzatore per la lingua inglese Lucene o l'analizzatore per la lingua inglese di Microsoft. 
 
+ L'analizzatore per la lingua inglese Lucene estende l'analizzatore standard. Rimuove il genitivo sassone (la 's finale) dalle parole, applica lo stemming in base all'algoritmo Porter Stemming e rimuove le parole non significative per la lingua inglese.  

+ L'analizzatore per la lingua inglese Microsoft esegue la lemmatizzazione anziché lo stemming. Ciò significa che è in grado di gestire in modo molto migliore i form di Word flessivi e irregolari, con risultati di ricerca più rilevanti 

## <a name="configuring-analyzers"></a>Configurazione degli analizzatori

Gli analizzatori di lingua vengono usati così come sono. Per ogni campo nella definizione dell'indice, è possibile impostare la proprietà **analyzer** su un nome di analizzatore che specifica la lingua e lo stack linguistico (Microsoft o Lucene). Lo stesso analizzatore verrà applicato per l'indicizzazione e la ricerca di tale campo. Ad esempio, nello stesso indice possono essere presenti campi separati per le descrizioni di hotel in lingua inglese, francese e spagnola.

> [!NOTE]
> Non è possibile utilizzare un analizzatore del linguaggio diverso in fase di indicizzazione rispetto al tempo di query per un campo. Questa funzionalità è riservata agli [analizzatori personalizzati](index-add-custom-analyzers.md). Per questo motivo, se si tenta di impostare le proprietà **searchAnalyzer** o **indexAnalyzer** sul nome di un analizzatore del linguaggio, l'API REST restituirà una risposta di errore. In alternativa, è necessario utilizzare la proprietà **Analyzer** .

Usare il parametro di query **searchFields** per indicare il campo specifico della lingua da ricercare nelle query. È possibile esaminare gli esempi di query che includono la proprietà Analyzer nei [documenti di ricerca](/rest/api/searchservice/search-documents). 

Per altre informazioni sulle proprietà degli indici, vedere [creare un indice &#40;API REST di Azure ricerca cognitiva&#41;](/rest/api/searchservice/create-index). Per altre informazioni sull'analisi in Azure ricerca cognitiva, vedere [analizzatori in azure ricerca cognitiva](./search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Elenco di analizzatori di lingua 
 Di seguito è riportato l'elenco delle lingue supportate con i nomi degli analizzatori Lucene e Microsoft.  

|Lingua|Nome analizzatore Microsoft|Nome analizzatore Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabo|ar.microsoft|ar.lucene|  
|Armeno||hy.lucene|  
|Bengalese|bn.microsoft||  
|Basco||eu.lucene|  
|Bulgaro|bg.microsoft|bg.lucene|  
|Catalano|ca.microsoft|ca.lucene|  
|Cinese semplificato|zh-Hans.microsoft|zh-Hans.lucene|  
|Cinese tradizionale|zh-Hant.microsoft|zh-Hant.lucene|  
|Croato|hr.microsoft||  
|Ceco|cs.microsoft|cs.lucene|  
|Danese|da.microsoft|da.lucene|  
|Olandese|nl.microsoft|nl.lucene|  
|Inglese|en.microsoft|en.lucene|  
|Estone|et.microsoft||  
|Finlandese|fi.microsoft|fi.lucene|  
|Francese|fr.microsoft|fr.lucene|  
|Galiziano||gl.lucene|  
|Tedesco|de.microsoft|de.lucene|  
|Greco|el.microsoft|el.lucene|  
|Gujarati|gu.microsoft||  
|Ebraico|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Ungherese|hu.microsoft|hu.lucene|  
|Islandese|is.microsoft||  
|Indonesiano (Bahasa)|id.microsoft|id.lucene|  
|Irlandese||ga.lucene|  
|Italiano|it.microsoft|it.lucene|  
|Giapponese|ja.microsoft|ja.lucene|  
|Kannada|kN. Microsoft||  
|Coreano|ko.Microsoft|ko.lucene|  
|Lettone|lv.microsoft|lv.lucene|  
|Lituano|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malese (alfabeto latino)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norvegese|nb.microsoft|no.lucene|  
|Persiano||fa.lucene|  
|Polacco|pl.microsoft|pl.lucene|  
|Portoghese (Brasile)|pt-Br.microsoft|pt-Br.lucene|  
|Portoghese (Portogallo)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Romeno|ro.microsoft|ro.lucene|  
|Russo|ru.microsoft|ru.lucene|  
|Serbo (alfabeto cirillico)|sr-cyrillic.microsoft||  
|Serbo (alfabeto latino)|sr-latin.microsoft||  
|Slovacco|sk.microsoft||  
|Sloveno|sl.microsoft||  
|Spagnolo|es.microsoft|es.lucene|  
|Svedese|sv.microsoft|sv.lucene|  
|Tamil|ta.microsoft||  
|Telugu|te.microsoft||  
|Thai|th.microsoft|th.lucene|  
|Turco|tr.microsoft|tr.lucene|  
|Ucraino|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamita|vi.microsoft||  

 Tutti gli analizzatori con nomi contenenti la parola **Lucene** sono basati sugli [analizzatori del linguaggio Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Vedere anche  

+ [Creare l'indice &#40;API REST di Azure ricerca cognitiva&#41;](/rest/api/searchservice/create-index)  

+ [Classe LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)