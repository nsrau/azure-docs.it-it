---
title: Esempi di query semplici per Ricerca di Azure | Microsoft Docs
description: Esempi di query semplici per la ricerca full-text, la ricerca con filtro, la ricerca con geolocalizzazione, la ricerca basata su facet e altre stringhe di query usate per eseguire query in un indice di Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368599"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Esempi di query con sintassi semplice per compilare query in Ricerca di Azure

La [sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) richiama il parser di query predefinito per eseguire query di ricerca full-text in un indice di Ricerca di Azure. L'analizzatore di query semplice è rapido e gestisce scenari comuni in Ricerca di Azure, incluse la ricerca full-text, la ricerca con filtro e facet e la ricerca con geolocalizzazione. In questo articolo vedere gli esempi che dimostrano le operazioni di query disponibili con la sintassi semplice.

La sintassi di query alternativa è [la sintassi di query Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), che supporta strutture di query più complesse come la ricerca fuzzy e con caratteri jolly la cui elaborazione richiede più tempo. Per ulteriori informazioni ed esempi che dimostrano la sintassi completa, vedere [gli esempi di query con sintassi Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulare richieste in Postman

Negli esempi seguenti viene usato l'indice di ricerca NYC Jobs contenente le opportunità di lavoro disponibili in base a un set di dati fornito dall'iniziativa [City of New York OpenData](https://nycopendata.socrata.com/). Questi dati non devono essere considerati attuali o completi. L'indice si trova in un servizio sandbox fornito da Microsoft, il che significa che non è necessario disporre di una sottoscrizione di Azure o di Ricerca di Azure per provare queste query.

È necessario disporre di Postman o di uno strumento equivalente per rilasciare una richiesta HTTP su GET. Per ulteriori informazioni, vedere [Testare con i client REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Impostare l'intestazione della richiesta

1. Nell'intestazione della richiesta impostare **Content-Type** su `application/json`.

2. Aggiungere una **chiave API** e impostarla su questa stringa: `252044BE3886FE4A8E3BAA4F595114BB`. Si tratta di una chiave di query per il servizio di ricerca sandbox che ospita l'indice NYC Jobs.

Dopo aver specificato l'intestazione della richiesta, è possibile riusarla per tutte le query in questo articolo, scambiando solo la stringa **search=**. 

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Impostare l'URL della richiesta

La richiesta è un comando GET abbinato a un URL contenente la stringa di ricerca e l'endpoint di Ricerca di Azure.

  ![Intestazione della richiesta Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

La composizione dell'URL presenta i seguenti elementi:

+ **`https://azs-playground.search.windows.net/`** è un servizio di ricerca sandbox gestito dal team di sviluppo di Ricerca di Azure. 
+ **`indexes/nycjobs/`** è l'indice NYC Jobs nella raccolta di indici del servizio. Il nome e l'indice del servizio sono entrambi necessari sulla richiesta.
+ **`docs`** è la raccolta di documenti contenente tutto il contenuto disponibile per la ricerca. La chiave API della query fornita nell'intestazione della richiesta funziona solo per le operazioni di lettura destinate alla raccolta di documenti.
+ **`api-version=2017-11-11`** imposta la versione dell'API, un parametro obbligatorio per ogni richiesta.
+ **`search=*`** è la stringa di query, che nella query iniziale è null e restituisce i primi 50 risultati (per impostazione predefinita).

## <a name="send-your-first-query"></a>Inviare la prima query

Come fase di verifica, incollare la seguente richiesta in GET e fare clic su **Invia**. I risultati vengono restituiti come documenti JSON dettagliati. È possibile copiare e incollare questo URL nel primo esempio riportato di seguito.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

La stringa di query, **`search=*`**, è una ricerca non specificata equivalente a una ricerca null o vuota. Non è particolarmente utile, ma è la ricerca più semplice che sia possibile eseguire.

Se lo si desidera, è possibile aggiungere **`$count=true`** all'URL per restituire un conteggio dei documenti corrispondenti ai criteri di ricerca. In una stringa di ricerca vuota sono tutti i documenti nell'indice (2802 nel caso di NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Come richiamare l'analisi di query semplice

Per le query interattive, non è necessario specificare nulla: la sintassi semplice è quella predefinita. Nel codice se è stato richiamato **queryType=full** per la sintassi di query completa, è possibile tornare al valore predefinito con **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Esempio 1: query con ambito campo

La prima query non è specifica della sintassi (la query funziona sia per la sintassi semplice che per quella completa), ma con questo esempio intendiamo introdurre un concetto di query di riferimento in grado di produrre una risposta JSON ragionevolmente leggibile. In breve, la query punta solo al campo *business_title* e specifica che vengano restituite solo le qualifiche professionali. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

Il parametro **searchFields** limita la ricerca al solo campo della qualifica professionale (business_title). Il parametro **select** determina quali campi sono inclusi nel set di risultati.

La risposta per questa query dovrebbe essere simile alla seguente schermata.

  ![Risposta di esempio di Postman](media/search-query-lucene-examples/postman-sample-results.png)

Si noti che il punteggio della ricerca viene restituito per ogni documento anche se non è stato specificato. Questo perché il punteggio di ricerca è composto da metadati, il cui valore indica l'ordine dei risultati. Si ottengono punteggi uniformi pari a 1 in assenza di classificazione perché la ricerca non è una ricerca full-text o perché non vi sono criteri da applicare. Per la ricerca null, non vi sono criteri e le righe restituite hanno un ordine arbitrario. Poiché i criteri di ricerca assumono più definizioni, i punteggi di ricerca si convertono in valori significativi.

## <a name="example-2-look-up-by-id"></a>Esempio 2: ricerca per ID

Questo esempio è un po' atipico, ma quando si valutano i comportamenti di ricerca è possibile ispezionare l'intero contenuto di un documento per capire perché è stato incluso o escluso dai risultati. Per restituire un intero documento, usare un'[operazione di ricerca](https://docs.microsoft.com/rest/api/searchservice/lookup-document) per passare l'ID documento.

Tutti i documenti dispongono di un identificatore unico. Per provare la sintassi per una query di ricerca, restituire innanzitutto un elenco di ID documento per trovarne uno da usare. Per NYC Jobs, gli identificatori sono archiviati nel campo `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

L'esempio seguente è una query di ricerca che restituisce un documento in base a `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", che compariva per primo nella risposta precedente. La query seguente restituisce l'intero documento, non solo i campi selezionati. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>Esempio 3: precisione della ricerca

Le query basate su termini sono composte da singoli termini valutati separatamente. Le query basate su frasi sono racchiuse tra virgolette e vengono valutate come stringhe verbatim. La precisione della corrispondenza è controllata da operatori e searchMode.

Esempio 1: **`&search=fire`** restituisce 150 risultati, dove tutte le corrispondenze contengono la parola fire in qualche punto del documento.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Esempio 2: **`&search=fire department`** restituisce 2002 risultati. Vengono restituite corrispondenze per i documenti contenenti fire o department.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Esempio 3: **`&search="fire department"`** restituisce 82 risultati. Racchiudendo la stringa tra virgolette si crea una ricerca verbatim su entrambi i termini e le corrispondenze vengono trovate su termini in formato token nell'indice composto dai termini combinati. Questo spiega perché una ricerca come **`search=+fire +department`** non è equivalente. Entrambi i termini sono necessari, ma vengono analizzati in modo indipendente. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>Esempio 4: valori booleani con searchMode

La sintassi semplice supporta gli operatori booleani sotto forma di caratteri (`+, -, |`). Il parametro searchMode indica compromessi tra la precisione e il richiamo, con `searchMode=any` che favorisce il richiamo (la corrispondenza su qualsiasi criterio rende idoneo un documento al set di risultati) e `searchMode=all` che favorisce la precisione (deve esistere una corrispondenza per tutti i criteri). Il valore predefinito è `searchMode=any`, che può creare confusione se si impila una query con più operatori e si ottengono risultati più ampi anziché limitati. Questo è particolarmente vero con NOT, dove i risultati includono tutti i documenti che "non contengono" un termine specifico.

Con il parametro searchMode predefinito (qualsiasi), vengono restituiti 2800 documenti: quelli che contengono il termine in più parti "fire department", più tutti i documenti che non contengono il termine "Metrotech Center".

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
La modifica del parametro searchMode in `all` implica un effetto cumulativo sui criteri e restituisce un set di risultati ridotto - 21 documenti (documenti contenenti l'intera frase "fire department"), meno le posizioni lavorative all'indirizzo Metrotech Center.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>Esempio 5: strutturazione dei risultati

Più parametri determinano i campi presenti nei risultati di ricerca, il numero di documenti restituiti in ciascun batch e l'ordinamento. In questo esempio riaffiorano alcuni degli esempi precedenti, limitando i risultati a campi specifici usando l'istruzione **$select** e i criteri di ricerca verbatim, restituendo 82 corrispondenze 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Aggiunto all'esempio precedente, è possibile ordinare in base al titolo. Questo ordinamento funziona perché civil_service_title è *ordinabile* nell'indice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Il paging dei risultati viene implementato usando il parametro **$top**, in questo caso restituendo i primi 5 documenti:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Per ottenere i successivi 5, ignorare il primo batch:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Passaggi successivi
Provare a specificare le query nel codice. I collegamenti seguenti illustrano come configurare le query di ricerca per l'API .NET e l'API REST usando la sintassi semplice predefinita.

* [Eseguire query su un indice di Ricerca di Azure con .NET SDK](search-query-dotnet.md)
* [Eseguire query su un indice di Ricerca di Azure con l'API REST](search-query-rest-api.md)

Un riferimento alla sintassi aggiuntivo, l'architettura di query ed esempi sono disponibili nei collegamenti seguenti:

+ [Esempi di query con sintassi Lucene per la compilazione di query avanzate](search-query-lucene-examples.md)
+ [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md)
+ [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Query Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
