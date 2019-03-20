---
title: Aggiungere analizzatori del linguaggio - Ricerca di Azure
description: Analisi del testo lessicale multilingue per gli indici e le query in lingua diversa dall'inglese in Ricerca di Azure.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: 9584f0ee4264724787c5913c0d6baa4c2769fa82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085678"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Aggiungere analizzatori del linguaggio a un indice di Ricerca di Azure

L'*analizzatore del linguaggio* è un tipo specifico di [analizzatore di testo](search-analyzers.md) che esegue l'analisi lessicale usando le regole linguistiche della lingua di destinazione. Ogni campo che consente la ricerca ha una proprietà **analyzer**. Se l'indice contiene stringhe tradotte, ad esempio campi separati per un testo in lingua inglese e cinese, è possibile specificare gli analizzatori del linguaggio in ogni campo per accedere alle funzionalità linguistiche avanzate di tali analizzatori.  

Ricerca di Azure supporta 35 analizzatori basati sulla tecnologia Lucene e 50 sono basati sulla tecnologia proprietaria di Microsoft per l'elaborazione del linguaggio naturale usata in Office e Bing.

## <a name="comparing-analyzers"></a>Confronto tra analizzatori

Alcuni sviluppatori potrebbero preferire la soluzione open source di Lucene, più semplice e familiare. Gli analizzatori del linguaggio Lucene sono più veloci, ma gli analizzatori Microsoft dispongono di funzionalità avanzate, ad esempio lemmatizzazione, scomposizione delle parole (in lingue come tedesco, danese, olandese, svedese, norvegese, estone, finlandese, ungherese, slovacco) e riconoscimento di entità (URL, messaggi di posta elettronica, date, numeri). Si consiglia di confrontare, se possibile, gli analizzatori Microsoft e Lucene per scegliere la soluzione più adatta al proprio caso. 

L'indicizzazione con gli analizzatori Microsoft è in media da due a tre volte più lenta rispetto agli analizzatori Lucene corrispondenti, a seconda della lingua. Le prestazioni della ricerca non devono essere influenzate in modo significativo per le query di dimensioni medie. 

### <a name="english-analyzers"></a>Analizzatori per la lingua inglese

L'analizzatore predefinito è Lucene standard, che funziona bene per la lingua inglese, ma probabilmente non altrettanto quanto l'analizzatore per la lingua inglese Lucene o l'analizzatore per la lingua inglese di Microsoft. 
 
+ L'analizzatore per la lingua inglese Lucene estende l'analizzatore standard. Rimuove il genitivo sassone (la 's finale) dalle parole, applica lo stemming in base all'algoritmo Porter Stemming e rimuove le parole non significative per la lingua inglese.  

+ L'analizzatore per la lingua inglese Microsoft esegue la lemmatizzazione anziché lo stemming. Significa che è possibile gestire le forme lessicali flesse e irregolari in modo decisamente migliore, producendo risultati di ricerca più rilevanti 

  > [!Tip]
  > La [Search Analyzer Demo](https://alice.unearth.ai/) fornisce un confronto side-by-side dei risultati prodotti dall'analizzatore Lucene standard, dall'analizzatore di lingua inglese Lucene e dal processore di lingua inglese di Microsoft. Per ogni input di ricerca fornito, i risultati di ogni analizzatore vengono visualizzati nei riquadri adiacenti.

## <a name="configuring-analyzers"></a>Configurazione degli analizzatori

Gli analizzatori di lingua vengono usati così come sono. Per ogni campo nella definizione dell'indice, è possibile impostare la proprietà **analyzer** su un nome di analizzatore che specifica la lingua e lo stack linguistico (Microsoft o Lucene). Lo stesso analizzatore verrà applicato per l'indicizzazione e la ricerca di tale campo. Ad esempio, nello stesso indice possono essere presenti campi separati per le descrizioni di hotel in lingua inglese, francese e spagnola. In alternativa, al posto di **analyzer**, è possibile usare **indexAnalyzer** e **searchAnalyzer** per disporre di regole di analisi diverse in fase di indicizzazione e di query. 

Usare il parametro di query **searchFields** per indicare il campo specifico della lingua da ricercare nelle query. È possibile esaminare gli esempi di query che includono la proprietà analyzer in [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Eseguire ricerche nei documenti). 

Per altre informazioni sulle proprietà di indice, vedere [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)(Creare indice API REST per il servizio Ricerca di Azure). Per altre informazioni sull'analisi in Ricerca di Azure, vedere [Analizzatori per elaborazione del testo in Ricerca di Azure](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Elenco di analizzatori di lingua 
 Di seguito è riportato l'elenco delle lingue supportate con i nomi degli analizzatori Lucene e Microsoft.  

|Linguaggio|Nome analizzatore Microsoft|Nome analizzatore Lucene|  
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
|Kannada|ka.microsoft||  
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
|Rumeno|ro.microsoft|ro.lucene|  
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

 Tutti gli analizzatori con nomi contenenti la parola **Lucene** sono basati sugli [analizzatori del linguaggio Apache Lucene](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Vedere anche   
 [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creare indice API REST per il servizio Ricerca di Azure)  
 [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Video: module 7 of Azure Search MVA presentation](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07) (Video: modulo 7 di presentazione di Microsoft Virtual Academy su Ricerca di Azure).  

