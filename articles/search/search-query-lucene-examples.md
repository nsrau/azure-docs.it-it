---
title: Usa la sintassi di query Lucene completa
titleSuffix: Azure Cognitive Search
description: Sintassi di query Lucene per la ricerca fuzzy, la ricerca per prossimità, l'aumento dei termini, la ricerca di espressioni regolari e le ricerche con caratteri jolly in un servizio ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793437"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Usare la sintassi di ricerca Lucene "completa" (query avanzate in Azure ricerca cognitiva)

Quando si creano query per Azure ricerca cognitiva, è possibile sostituire il [parser di query semplice](query-simple-syntax.md) predefinito con il parser di query Lucene più espansivo [in Azure ricerca cognitiva](query-lucene-syntax.md) per formulare definizioni di query specializzate e avanzate. 

Il parser Lucene supporta costrutti di query complessi, ad esempio query con ambito campo, ricerca con caratteri jolly fuzzy e prefisso, ricerca di prossimità, boosting dei termini e ricerca di espressioni regolari. Il livello più avanzato comporta requisiti di elaborazione aggiuntivi. È pertanto opportuno prevedere un tempo di esecuzione leggermente superiore. In questo articolo vengono illustrati esempi di operazioni di query disponibili quando si usa la sintassi completa.

> [!Note]
> Molte delle costruzioni di query specializzate possibili attraverso la sintassi di query Lucene completa non vengono [analizzate dal punto di vista del testo](search-lucene-query-architecture.md#stage-2-lexical-analysis), fatto che può sembrare sorprendente se ci si aspetta lo stemming o la lemmatizzazione. L'analisi lessicale viene eseguita solo su termini completi, la query di un termine o di una locuzione. I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita per i termini di una query incompleta è la conversione in lettere minuscole. 
>

## <a name="formulate-requests-in-postman"></a>Formulare richieste in Postman

Negli esempi seguenti viene usato l'indice di ricerca NYC Jobs contenente le opportunità di lavoro disponibili in base a un set di dati fornito dall'iniziativa [City of New York OpenData](https://opendata.cityofnewyork.us/). Questi dati non devono essere considerati attuali o completi. L'indice si trova in un servizio sandbox fornito da Microsoft, il che significa che non è necessaria una sottoscrizione di Azure o un ricerca cognitiva di Azure per provare queste query.

È necessario disporre di Postman o di uno strumento equivalente per rilasciare una richiesta HTTP su GET. Per altre informazioni, vedere [Esplorare con client REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Impostare l'intestazione della richiesta

1. Nell'intestazione della richiesta impostare **Content-Type** su `application/json`.

2. Aggiungere una **chiave API** e impostarla su questa stringa: `252044BE3886FE4A8E3BAA4F595114BB`. Si tratta di una chiave di query per il servizio di ricerca sandbox che ospita l'indice NYC Jobs.

Dopo aver specificato l'intestazione della richiesta, è possibile riusarla per tutte le query in questo articolo, scambiando solo la stringa **search=** . 

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Impostare l'URL della richiesta

Request è un comando GET associato a un URL contenente l'endpoint di Azure ricerca cognitiva e la stringa di ricerca.

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

La composizione dell'URL presenta i seguenti elementi:

+ **`https://azs-playground.search.windows.net/`** è un servizio di ricerca sandbox gestito dal team di sviluppo di Azure ricerca cognitiva. 
+ **`indexes/nycjobs/`** è l'indice NYC Jobs nella raccolta di indici del servizio. Il nome e l'indice del servizio sono entrambi necessari sulla richiesta.
+ **`docs`** è la raccolta di documenti contenente tutto il contenuto disponibile per la ricerca. La chiave API della query fornita nell'intestazione della richiesta funziona solo nelle operazioni di lettura destinate alla raccolta di documenti.
+ **`api-version=2019-05-06`** imposta la versione dell'API, un parametro obbligatorio per ogni richiesta.
+ **`search=*`** è la stringa di query, che nella query iniziale è null e restituisce i primi 50 risultati (per impostazione predefinita).

## <a name="send-your-first-query"></a>Inviare la prima query

Come fase di verifica, incollare la seguente richiesta in GET e fare clic su **Invia**. I risultati vengono restituiti come documenti JSON dettagliati. Vengono restituiti interi documenti, che consentono di visualizzare tutti i campi e tutti i valori.

Incollare l'URL in un client REST come passaggio di convalida e visualizzare la struttura del documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

La stringa di query, **`search=*`** , è una ricerca non specificata equivalente a una ricerca null o vuota. Si tratta della ricerca più semplice che è possibile eseguire.

Se lo si desidera, è possibile aggiungere **`$count=true`** all'URL per restituire un conteggio dei documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, sono tutti i documenti nell'indice (circa 2800 nel caso di NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Come richiamare l'analisi completa di Lucene

Aggiungere **queryType=full** per richiamare la sintassi di query completa che sostituisce la sintassi di query semplice predefinita. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Tutti gli esempi in questo articolo specificano il parametro di ricerca **queryType=full**, che indica che la sintassi completa viene gestita dal parser di query Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Esempio 1: query con ambito per un elenco di campi

Questo primo esempio non è specifico di Lucene, ma viene introdotto il primo concetto di query fondamentale, ovvero l'ambito del campo. In questo esempio viene definito l'ambito dell'intera query e della risposta solo ad alcuni campi specifici. È importante sapere come strutturare una risposta JSON leggibile quando lo strumento usato è Postman o Esplora ricerche. 

In breve, la query punta solo al campo *business_title* e specifica che vengano restituite solo le qualifiche professionali. Il parametro **searchFields** limita l'esecuzione delle query al solo campo business_title e **SELECT** specifica i campi inclusi nella risposta.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
&search=*&searchFields=business_title&$select=business_title
```

Di seguito è illustrata la stessa query con più campi in un elenco delimitato da virgole.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Gli spazi dopo le virgole sono facoltativi.

> [!Tip]
> Quando si usa l'API REST dal codice dell'applicazione, non dimenticare i parametri di codifica URL come `$select` e `searchFields`.

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  ![Risposta di esempio di Postman](media/search-query-lucene-examples/postman-sample-results.png)

Si sarà notato il punteggio di ricerca nella risposta. Si ottengono punteggi uniformi pari a 1 in assenza di classificazione perché la ricerca non è una ricerca full-text o perché non sono stati applicati criteri. Per ricerche Null senza criteri le righe vengono restituite in ordine arbitrario. Quando si includono i criteri di ricerca effettivi, si noterà che i punteggi di ricerca si evolvono in valori significativi.

## <a name="example-2-fielded-search"></a>Esempio 2: ricerca nel campo

La sintassi Lucene completa supporta l'ambito di singole espressioni di ricerca in un campo specifico. Questo esempio cerca i titoli aziendali con il termine senior, ma non Junior.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Di seguito è illustrata la stessa query con più campi.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completo

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Risposta di esempio di Postman](media/search-query-lucene-examples/intrafieldfilter.png)

È possibile definire un'operazione di ricerca in campo con la sintassi **FieldName: searchExpression** , in cui l'espressione di ricerca può essere costituita da una singola parola o una frase o da un'espressione più complessa tra parentesi, facoltativamente con operatori booleani. Ecco alcuni esempi:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Assicurarsi di inserire più stringhe tra virgolette se si desidera che entrambe le stringhe vengano valutate come una singola entità, come in questo caso la ricerca di due posizioni distinte nel campo `state`. Assicurarsi anche che l'operatore sia in lettere maiuscole, come NOT e AND.

Il campo specificato in **FieldName: searchExpression** deve essere un campo ricercabile. Per informazioni dettagliate sull'uso degli attributi di indice nelle definizioni di campo, vedere [create index (API REST di Azure ricerca cognitiva)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

> [!NOTE]
> Nell'esempio precedente non era necessario usare il parametro `searchFields` perché ogni parte della query ha un nome di campo specificato in modo esplicito. Tuttavia, è comunque possibile utilizzare il parametro `searchFields` se si desidera eseguire una query in cui alcune parti hanno come ambito un campo specifico e il resto può essere applicato a più campi. Il `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` di query, ad esempio, corrisponderà `senior NOT junior` solo al campo `business_title`, mentre corrisponderebbe a "External" con il campo `posting_type`. Il nome del campo specificato in **FieldName: searchExpression** ha sempre la precedenza sul parametro `searchFields`, motivo per cui in questo esempio non è necessario includere `business_title` nel parametro `searchFields`.

## <a name="example-3-fuzzy-search"></a>Esempio 3: ricerca fuzzy

La sintassi Lucene completa supporta anche la ricerca fuzzy, basata sui termini che hanno una costruzione simile. Per eseguire una ricerca fuzzy, aggiungere il simbolo tilde `~` alla fine di una parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica la distanza di edit. Ad esempio, `blue~` o `blue~1` restituirà blue, blues e glue.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Le frasi non sono supportate direttamente, ma è possibile specificare una corrispondenza fuzzy sulle parti componente di una frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completo

Questa query cerca le opportunità di lavoro con il termine "associate" (scritto erroneamente di proposito):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Risposta di Ricerca fuzzy](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Le query fuzzy non vengono [analizzate](search-lucene-query-architecture.md#stage-2-lexical-analysis). I tipi di query con termini incompleti, ad esempio query di prefisso, di caratteri jolly, di espressioni regolari, fuzzy, vengono aggiunte direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita per i termini di una query incompleta è la conversione in lettere minuscole.
>

## <a name="example-4-proximity-search"></a>Esempio 4: ricerca di prossimità
Le ricerche per prossimità vengono usate per trovare termini che si trovano vicini in un documento. Inserire un carattere tilde "~" alla fine di una frase seguito dal numero di parole che creano il limite di prossimità. Ad esempio, "hotel airport"~5 troverà i termini hotel e airport entro 5 parole di distanza una dall'altra in un documento.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completo

In questa query per le opportunità di lavoro contenenti il termine "senior analyst" separato da non più di una parola:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Query di prossimità](media/search-query-lucene-examples/proximity-before.png)

Riprovare rimuovendo le parole tra il termine "senior analyst". Si noti che vengono restituiti 8 documenti per questa query, rispetto ai 10 per la query precedente.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Esempio 5: aumento priorità termine
Questa definizione si riferisce alla termine si riferisce alla classificazione più alta di un documento se contiene il termine con aumento di priorità, rispetto a documenti che non contengono il termine. Per aumentare la priorità di un termine, usare il carattere accento circonflesso "^", con un fattore di aumento di priorità (un numero) alla fine del termine da cercare. 

### <a name="full-urls"></a>URL completi

In questa query "before" cercare le opportunità di lavoro con il termine *computer analyst* e si noti che non vi sono risultati con le parole *computer* e *analyst*, eppure i lavori *computer* sono i primi risultati.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Aumento priorità termini "before"](media/search-query-lucene-examples/termboostingbefore.png)

Nella query "after", ripetere la ricerca, questa volta aumentando la priorità dei risultati con il termine *analyst* rispetto al termine *computer* se nessuna delle due parole esiste. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Una versione maggiormente leggibile della query precedente è `search=business_title:computer analyst^2`. Per una query di lavoro `^2` viene codificato come `%5E2`, più difficile da vedere.

  ![Aumento priorità termini "after"](media/search-query-lucene-examples/termboostingafter.png)

L'aumento priorità dei termini si differenzia dai profili di punteggio per il fatto che questi ultimi aumentano la priorità di alcuni campi e non di termini specifici. L'esempio seguente illustra le differenze.

Considerare un profilo di punteggio che migliora le corrispondenze in un determinato campo, ad esempio **genre** (genere) nell'esempio musicstoreindex. L'aumento di priorità di un termine si usa per assegnare a determinati termini di ricerca una priorità maggiore rispetto ad altri. Ad esempio, "rock^2 electronic" aumenta la priorità nell'indice dei documenti che contengono tali termini di ricerca nel campo **genre** rispetto a quelli con altri campi ricercabili. Inoltre, i documenti che contengono il termine di ricerca "rock" verranno classificati con una priorità superiore rispetto all'altro termine di ricerca "electronic" come risultato il valore di priorità del termine (2).

Quando si imposta il fattore, maggiore è il fattore di aumento, maggiore è la rilevanza del termine relativamente ad altri termini di ricerca. Per impostazione predefinita, il fattore di aumento di priorità è 1. Anche se il fattore di aumento di priorità deve essere positivo, può essere minore di 1 (ad esempio 0,2).


## <a name="example-6-regex"></a>Esempio 6: Regex

Una ricerca con espressione regolare trova una corrispondenza in base al contenuto incluso tra le barre "/", come indicato nella [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Stringa di query parziale

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completo

In questa query cercare i processi con il termine senior o Junior: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Query Regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> Le query Regex non vengono [analizzate](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). L'unica trasformazione eseguita per i termini di una query incompleta è la conversione in lettere minuscole.
>

## <a name="example-7-wildcard-search"></a>Esempio 7: ricerca con caratteri jolly
È possibile usare una sintassi generalmente riconosciuta per ricerche con caratteri jolly per trovare più caratteri (\*) o un singolo carattere (?). Si noti che il parser di query Lucene supporta l'utilizzo di questi simboli con un singolo termine, non una frase.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completo

In questa query cercare le opportunità di lavoro che contengono il prefisso 'prog' che include le qualifiche professionali con i termini programming e programmer. Non è possibile usare un carattere * o ? come primo carattere di una ricerca.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Query con caratteri jolly](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Le query con caratteri jolly non vengono [analizzate](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). L'unica trasformazione eseguita per i termini di una query incompleta è la conversione in lettere minuscole.
>

## <a name="next-steps"></a>Passaggi successivi
Provare a specificare il parser di query Lucene nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca per .NET e l'API REST. I collegamenti usano la sintassi semplice predefinita, quindi è necessario applicare quanto appreso in questo articolo per specificare il parametro **queryType**.

* [Eseguire query sull'indice usando .NET SDK](search-query-dotnet.md)
* [Eseguire query sull'indice usando l'API REST](search-create-index-rest-api.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query e gli esempi sono disponibili nei seguenti collegamenti:

+ [Esempi di query con sintassi semplice](search-query-simple-examples.md)
+ [Funzionamento della ricerca full-text in Azure ricerca cognitiva](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Full Lucene query syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Sintassi di query completa Lucene)