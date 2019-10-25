---
title: Sintassi di query Lucene
titleSuffix: Azure Cognitive Search
description: Riferimento per la sintassi Lucene completa, come usato con ricerca cognitiva di Azure.
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
ms.openlocfilehash: 1b94a1bbab810345ab222be9e7aba2fef0f52549
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786275"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintassi di query Lucene in Azure ricerca cognitiva

È possibile scrivere query su Azure ricerca cognitiva in base alla sintassi avanzata del [parser di query Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) per i moduli di query specializzati: i caratteri jolly, la ricerca fuzzy, la ricerca di prossimità, le espressioni regolari sono alcuni esempi. Gran parte della sintassi del parser di query Lucene viene [implementata in modo intatto in azure ricerca cognitiva](search-lucene-query-architecture.md), ad eccezione delle *ricerche di intervallo* costruite in Azure ricerca cognitiva tramite espressioni di `$filter`. 

## <a name="how-to-invoke-full-parsing"></a>Come richiamare l'analisi completa

Impostare il parametro di ricerca `queryType` per specificare il parser da usare. I valori validi includono `simple|full`, con `simple` come impostazione predefinita e `full` per Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Esempio di sintassi completa

L'esempio seguente trova documenti nell'indice usando la sintassi di query Lucene, come risulta dal parametro `queryType=full`. Questa query restituisce gli hotel in cui il campo categoria contiene il termine "budget" e tutti i campi disponibili per la ricerca contengono la frase "recently renovated". I documenti contenenti la frase "recently renovated" avranno una posizione superiore nella classifica, come risultato del valore di incremento dell'importanza di un termine (3).  

Il parametro `searchMode=all` è rilevante in questo esempio. Quando nella query sono presenti operatori, è in genere consigliabile impostare `searchMode=all` per assicurarsi che venga trovata una corrispondenza per *tutti* i criteri.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 In alternativa, usare POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Per altri esempi, vedere [esempi di sintassi di query Lucene per la compilazione di query in Azure ricerca cognitiva](search-query-lucene-examples.md). Per informazioni dettagliate su come specificare l'intero contingente dei parametri di query, vedere [eseguire ricerche nei documenti &#40;Azure&#41;ricerca cognitiva API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure ricerca cognitiva supporta anche una [semplice sintassi di query](query-simple-syntax.md), un linguaggio di query semplice e affidabile che può essere usato per la semplice ricerca di parole chiave.  

##  <a name="bkmk_syntax"></a> Nozioni fondamentali sulla sintassi  
 Le nozioni fondamentali seguenti sulla sintassi si applicano a tutte le query che usano la sintassi Lucene.  

### <a name="operator-evaluation-in-context"></a>Valutazione degli operatori nel contesto

Il posizionamento determina se un simbolo viene interpretato come operatore o semplicemente come un altro carattere in una stringa.

Nella sintassi Lucene completa, ad esempio, la tilde (~) viene usata sia per la ricerca fuzzy che per la ricerca per prossimità. Quando viene inserita dopo una frase tra virgolette, ~ richiama la ricerca per prossimità. Quando viene inserita alla fine di un termine, ~ richiama la ricerca fuzzy.

All'interno di un termine, ad esempio "business~analyst", il carattere non viene valutato come operatore. In questo caso, supponendo che la query sia relativa a un termine o a una frase, la [ricerca full-text](search-lucene-query-architecture.md) con l'[analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) elimina il carattere ~ e suddivide il termine "business~analyst" in due: business OR analyst.

L'esempio precedente è relativo alla tilde (~), ma lo stesso principio si applica a ogni operatore.

### <a name="escaping-special-characters"></a>Escape dei caratteri speciali

 I caratteri speciali devono essere preceduti da un carattere di escape per poter essere usati come parte del testo di ricerca. Per usare i caratteri di escape, anteporre la barra rovesciata (\\). I caratteri speciali che devono essere preceduti da un carattere di escape includono i seguenti:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Per eseguire l'escape di un carattere jolly, ad esempio, utilizzare \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codifica dei caratteri riservati e non sicuri negli URL

 Assicurarsi che tutti i caratteri riservati e non sicuri siano codificati in un URL. "#", ad esempio, è un carattere non sicuro perché è un identificatore di frammento/ancoraggio in un URL. Il carattere deve essere codificato al `%23`, se usato in un URL. ' &' è =' sono esempi di caratteri riservati in quanto delimitano i parametri e specificano i valori in Azure ricerca cognitiva. Per altri dettagli, vedere [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

 I caratteri non sicuri sono ``" ` < > # % { } | \ ^ ~ [ ]``. I caratteri riservati sono `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operatori di precedenza: raggruppamento e raggruppamento di campi  
 È possibile usare le parentesi per creare sottoquery, inclusi gli operatori nell'istruzione tra parentesi. Ad esempio, `motel+(wifi||luxury)` cercherà i documenti contenenti i termini "motel" e "wifi" o "luxury" (oppure entrambi).

Il raggruppamento di campi è simile, ma definisce un singolo campo come ambito del raggruppamento. Ad esempio, `hotelAmenities:(gym+(wifi||pool))` cerca "gym" e "wifi" oppure "gym" e "pool" nel campo "hotelAmenities".  

### <a name="searchmode-parameter-considerations"></a>Considerazioni sul parametro SearchMode  
 L'effetto di `searchMode` sulle query, come descritto in [sintassi di query semplice in Azure ricerca cognitiva](query-simple-syntax.md), si applica ugualmente alla sintassi di query Lucene. In particolare, `searchMode` insieme agli operatori NOT può restituire risultati di query che potrebbero sembrare insoliti se non è chiaro quali siano le implicazioni dell'impostazione del parametro. Se si mantiene l'impostazione predefinita `searchMode=any` e si usa un operatore NOT, l'operazione viene calcolata come azione OR e quindi "New York" NOT "Seattle" restituisce tutte le città diverse da Seattle.  

##  <a name="bkmk_boolean"></a>Operatori booleani (AND, OR, NOT) 
 Specificare sempre gli operatori booleani di testo (AND, OR, NOT) in lettere tutte maiuscole.  

### <a name="or-operator-or-or-"></a>Operatore OR `OR` o `||`

L'operatore OR è un carattere barra verticale o pipe. Ad esempio, `wifi || luxury` cercherà i documenti contenenti "wifi" o "luxury" oppure entrambi. Poiché OR è l'operatore di congiunzione predefinito, è anche possibile escluderlo, in modo che `wifi luxury` sia l'equivalente di `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>Operatore AND `AND`, `&&` o `+`

L'operatore AND è una e commerciale o un segno più. Ad esempio, `wifi && luxury` cercherà i documenti contenenti sia "wifi" che "luxury". Il segno più (+) viene usato per i termini obbligatori. Ad esempio, `+wifi +luxury` stabilisce che entrambi i termini devono essere presenti nel campo di un singolo documento.


### <a name="not-operator-not--or--"></a>Operatore NOT `NOT`, `!` o `-`

L'operatore NOT è un punto esclamativo o il segno meno. Ad esempio, `wifi !luxury` cercherà i documenti che contengono il termine "wifi" e/o non contengono "luxury". L'opzione `searchMode` controlla se un termine con l'operatore NOT è associato tramite l'operatore AND o OR agli altri termini nella query in assenza di un operatore + o ||. Si ricordi che `searchMode` può essere impostato su `any` (impostazione predefinita) o su `all`.

L'uso di `searchMode=any` aumenta il livello di richiamo delle query includendo più risultati e per impostazione predefinita il segno - verrà interpretato come "OR NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine *wifi* o quelli non contenenti il termine *luxury*.

L'uso di `searchMode=all` aumenta il livello di precisione delle query includendo meno risultati e per impostazione predefinita il segno - verrà interpretato come "AND NOT". Ad esempio, `wifi -luxury` troverà la corrispondenza con documenti contenenti il termine `wifi` e quelli non contenenti il termine `luxury`. Si tratta di un comportamento verosimilmente più intuitivo per l'operatore -. Valutare quindi l'opportunità di scegliere `searchMode=all` invece di `searchMode=any` se si vuole ottimizzare il livello di precisione delle ricerche invece che quello di richiamo *e* gli utenti usano spesso l'operatore `-` nelle ricerche.

##  <a name="bkmk_querysizelimits"></a> Limitazioni relative alla dimensione delle query  
 È previsto un limite per le dimensioni delle query che è possibile inviare al ricerca cognitiva di Azure. In particolare, è possibile avere al massimo 1024 clausole (espressioni separate da AND, OR e così via). È previsto anche un limite di circa 32 KB per la dimensione di ogni singolo termine di una query. Se l'applicazione genera query di ricerca a livello di codice, è consigliabile progettarla in modo che non generi query di dimensioni illimitate.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Punteggio delle query con caratteri jolly e regex
 Azure ricerca cognitiva usa il punteggio in base alla frequenza ([tf-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) per le query di testo. Per le query con caratteri jolly e regex, in cui l'ambito dei termini può essere potenzialmente ampio, il fattore frequenza viene tuttavia ignorato per evitare che la classificazione privilegi le corrispondenze con termini più rari. Tutte le corrispondenze vengono trattate equamente per le ricerche con caratteri jolly e regex.

##  <a name="bkmk_fields"></a>Ricerca nel campo  
È possibile definire un'operazione di ricerca in campo con la sintassi `fieldName:searchExpression`, in cui l'espressione di ricerca può essere una singola parola o una frase o un'espressione più complessa tra parentesi, facoltativamente con gli operatori booleani. Ecco alcuni esempi:  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

Assicurarsi di inserire più stringhe racchiuse tra virgolette se si vuole che entrambe le stringhe siano valutate come una singola entità, in questo caso per la ricerca di due artisti distinti nel campo `artists`.  

Il campo specificato in `fieldName:searchExpression` deve essere un campo `searchable`.  Per informazioni dettagliate sull'uso di attributi dell'indice nelle definizioni campo, vedere [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creare l'indice).  

> [!NOTE]
> Quando si usano le espressioni di ricerca nel campo, non è necessario usare il parametro `searchFields` perché ogni espressione di ricerca campo ha un nome di campo specificato in modo esplicito. Tuttavia, è comunque possibile utilizzare il parametro `searchFields` se si desidera eseguire una query in cui alcune parti hanno come ambito un campo specifico e il resto può essere applicato a più campi. Il `search=genre:jazz NOT history&searchFields=description` di query, ad esempio, corrisponde `jazz` solo al campo `genre`, mentre corrisponde `NOT history` con il campo `description`. Il nome del campo fornito in `fieldName:searchExpression` ha sempre la precedenza sul parametro `searchFields`, motivo per cui in questo esempio non è necessario includere `genre` nel parametro `searchFields`.

##  <a name="bkmk_fuzzy"></a> Ricerca fuzzy  
 Una ricerca fuzzy trova le corrispondenze in termini che hanno una costruzione simile. Secondo la [documentazione di Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), le ricerche fuzzy si basano sulla [distanza di Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Le ricerche fuzzy possono espandere un termine fino a un massimo di 50 termini che soddisfano i criteri di distanza. 

 Per eseguire una ricerca fuzzy, usare il carattere tilde "~" alla fine di una singola parola con un parametro facoltativo, un numero compreso tra 0 e 2 (impostazione predefinita), che specifica il numero minimo di operazioni di modifica o "edit distance". Ad esempio, "blue~" o "blue~1" restituirà "blue", "blues" e "glue".

 La ricerca fuzzy può essere applicata solo a termini, non a frasi, ma è possibile aggiungere la tilde a ogni termine singolarmente in un nome in più parti o in una frase. Ad esempio, "Unviersty ~ di ~" wSHINGTON ~ "corrisponderà a" University of Washington ".
 

##  <a name="bkmk_proximity"></a> Ricerca per prossimità  
 Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, `"hotel airport"~5` troverà i termini "hotel" e "airport" entro 5 parole di distanza una dall'altra in un documento.  


##  <a name="bkmk_termboost"></a> Aumento priorità dei termini  
 Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Questo comportamento è diverso dall'assegnazione di punteggi ai profili, perché questo metodo assegna priorità ad alcuni campi, invece che a termini specifici.  

L'esempio seguente illustra le differenze. Si supponga che esista un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio *genre* (genere) nell'[esempio musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, `rock^2 electronic` aumenta la priorità nell'indice dei documenti che contengono tali termini di ricerca nel campo genre rispetto a quelli con altri campi ricercabili. Inoltre, i documenti che contengono il termine di ricerca *rock* verranno classificati con una priorità superiore rispetto all'altro termine di ricerca *electronic* come risultato del valore di priorità del termine (2).  

 Per aumentare la priorità di un termine, usare il carattere accento circonflesso "^", con un fattore di aumento di priorità (un numero) alla fine del termine da cercare. È anche possibile aumentare la priorità delle frasi. Maggiore è il fattore di aumento di priorità, più rilevante sarà il termine rispetto ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,20).  

##  <a name="bkmk_regex"></a> Ricerca basata su espressioni regolari  
 Una ricerca con espressione regolare trova una corrispondenza in base al contenuto incluso tra le barre "/", come indicato nella [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Ad esempio, per trovare i documenti contenenti "motel" o "hotel", specificare `/[mh]otel/`.  Le ricerche basate su espressioni regolari vengono confrontate con parole singole.   

##  <a name="bkmk_wildcard"></a> Ricerca con caratteri jolly  
 È possibile usare una sintassi generalmente riconosciuta per ricerche con caratteri jolly per trovare più caratteri (*) o un singolo carattere (?). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.  

 Ad esempio, per trovare i documenti contenenti le parole con il prefisso "note", come "notebook" o "notepad", specificare "note*".  

> [!NOTE]  
>  Non è possibile usare un carattere * o ? come primo carattere di una ricerca.  
>  Sulle query di ricerca con caratteri jolly non vengono eseguite analisi del testo. In fase di query, i termini della query con caratteri jolly vengono confrontati con i termini analizzati nell'indice di ricerca ed espansi.

## <a name="see-also"></a>Vedi anche  

+ [Eseguire ricerche nei documenti](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintassi delle espressioni OData per filtri e ordinamento](query-odata-filter-orderby-syntax.md)   
+ [Sintassi di query semplice in Azure ricerca cognitiva](query-simple-syntax.md)   
