---
title: 'Creare una query semplice: ricerca di Azure'
description: Per informazioni sull'esecuzione di query in base alla semplice sintassi per la ricerca full-text, la ricerca di filtri, la ricerca geografica e la ricerca in base a facet in un indice di ricerca di Azure.
author: HeidiSteen
manager: nitinme
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7c4aeef07d34159e01f188effae77926895e2857
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179198"
---
# <a name="create-a-simple-query-in-azure-search"></a>Creare una query semplice in ricerca di Azure

In ricerca di Azure, la [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) richiama il parser di query predefinito per l'esecuzione di query di ricerca full-text su un indice. Questo parser è veloce e gestisce scenari comuni, tra cui ricerca full-text, ricerca filtrata e sfaccettata e ricerca geografica. 

In questo articolo vengono usati esempi per illustrare la sintassi semplice.

Una sintassi di query alternativa è [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), che supporta strutture di query più complesse, come la ricerca fuzzy e la ricerca con caratteri jolly, che può richiedere ulteriore tempo per l'elaborazione. Per altre informazioni ed esempi che illustrano la sintassi completa, vedere [usare la sintassi Lucene completa](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulare richieste in Postman

Negli esempi seguenti viene usato l'indice di ricerca NYC Jobs contenente le opportunità di lavoro disponibili in base a un set di dati fornito dall'iniziativa [City of New York OpenData](https://nycopendata.socrata.com/). Questi dati non devono essere considerati attuali o completi. L'indice si trova in un servizio sandbox fornito da Microsoft, il che significa che non è necessario disporre di una sottoscrizione di Azure o di Ricerca di Azure per provare queste query.

È necessario disporre di Postman o di uno strumento equivalente per rilasciare una richiesta HTTP su GET. Per altre informazioni, vedere [Avvio rapido: Esplorare l'API REST di ricerca di Azure](search-get-started-postman.md)con il post.

### <a name="set-the-request-header"></a>Impostare l'intestazione della richiesta

1. Nell'intestazione della richiesta impostare **Content-Type** su `application/json`.

2. Aggiungere una **chiave API** e impostarla su questa stringa: `252044BE3886FE4A8E3BAA4F595114BB`. Si tratta di una chiave di query per il servizio di ricerca sandbox che ospita l'indice NYC Jobs.

Dopo aver specificato l'intestazione della richiesta, è possibile riusarla per tutte le query in questo articolo, scambiando solo la stringa **search=** . 

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Impostare l'URL della richiesta

La richiesta è un comando GET abbinato a un URL contenente la stringa di ricerca e l'endpoint di Ricerca di Azure.

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

La composizione dell'URL presenta i seguenti elementi:

+ **`https://azs-playground.search.windows.net/`** è un servizio di ricerca sandbox gestito dal team di sviluppo di Ricerca di Azure. 
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

La stringa di query, **`search=*`** , è una ricerca non specificata equivalente a una ricerca null o vuota. Non è particolarmente utile, ma è la ricerca più semplice che sia possibile eseguire.

Se lo si desidera, è possibile aggiungere **`$count=true`** all'URL per restituire un conteggio dei documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota, sono tutti i documenti nell'indice (circa 2800 nel caso di NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Come richiamare l'analisi di query semplice

Per le query interattive, non è necessario specificare nulla: la sintassi semplice è quella predefinita. Nel codice, se è stato richiamato **queryType=full** per la sintassi di query completa, è possibile tornare al valore predefinito con **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Esempio 1: Query con ambito campo

Questo primo esempio non è specifico del parser, ma viene presentato per primo come introduzione al primo concetto fondamentale delle query: l'indipendenza. Questo esempio limita l'ambito dell'esecuzione di query e della risposta a un numero ridotto di campi specifici. È importante sapere come strutturare una risposta JSON leggibile quando lo strumento usato è Postman o Esplora ricerche. 

In breve, la query punta solo al campo *business_title* e specifica che vengano restituite solo le qualifiche professionali. La sintassi è **searchFields** per limitare l'esecuzione della query al campo business_title e **select** per specificare i campi da includere nella risposta.

### <a name="partial-query-string"></a>Stringa di query parziale

```http
searchFields=business_title&$select=business_title&search=*
```

Di seguito è illustrata la stessa query con più campi in un elenco delimitato da virgole.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  ![Risposta di esempio di Postman](media/search-query-lucene-examples/postman-sample-results.png)

Si sarà notato il punteggio di ricerca nella risposta. Si ottengono punteggi uniformi pari a 1 in assenza di classificazione perché la ricerca non è una ricerca full-text o perché non sono stati applicati criteri. Per ricerche Null senza criteri le righe vengono restituite in ordine arbitrario. Se si includono criteri effettivi, i punteggi di ricerca si convertono in valori significativi.

## <a name="example-2-look-up-by-id"></a>Esempio 2 Ricerca per ID

Questo esempio è un po' atipico, ma quando si valutano i comportamenti di ricerca è possibile ispezionare l'intero contenuto di un documento specifico per capire perché è stato incluso o escluso dai risultati. Per restituire un intero documento, usare un'[operazione di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per passare l'ID documento.

Tutti i documenti dispongono di un identificatore unico. Per provare la sintassi per una query di ricerca, restituire innanzitutto un elenco di ID documento per trovarne uno da usare. Per NYC Jobs, gli identificatori sono archiviati nel campo `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

L'esempio seguente è una query di ricerca che restituisce un documento in base a `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", che compariva per primo nella risposta precedente. La query seguente restituisce l'intero documento, non solo i campi selezionati. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Esempio 3: Query filtro

La [sintassi del filtro](https://docs.microsoft.com/azure/search/search-query-odata-filter) è un'espressione OData che si può usare con **search** o in modo autonomo. Un filtro autonomo, senza un parametro di ricerca, è utile quando l'espressione filtro è in grado di specificare il nome completo dei documenti di interesse. Senza una stringa di query, non ci sono un'analisi lessicale o linguistica, un'assegnazione del punteggio (tutti i punteggi corrispondono a 1) e una classificazione. Si noti che la stringa di ricerca è vuota.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Usati insieme, il filtro viene applicato per primo all'intero indice, quindi la ricerca viene eseguita sui risultati del filtro. I filtri quindi possono essere un'utile tecnica per migliorare le prestazioni delle query perché riducono il set di documenti che la query di ricerca deve elaborare.

  ![Filtrare le risposte della query](media/search-query-simple-examples/filtered-query.png)

Se si vuole provare questo esempio in Postman usando GET, incollare questa stringa:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Un altro modo efficace di combinare il filtro e la ricerca è tramite **`search.ismatch*()`** in un'espressione filtro in cui è possibile usare una query di ricerca all'interno del filtro. L'espressione filtro usa un carattere jolly nell'elemento *plan* per selezionare business_title includendo i termini plan, planner, planning, e così via.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Per altre informazioni sulla funzione, vedere [search.ismatch in "Esempi di filtro"](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Esempio 4: Filtri di intervallo

Il filtro di intervallo è supportato tramite le espressioni **`$filter`** per qualsiasi tipo di dati. Gli esempi seguenti eseguono la ricerca nei campi numerici e nei campi stringa. 

I tipi di dati sono importanti nei filtri di intervallo e funzionano in modo ottimale quando i dati numerici si trovano nei campi numerici e i dati di tipo stringa nei campi stringa. I dati numerici nei campi stringa non sono adatti per gli intervalli perché le stringhe numeriche non sono confrontabili in Ricerca di Azure. 

Per una migliore leggibilità, gli esempi riportati di seguito sono in formato POST (intervallo numerico seguito dall'intervallo di testo):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtro di intervallo per intervalli numerici](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtro di intervallo per intervalli di testo](media/search-query-simple-examples/rangefiltertext.png)

È anche possibile provare questi esempi in Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> L'esplorazione in base a facet su intervalli di valori è un requisito comune delle applicazioni di ricerca. Per altre informazioni ed esempi sulla creazione dei filtri per le strutture di navigazione facet, vedere ["Filtro basato su un intervallo" in *Come implementare l'esplorazione in base a facet*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Esempio 5: Ricerca geografica

L'indice degli esempi include un campo geo_location con le coordinate di latitudine e longitudine. Questo esempio usa la [funzione geo.distance](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) che applica il filtro ai documenti all'interno della circonferenza di un punto di partenza, fino a una distanza arbitraria (in chilometri) specificata. È possibile modificare l'ultimo valore nella query (4) per ridurre o aumentare l'area della query.

Per una migliore leggibilità, l'esempio riportato di seguito è in formato POST:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Per ottenere risultati più leggibili, i risultati della ricerca vengono rimossi per includere un ID processo, un titolo del processo e il percorso di lavoro. Le coordinate di inizio sono state ottenute da un documento casuale nell'indice (in questo caso, per una località di lavoro a Staten Island).

È anche possibile provare questo esempio in Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Esempio 6: Precisione della ricerca

Le query basate su termini sono composte da singoli termini valutati separatamente. Le query basate su frasi sono racchiuse tra virgolette e vengono valutate come stringhe verbatim. La precisione della corrispondenza è controllata da operatori e searchMode.

Esempio 1: **`&search=fire`** restituisce 150 risultati, dove tutte le corrispondenze contengono la parola fire in qualche punto del documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Esempio 2: **`&search=fire department`** restituisce 2002 risultati. Vengono restituite corrispondenze per i documenti contenenti fire o department.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Esempio 3: **`&search="fire department"`** restituisce 82 risultati. Racchiudendo la stringa tra virgolette si crea una ricerca verbatim su entrambi i termini e le corrispondenze vengono trovate su termini in formato token nell'indice composto dai termini combinati. Questo spiega perché una ricerca come **`search=+fire +department`** non è equivalente. Entrambi i termini sono necessari, ma vengono analizzati in modo indipendente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Esempio 7: Valori booleani con searchMode

La sintassi semplice supporta gli operatori booleani sotto forma di caratteri (`+, -, |`). Il parametro searchMode indica compromessi tra la precisione e il richiamo, con `searchMode=any` che favorisce il richiamo (la corrispondenza su qualsiasi criterio rende idoneo un documento al set di risultati) e `searchMode=all` che favorisce la precisione (deve esistere una corrispondenza per tutti i criteri). Il valore predefinito è `searchMode=any`, che può creare confusione se si impila una query con più operatori e si ottengono risultati più ampi anziché limitati. Questo è particolarmente vero con NOT, dove i risultati includono tutti i documenti che "non contengono" un termine specifico.

Con il parametro searchMode predefinito (qualsiasi), vengono restituiti 2800 documenti: quelli che contengono il termine in più parti "fire department", più tutti i documenti che non contengono il termine "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![modalità di ricerca = qualsiasi](media/search-query-simple-examples/searchmodeany.png)

La modifica del parametro searchMode in `all` implica un effetto cumulativo sui criteri e restituisce un set di risultati ridotto - 21 documenti (documenti contenenti l'intera frase "fire department"), meno le posizioni lavorative all'indirizzo Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![modalità di ricerca = tutto](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Esempio 8: Strutturazione dei risultati

Più parametri determinano i campi presenti nei risultati di ricerca, il numero di documenti restituiti in ciascun batch e l'ordinamento. In questo esempio riaffiorano alcuni degli esempi precedenti, limitando i risultati a campi specifici usando l'istruzione **$select** e i criteri di ricerca verbatim, restituendo 82 corrispondenze 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Aggiunto all'esempio precedente, è possibile ordinare in base al titolo. Questo ordinamento funziona perché civil_service_title è *ordinabile* nell'indice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Il paging dei risultati viene implementato usando il parametro **$top**, in questo caso restituendo i primi 5 documenti:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Per ottenere i successivi 5, ignorare il primo batch:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passaggi successivi
Provare a specificare le query nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca per l'API .NET e l'API REST usando la sintassi semplice predefinita.

* [Eseguire query su un indice di Ricerca di Azure con .NET SDK](search-query-dotnet.md)
* [Eseguire query su un indice di Ricerca di Azure con l'API REST](search-create-index-rest-api.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query ed esempi sono disponibili nei collegamenti seguenti:

+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Query Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Sintassi del filtro e sintassi Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
