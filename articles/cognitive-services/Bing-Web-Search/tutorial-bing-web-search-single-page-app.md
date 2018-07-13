---
title: Ricerca Web Bing in un'app Web a pagina singola | Microsoft Docs
description: Illustra come usare l'API Ricerca Web Bing in un'applicazione Web a pagina singola.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377033"
---
# <a name="tutorial-single-page-web-app"></a>Esercitazione: app Web a pagina singola

L'API Ricerca Web Bing consente di eseguire ricerche sul Web e di ottenere risultati di vari tipi pertinenti a una query di ricerca. In questa esercitazione viene compilata un'applicazione Web a pagina singola che usa l'API Ricerca Web Bing per visualizzare i risultati della ricerca direttamente nella pagina. L'applicazione include componenti HTML, CSS e JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> Quando si fa clic sulle intestazioni JSON e HTTP nella parte inferiore della pagina, vengono visualizzate le informazioni relative alla risposta JSON e alla richiesta HTTP. Questi dettagli risultano utili durante l'esplorazione del servizio.

L'applicazione di esercitazione illustra come:

> [!div class="checklist"]
> * Eseguire una chiamata all'API Ricerca Web Bing in JavaScript
> * Passare le opzioni di ricerca all'API Ricerca Web Bing
> * Visualizzare risultati di ricerca di tipo Web, notizie, immagini e video
> * Sfogliare i risultati della ricerca
> * Gestire l'ID del client Bing e la chiave di sottoscrizione dell'API
> * Gestire gli errori che potrebbero verificarsi

La pagina dell'esercitazione è autonoma, non usa framework, fogli di stile o file di immagine esterni. Usa solo funzionalità del linguaggio JavaScript ampiamente supportate e funziona con le versioni correnti di tutti i Web browser principali.

Questa esercitazione illustra solo alcune parti del codice sorgente. Il codice sorgente completo è disponibile [in un'altra pagina](tutorial-bing-web-search-single-page-app-source.md). Copiare e incollare il codice in un editor di testo e salvarlo come `bing.html`.

## <a name="app-components"></a>Componenti dell'app

Come qualsiasi app Web a pagina singola, l'applicazione di esercitazione include tre parti:

> [!div class="checklist"]
> * HTML, che definisce la struttura e il contenuto della pagina
> * CSS, che definisce l'aspetto della pagina
> * JavaScript, che definisce il comportamento della pagina

Questa esercitazione non tratta dettagliatamente il codice HTML o CSS, perché sono piuttosto semplici.

Il codice HTML contiene il modulo di ricerca in cui l'utente immette una query e sceglie le opzioni di ricerca. Il modulo è connesso a JavaScript che esegue effettivamente la ricerca usando l'attributo `onsubmit` del tag `<form>`:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

Il gestore `onsubmit` restituisce `false`, che impedisce che il modulo venga inviato a un server. Il codice JavaScript esegue le operazioni di raccolta delle informazioni necessarie dal modulo ed esegue la ricerca.

Il codice HTML contiene anche le divisioni, ovvero tag HTML `<div>`, in cui vengono visualizzati i risultati della ricerca.

## <a name="managing-subscription-key"></a>Gestione della chiave di sottoscrizione

Per evitare di includere la chiave di sottoscrizione dell'API di ricerca Bing nel codice, viene usata l'archiviazione permanente del browser per archiviare la chiave. Se non è archiviata alcuna chiave, viene richiesta la chiave dell'utente e viene archiviata per uso futuro. Se in un secondo momento la chiave viene rifiutata dall'API, la chiave archiviata viene invalidata e l'utente riceverà nuovamente la richiesta.

Si definiscono le funzioni `storeValue` e `retrieveValue` che usano l'oggetto `localStorage`, se il browser lo supporta, o un cookie. La funzione `getSubscriptionKey()` usa queste funzioni per archiviare e recuperare la chiave dell'utente.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Il tag `onsubmit` HTML `form` chiama la funzione `bingWebSearch` per restituire i risultati della ricerca. `bingWebSearch` usa `getSubscriptionKey` per autenticare ciascuna query. Come indicato nella definizione precedente, `getSubscriptionKey` richiede all'utente la chiave se non è stata immessa. La chiave viene quindi archiviata per continuare a essere usata dall'applicazione.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Selezione delle opzioni di ricerca

![[Modulo API Ricerca Web Bing]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

Il modulo HTML include elementi con i seguenti nomi:

| | |
|-|-|
| `where` | Un menu a discesa per la selezione del mercato, ovvero località e lingua, utilizzato per la ricerca. |
| `query` | Il campo di testo in cui inserire i termini di ricerca. |
| `what` | Caselle di controllo per alzare di livello determinati tipi di risultati. Alzando di livello le immagini, ad esempio, se ne alza la classificazione. |
| `when` | Menu a discesa per limitare facoltativamente la ricerca all'ultimo giorno, settimana o mese. |
| `safe` | Una casella di controllo che indica se usare la funzionalità SafeSearch di Bing per filtrare i risultati con contenuti "per soli adulti". |
| `count` | Campo nascosto. Il numero di risultati della ricerca da restituire per ogni richiesta. Modificarlo per visualizzare un numero maggiore o minore di risultati per pagina. |
| `offset` | Campo nascosto. Lo scostamento del primo risultato della ricerca nella richiesta; utilizzato per il paging. Viene reimpostato su `0` per una nuova richiesta. |

> [!NOTE]
> Ricerca Web Bing offre molti altri parametri di query. Qui ne vengono usati solo alcuni.

La funzione JavaScript `bingSearchOptions()` converte questi campi nel formato richiesto dall'API di ricerca Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Ad esempio, il parametro `SafeSearch` in una chiamata API effettiva può essere `strict`, `moderate` o `off` con `moderate` come valore predefinito. Il modulo, tuttavia, usa una casella di controllo che presenta solo due stati. Il codice JavaScript converte questa impostazione in `strict` o `off`; `moderate` non viene usato.

Se una qualsiasi delle caselle di controllo **Alza di livello** è contrassegnata, si aggiunge anche un parametro `answerCount` alla query. `answerCount` è necessario quando si usa il parametro `promote`. Lo si imposta semplicemente su `9`, ovvero il numero dei tipi di risultato supportati dall'API Ricerca Web Bing, per assicurarsi di ottenere il numero massimo possibile di tipi di risultati.

> [!NOTE]
> Alzando di livello un tipo di risultato non si *garantisce* che i risultati della ricerca includano questo tipo di risultato, ma si alza la classificazione di questi tipi di risultati rispetto alla loro classificazione consueta. Per limitare le ricerche a determinati tipi di risultati, usare il parametro di query `responseFilter` o chiamare un endpoint più specifico, ad esempio Ricerca immagini Bing o Ricerca notizie Bing.

Si inviano anche i parametri di query `textDecoration` e `textFormat` per fare in modo che il termine di ricerca appaia in grassetto nei risultati della ricerca. Questi valori sono hardcoded nello script.

## <a name="performing-the-request"></a>Esecuzione della richiesta

Dati la query, la stringa delle opzioni e la chiave API, la funzione `BingWebSearch` usa un oggetto `XMLHttpRequest` per effettuare la richiesta all'endpoint di Ricerca Web Bing.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Al completamento corretto della richiesta HTTP, JavaScript chiama il gestore dell'evento `load`, la funzione `handleBingResponse()`, per gestire una richiesta HTTP GET corretta all'API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Una richiesta HTTP corretta *non* necessariamente indica che la ricerca sia andata a buon fine. Se si verifica un errore nell'operazione di ricerca, l'API Ricerca Web Bing restituisce un codice di stato HTTP non-200 e include le informazioni sull'errore nella risposta JSON. Inoltre, se la richiesta era a frequenza limitata, l'API restituisce una risposta vuota.

La maggior parte del codice in entrambe le precedenti funzioni è dedicata alla gestione degli errori. Possono verificarsi errori nelle fasi seguenti:

|Fase|Errori potenziali|Gestito da|
|-|-|-|
|Compilazione dell'oggetto richiesta JavaScript|URL non valido|Blocco `try`/`catch`|
|Invio della richiesta|Errori di rete, connessioni interrotte|Gestori degli eventi `error` e `abort`|
|Esecuzione della ricerca|Richiesta non valida, JSON non valido, limiti di frequenza|Test nel gestore eventi `load`|

Gli errori vengono gestiti chiamando `renderErrorMessage()` con tutti i dettagli noti dell'errore. Se la risposta supera la serie completa dei test di errore, viene chiamato `renderSearchResults()` per visualizzare i risultati della ricerca nella pagina.

## <a name="displaying-search-results"></a>Visualizzazione dei risultati della ricerca

L'API Ricerca Web Bing [richiede di visualizzare i risultati in un ordine specifico](useanddisplayrequirements.md). Poiché l'API può restituire diversi tipi di risposte, non è sufficiente eseguire l'iterazione sulla raccolta di massimo livello `WebPages` nella risposta JSON e visualizzare i risultati. Se si desidera un solo tipo di risultati, usare il parametro di query `responseFilter` o un altro endpoint di Ricerca Bing.

Si usa invece `rankingResponse` nei risultati della ricerca per ordinare i risultati per la visualizzazione. Questo oggetto fa riferimento a elementi delle raccolte `WebPages` `News`, `Images` e/o `Videos` o in altre raccolte di risposta di massimo livello nella risposta JSON.

`rankingResponse` può contenere fino a tre raccolte di risultati della ricerca, indicate come `pole`, `mainline` e `sidebar`. 

`pole`, se presente, è il risultato della ricerca più pertinente e deve essere visualizzato in primo piano. `mainline` fa riferimento alla maggior parte dei risultati della ricerca. I risultati di tipo mainline devono essere visualizzati immediatamente dopo `pole`, o prima se `pole` non è presente. 

Infine, `sidebar` si riferisce a risultati della ricerca ausiliari. Spesso questi risultati sono immagini o ricerche correlate. Se possibile, questi risultati devono essere visualizzati in una barra laterale. Se i limiti dello schermo non consentono di mostrare una barra laterale, ad esempio in un dispositivo mobile, devono essere visualizzati dopo i risultati `mainline`.

Ogni elemento in una raccolta `rankingResponse` fa riferimento agli effettivi elementi dei risultati della ricerca in due modi diversi, ma equivalenti.

| | |
|-|-|
|`id`|`id` è simile a un URL, ma non deve essere usato per i collegamenti. Il tipo `id` di un risultato di classificazione corrisponde al tipo `id` di un elemento dei risultati della ricerca in una raccolta di risposte *o* un'intera raccolta di risposte, ad esempio `Images`.
|`answerType`, `resultIndex`|`answerType` fa riferimento alla raccolta di risposte di massimo livello contenente il risultato, ad esempio `WebPages`. `resultIndex` fa riferimento all'indice del risultato all'interno della raccolta. Se `resultIndex` viene omesso, il risultato di classificazione si riferisce all'intera raccolta.

> [!NOTE]
> Per altre informazioni su questa parte della risposta della ricerca, vedere [Classificare i risultati](rank-results.md).

È possibile usare un metodo qualsiasi per individuare in modo più conveniente l'elemento referenziato dei risultati della ricerca per l'applicazione. Nel codice dell'esercitazione vengono usati `answerType` e `resultIndex` per individuare ogni risultato della ricerca.

È infine giunto il momento di osservare la funzione `renderSearchResults()`. Questa funzione esegue l'iterazione sulle tre raccolte `rankingResponse` che rappresentano le tre sezioni dei risultati della ricerca. Per ogni sezione viene chiamato `renderResultsItems()` per eseguire il rendering dei risultati della sezione.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` a sua volta esegue un'iterazione sugli elementi in ogni sezione `rankingResponse`, esegue il mapping di ogni risultato della classificazione a un risultato di ricerca mediante i campi `answerType` e `resultIndex` e chiama la funzione di rendering appropriata per generare l'HTML del risultato. 

Se `resultIndex` non viene specificato per un determinato elemento della classificazione, `renderResultsItems()` esegue un'iterazione su tutti i risultati di quel tipo e chiama la funzione di rendering per ogni elemento. 

In entrambi i casi il codice HTML risultante viene inserito nell'elemento `<div>` appropriato nella pagina.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Rendering degli elementi dei risultati

Nel codice JavaScript l'oggetto `searchItemRenderers` contiene i *renderer*: funzioni che generano codice HTML per ciascun tipo di risultato della ricerca.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> L'applicazione di esercitazione ha renderer per pagine Web, notizie, immagini, video e ricerche correlate. Un'applicazione deve avere renderer per tutti i tipi di risultati che può ricevere, fra cui possono essere inclusi calcoli, suggerimenti, entità, fusi orari e definizioni.

Alcuni delle nostre funzioni di rendering accettano solo il parametro `item`: un oggetto JavaScript che rappresenta un singolo risultato di ricerca. Altre accettano parametri aggiuntivi, che possono essere usati per eseguire il rendering degli elementi in modo diverso in contesti differenti. Un renderer che non usa queste informazioni non ha bisogno di accettare questi parametri.

Gli argomenti di contesto sono:

| | |
|-|-|
|`section`|La sezione relativa ai risultati, ovvero `pole`, `mainline` o `sidebar`, in cui l'elemento compare.
|`index`<br>`count`|Disponibile quando l'elemento `rankingResponse` specifica che tutti i risultati di una determinata raccolta devono essere visualizzati; `undefined` in caso contrario. Questi parametri ricevono l'indice dell'elemento all'interno della raccolta e il numero totale di elementi della raccolta. È possibile usare queste informazioni per numerare i risultati e generare codice HTML diverso per il primo o l'ultimo risultato e così via.|

Nell'app dell'esercitazione entrambi i renderer `images` e `relatedSearches` usano argomenti di contesto per personalizzare il codice HTML che generano. Analizziamo in dettaglio il renderer `images`:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

La funzione renderer dell'immagine:

> [!div class="checklist"]
> * Calcola le dimensioni dell'anteprima immagine; la larghezza varia, mentre l'altezza è fissa a 60 pixel.
> * Inserisce il codice HTML che precede l'immagine ottenuta come risultato in base al contesto.
> * Compila il tag HTML `<a>` che collega la pagina contenente l'immagine.
> * Compila il tag HTML `<img>` per visualizzare l'anteprima dell'immagine. 

Usa il renderer di immagini per le variabili `section` e `index` per visualizzare i risultati in modo diverso a seconda di dove vengono mostrati. Un'interruzione di riga, tag `<br>`, viene inserita tra le immagini ottenute come risultato nella barra laterale, in modo che la barra laterale mostri una colonna di immagini. In altre sezioni la prima immagine dei risultati `(index === 0)` è preceduta da un tag `<p>`. In caso contrario le anteprime sono una vicina all'altra e vanno a capo come necessario nella finestra del browser.

La dimensione dell'anteprima viene usata sia nel tag `<img>` che nei campi `h` e `w` dell'URL dell'anteprima. Il [servizio di anteprima Bing](resize-and-crop-thumbnails.md) invia un'anteprima esattamente di tali dimensioni. Gli attributi `title` e `alt`, una descrizione testuale dell'immagine, sono costruiti dal nome dell'immagine e il nome host nell'URL.

Le immagini sono visualizzate come mostrato di seguito nei risultati principali della ricerca.

![[Immagine ottenute come risultato da Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>ID client persistente

Le risposte dalle API di ricerca Bing potrebbero includere un'intestazione `X-MSEdge-ClientID` che deve essere inviata all'API con le richieste successive. Se vengono usate più API di ricerca Bing, usare se possibile lo stesso ID client con tutte le API.

Fornire l'intestazione `X-MSEdge-ClientID` consente alle API Bing di associare tutte le ricerche di un utente, cosa che presenta due vantaggi importanti.

Innanzitutto, consente al motore di ricerca Bing di applicare un contesto passato alle ricerche per trovare i risultati che meglio soddisfano l'utente. Se un utente ha cercato in precedenza termini correlati alla navigazione, ad esempio, una successiva ricerca di "nodi" potrebbe restituire preferibilmente informazioni sui nodi utilizzati nella navigazione.

In secondo luogo, Bing potrebbe selezionare utenti in modo casuale per provare nuove funzionalità prima di renderle disponibili. Fornire lo stesso ID client per tutte le richieste fa sì che gli utenti selezionati per la visualizzazione di una funzione la visualizzino sempre. Senza un ID client, l'utente potrebbe vedere una funzionalità apparire e scomparire apparentemente in modo casuale nei risultati delle ricerche.

I criteri di sicurezza del browser (CORS) potrebbero impedire che l'intestazione `X-MSEdge-ClientID` sia disponibile per JavaScript. Questa limitazione si verifica quando la risposta della ricerca ha un'origine diversa dalla pagina che l'ha richiesta. In un ambiente di produzione è necessario soddisfare questo criterio tramite l'hosting di uno script sul lato server che esegue la chiamata API nello stesso dominio della pagina Web. Poiché lo script ha la stessa origine della pagina Web, l'intestazione `X-MSEdge-ClientID` diventa disponibile per JavaScript.

> [!NOTE]
> In un'applicazione Web di produzione è consigliabile comunque eseguire la richiesta lato server. In caso contrario, la chiave dell'API di ricerca Bing deve essere inclusa nella pagina Web, dove sarà disponibile per chiunque visualizzi l'origine. All'utente vengono addebitati i costi per tutto l'utilizzo della chiave di sottoscrizione dell'API, anche le richieste effettuate da parti non autorizzate, pertanto è importante che la chiave non sia visibile.

A scopo di sviluppo, è possibile effettuare la richiesta API Ricerca Web Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

È facile installare un proxy CORS per consentire all'applicazione di esercitazione di accedere all'intestazione ID del client. Se non è disponibile, per prima cosa [installare Node.js](https://nodejs.org/en/download/). Digitare quindi il comando seguente in una finestra di comando:

    npm install -g cors-proxy-server

Poi modificare l'endpoint di Ricerca Web Bing nel file HTML in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando durante l'uso dell'applicazione di esercitazione. Se si chiude la finestra, il proxy si arresta. Nella sezione Intestazioni HTTP espandibile sotto i risultati della ricerca è ora possibile vedere l'intestazione `X-MSEdge-ClientID`, tra le altre, e verificare che sia la stessa per ogni richiesta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app per dispositivi mobili con Ricerca visiva](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Ricerca Web Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
