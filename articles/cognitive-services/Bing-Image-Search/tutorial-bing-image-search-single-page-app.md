---
title: Ricerca immagini Bing in un'app Web a pagina singola | Microsoft Docs
description: Illustra come usare l'API Ricerca immagini Bing in un'applicazione Web a pagina singola.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377028"
---
# <a name="tutorial-single-page-web-app"></a>Esercitazione: app Web a pagina singola

L'API Ricerca immagini Bing consente di eseguire ricerche sul Web e di ottenere come risultati immagini pertinenti alla query di ricerca. In questa esercitazione viene compilata un'applicazione Web a pagina singola che usa l'API Ricerca immagini Bing per visualizzare i risultati della ricerca direttamente nella pagina. L'applicazione include componenti HTML, CSS e JavaScript.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> Quando si fa clic sulle intestazioni JSON e HTTP nella parte inferiore della pagina, vengono visualizzate le informazioni relative alla risposta JSON e alla richiesta HTTP. Questi dettagli risultano utili durante l'esplorazione del servizio.

L'applicazione di esercitazione illustra come:

> [!div class="checklist"]
> * Eseguire una chiamata all'API Ricerca immagini Bing in JavaScript
> * Passare le opzioni di ricerca all'API Ricerca immagini Bing
> * Visualizzare i risultati della ricerca
> * Sfogliare i risultati della ricerca
> * Gestire l'ID del client Bing e la chiave di sottoscrizione dell'API
> * Gestire gli errori che potrebbero verificarsi

La pagina dell'esercitazione è autonoma, non usa framework, fogli di stile o file di immagine esterni. Usa solo funzionalità del linguaggio JavaScript ampiamente supportate e funziona con le versioni correnti di tutti i Web browser principali.

Questa esercitazione illustra solo alcune parti del codice sorgente. Il codice sorgente completo è disponibile [in un'altra pagina](tutorial-bing-image-search-single-page-app-source.md). Copiare e incollare il codice in un editor di testo e salvarlo come `bing.html`.

> [!NOTE]
> Questa esercitazione è essenzialmente simile all'[esercitazione su Ricerca Web Bing in un'app a pagina singola](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ma gestisce solo i risultati delle ricerche di immagini.

## <a name="app-components"></a>Componenti dell'app

Come qualsiasi app Web a pagina singola, l'applicazione di esercitazione include tre parti:

> [!div class="checklist"]
> * HTML, che definisce la struttura e il contenuto della pagina
> * CSS, che definisce l'aspetto della pagina
> * JavaScript, che definisce il comportamento della pagina

Questa esercitazione non tratta dettagliatamente il codice HTML o CSS, perché sono piuttosto semplici.

Il codice HTML contiene il modulo di ricerca in cui l'utente immette una query e sceglie le opzioni di ricerca. Il modulo è connesso a JavaScript che esegue effettivamente la ricerca usando l'attributo `onsubmit` del tag `<form>`:

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
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

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

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

Il tag `onsubmit` HTML `<form>` chiama la funzione `bingWebSearch` per restituire i risultati della ricerca. `bingWebSearch` usa `getSubscriptionKey` per autenticare ciascuna query. Come indicato nella definizione precedente, `getSubscriptionKey` richiede all'utente la chiave se non è stata immessa. La chiave viene quindi archiviata per continuare a essere usata dall'applicazione.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Selezione delle opzioni di ricerca

![[Modulo di Ricerca immagini Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Il modulo HTML include i controlli seguenti:

| | |
|-|-|
|`where`|Un menu a discesa per la selezione del mercato, ovvero località e lingua, utilizzato per la ricerca.|
|`query`|Il campo di testo in cui inserire i termini di ricerca.|
|`aspect`|Pulsante di opzione per scegliere le proporzioni delle immagini trovate: approssimativamente quadrate, larghe o alte.|
|`color`|Seleziona colore o bianco e nero o un colore predominante.
|`when`|Menu a discesa per limitare facoltativamente la ricerca all'ultimo giorno, settimana o mese.|
|`safe`|Una casella di controllo che indica se usare la funzionalità SafeSearch di Bing per filtrare i risultati con contenuti "per soli adulti".|
|`count`|Campo nascosto. Il numero di risultati della ricerca da restituire per ogni richiesta. Modificarlo per visualizzare un numero maggiore o minore di risultati per pagina.|
|`offset`|Campo nascosto. Lo scostamento del primo risultato della ricerca nella richiesta; utilizzato per il paging. Viene reimpostato su `0` per una nuova richiesta.|
|`nextoffset`|Campo nascosto. Quando riceve un risultato della ricerca, questo campo è impostato sul valore di `nextOffset` nella risposta. L'uso di questo campo consente di evitare risultati sovrapposti nelle pagine successive.|
|`stack`|Campo nascosto. Un elenco con codifica JSON degli scostamenti delle pagine di risultati di ricerca precedenti, per tornare alle pagine precedenti.|

> [!NOTE]
> Ricerca immagini Bing offre molti altri parametri di query. Qui ne vengono usati solo alcuni.

La funzione JavaScript `bingSearchOptions()` converte questi campi in una stringa di query parziale nel formato richiesto dall'API di ricerca Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Ad esempio, la funzione SafeSearch può essere `strict`, `moderate` o `off`, con `moderate` come impostazione predefinita. Il modulo, tuttavia, usa una casella di controllo che presenta solo due stati. Il codice JavaScript converte questa impostazione in `strict` o `off`; `moderate` non viene usato.

## <a name="performing-the-request"></a>Esecuzione della richiesta

Dati la query, la stringa delle opzioni e la chiave API, la funzione `BingImageSearch` usa un oggetto `XMLHttpRequest` per effettuare la richiesta all'endpoint di Ricerca immagini Bing.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> Una richiesta HTTP corretta *non* necessariamente indica che la ricerca sia andata a buon fine. Se si verifica un errore nell'operazione di ricerca, l'API Ricerca immagini Bing restituisce un codice di stato HTTP non-200 e include le informazioni sull'errore nella risposta JSON. Inoltre, se la richiesta era a frequenza limitata, l'API restituisce una risposta vuota.

La maggior parte del codice in entrambe le precedenti funzioni è dedicata alla gestione degli errori. Possono verificarsi errori nelle fasi seguenti:

|Fase|Errori potenziali|Gestito da|
|-|-|-|
|Compilazione dell'oggetto richiesta JavaScript|URL non valido|Blocco `try`/`catch`|
|Invio della richiesta|Errori di rete, connessioni interrotte|Gestori degli eventi `error` e `abort`|
|Esecuzione della ricerca|Richiesta non valida, JSON non valido, limiti di frequenza|Test nel gestore eventi `load`|

Gli errori vengono gestiti chiamando `renderErrorMessage()` con tutti i dettagli noti dell'errore. Se la risposta supera la serie completa dei test di errore, viene chiamato `renderSearchResults()` per visualizzare i risultati della ricerca nella pagina.

## <a name="displaying-search-results"></a>Visualizzazione dei risultati della ricerca

La funzione principale per la visualizzazione dei risultati della ricerca è `renderSearchResults()`. Questa funzione accetta la stringa JSON restituita dal servizio Ricerca immagini Bing ed esegue il rendering delle immagini e delle ricerche correlate, se presenti.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

I risultati della ricerca immagini principale vengono restituiti come oggetto di massimo livello `value` nella risposta JSON. Questi vengono inviati alla funzione `renderImageResults()` che esegue un'iterazione su essi e chiama una funzione separata per il rendering di ciascun elemento in HTML. Il codice HTML risultante viene restituito a `renderSearchResults()`, dove viene inserito nella divisione `results` della pagina.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

L'API Ricerca immagini Bing restituisce fino a quattro tipi di risultati correlati, ognuno dei quali nel proprio oggetto di massimo livello. Sono:

|||
|-|-|
|`pivotSuggestions`|Query che sostituiscono una parola pivot nella ricerca originale con una parola diversa. Ad esempio, se si cerca "fiori rossi", una parola pivot potrebbe essere "rossi" e un suggerimento pivot potrebbe essere "fiori gialli".|
|`queryExpansions`|Query che restringono la ricerca originale con l'aggiunta di nuovi termini. Ad esempio, se si cerca "Microsoft Surface", un'espansione della query potrebbe essere "Microsoft Surface Pro".|
|`relatedSearches`|Query che sono state inserite anche da altri utenti che hanno immesso la ricerca originale. Ad esempio, se si cerca "Mount Rainier", una ricerca correlata potrebbe essere "Mt. Saint Helens".|
|`similarTerms`|Query che sono simili nel significato alla ricerca originale. Ad esempio, se si cerca "gattino", un termine simile potrebbe essere "furbo".|

Come illustrato in precedenza in `renderSearchResults()`, viene eseguito il rendering solo dei suggerimenti `relatedItems` e i collegamenti risultanti vengono inseriti nella barra laterale della pagina.

## <a name="rendering-result-items"></a>Rendering degli elementi dei risultati

Nel codice JavaScript l'oggetto `searchItemRenderers` contiene i *renderer*: funzioni che generano codice HTML per ciascun tipo di risultato della ricerca.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Una funzione renderer può accettare i parametri seguenti:

| | |
|-|-|
|`item`|L'oggetto JavaScript che contiene le proprietà dell'elemento, ad esempio l'URL e la relativa descrizione.|
|`index`|L'indice dell'elemento dei risultati all'interno della raccolta.|
|`count`|Il numero di elementi nella raccolta degli elementi dei risultati della ricerca.|

I parametri `index` e `count` possono essere usati per numerare i risultati, per generare codice HTML speciale per l'inizio o la fine di una raccolta, per inserire le interruzioni di riga dopo un certo numero di elementi e così via. Se un renderer non necessita di questa funzionalità, non è necessario che accetti questi due parametri.

Analizziamo in dettaglio il renderer `images`:

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

La funzione renderer dell'immagine:

> [!div class="checklist"]
> * Calcola le dimensioni dell'anteprima immagine; la larghezza varia da un minimo di 120 pixel, mentre l'altezza è fissa a 90 pixel.
> * Compila il tag HTML `<img>` per visualizzare l'anteprima dell'immagine. 
> * Compila i tag HTML `<a>` che collegano l'immagine e la pagina che la contiene.
> * Compila la descrizione che consente di visualizzare informazioni sull'immagine e il sito in cui si trova.

Sarà testata la variabile `index` per inserire un tag `<p>` prima della prima immagine dei risultati. In caso contrario le anteprime sono una vicina all'altra e vanno a capo come necessario nella finestra del browser.

La dimensione dell'anteprima viene usata sia nel tag `<img>` che nei campi `h` e `w` dell'URL dell'anteprima. Il [servizio di anteprima Bing](resize-and-crop-thumbnails.md) invia un'anteprima esattamente di tali dimensioni.

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
> [Informazioni di riferimento per l'API Ricerca immagini Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

