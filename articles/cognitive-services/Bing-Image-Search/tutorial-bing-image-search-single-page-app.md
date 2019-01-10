---
title: "Esercitazione: Creare un'app Web di una pagina - API Ricerca immagini Bing"
titleSuffix: Azure cognitive services
description: L'API Ricerca immagini Bing consente di cercare nel Web immagini pertinenti di alta qualità. Usare questa esercitazione per compilare un'applicazione Web composta da una sola pagina in grado di inviare query di ricerca all'API e visualizzare i risultati all'interno della pagina Web.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: tutorial
ms.date: 9/12/2018
ms.author: aahi
ms.openlocfilehash: f7ab94c6d935222c65c1259f1ce3b01d10f78ec1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062860"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Esercitazione: Creare un'app di una pagina con l'API Ricerca immagini Bing

L'API Ricerca immagini Bing consente di cercare nel Web immagini pertinenti di alta qualità. Usare questa esercitazione per compilare un'applicazione Web composta da una sola pagina in grado di inviare query di ricerca all'API e visualizzare i risultati all'interno della pagina Web. Questa esercitazione è simile all'[esercitazione corrispondente](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) per Ricerca Web Bing.

L'applicazione di esercitazione illustra come:

> [!div class="checklist"]
> * Eseguire una chiamata all'API Ricerca immagini Bing in JavaScript
> * Migliorare i risultati della ricerca con le opzioni di ricerca
> * Visualizzare e sfogliare i risultati della ricerca
> * Richiedere e gestire una chiave di sottoscrizione API e un ID client Bing.

Il codice sorgente completo di questa esercitazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/).
* Il framework [Express.js](https://expressjs.com/) per Node.js. Le istruzioni di installazione del codice sorgente sono disponibili nel file Leggimi di esempio di GitHub.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Gestire e archiviare le chiavi di sottoscrizione utente

Questa applicazione usa l'archivio permanente dei Web browser per archiviare le chiavi di sottoscrizione API. Se non è archiviata alcuna chiave, la pagina Web richiede la chiave dell'utente e la archivia per uso futuro. Se in seguito la chiave viene rifiutata dall'API, l'app la rimuove dall'archivio.


Occorre definire le funzioni `storeValue` e `retrieveValue` per usare l'oggetto `localStorage`, se il browser lo supporta, o un cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

La funzione `getSubscriptionKey()` cerca di recuperare una chiave archiviata in precedenza usando `retrieveValue`. Se non ne trova una, chiede all'utente di immettere la propria chiave e la archivia mediante `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

## <a name="send-search-requests"></a>Inviare le richieste di ricerca

Questa applicazione usa un `<form>` HTML per inviare inizialmente le richieste di ricerca degli utenti, usando l'attributo `onsubmit` per chiamare `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Per impostazione predefinita, il gestore `onsubmit` restituisce `false`, che impedisce che il modulo venga inviato.

## <a name="select-search-options"></a>Selezionare le opzioni di ricerca

![[Modulo di Ricerca immagini Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

L'API Ricerca immagini Bing offre diversi [parametri di filtro delle query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#filter-query-parameters) per restringere e filtrare i risultati della ricerca. Il modulo HTML in questa applicazione usa e visualizza le opzioni di parametri seguenti:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Un menu a discesa per la selezione del mercato (località e lingua) usato per la ricerca.                                                                                             |
| `query`      | Il campo di testo in cui inserire i termini di ricerca.                                                                                                                                 |
| `aspect`     | Pulsanti di opzione per scegliere le proporzioni delle immagini trovate: approssimativamente quadrate, larghe o alte.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Menu a discesa per limitare facoltativamente la ricerca all'ultimo giorno, settimana o mese.                                                                                          |
| `safe`       | Una casella di controllo che indica se usare la funzionalità SafeSearch di Bing per filtrare i risultati con contenuti "per soli adulti".                                                                                      |
| `count`      | Campo nascosto. Il numero di risultati della ricerca da restituire per ogni richiesta. Modificarlo per visualizzare un numero maggiore o minore di risultati per pagina.                                                            |
| `offset`     | Campo nascosto. Lo scostamento del primo risultato della ricerca nella richiesta; utilizzato per il paging. Viene reimpostato su `0` per una nuova richiesta.                                                           |
| `nextoffset` | Campo nascosto. Quando riceve un risultato della ricerca, questo campo è impostato sul valore di `nextOffset` nella risposta. L'uso di questo campo consente di evitare risultati sovrapposti nelle pagine successive. |
| `stack`      | Campo nascosto. Un elenco con codifica JSON degli scostamenti delle pagine di risultati di ricerca precedenti, per tornare alle pagine precedenti.                                                      |

La funzione `bingSearchOptions()` formatta queste opzioni in una stringa di query parziale, che può essere usata nelle richieste API dell'app.  

```javascript
// Build query options from the HTML form
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

## <a name="performing-the-request"></a>Esecuzione della richiesta

Usando la query di ricerca, la stringa delle opzioni e la chiave API, la funzione `BingImageSearch()` usa un oggetto XMLHttpRequest per inviare la richiesta all'endpoint di Ricerca immagini Bing.


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

Al completamento corretto della richiesta HTTP, JavaScript chiama il gestore dell'evento "load" `handleBingResponse()` per gestire una richiesta HTTP GET corretta.

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
> Le richieste HTTP riuscite possono contenere informazioni sulle ricerche non riuscite. Se si verifica un errore durante l'operazione di ricerca, l'API Ricerca immagini Bing restituisce un codice di stato HTTP non-200 e le informazioni sull'errore nella risposta JSON. Inoltre, se la richiesta era a frequenza limitata, l'API restituisce una risposta vuota.

## <a name="display-the-search-results"></a>Visualizzare i risultati della ricerca

I risultati della ricerca vengono visualizzati dalla funzione `renderSearchResults()`, che accetta la stringa JSON restituita dal servizio Ricerca immagini Bing e chiama una funzione renderer appropriata su tutte le immagini restituite e sulle ricerche correlate.

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

I risultati della ricerca immagini sono contenuti nell'oggetto di primo livello `value` nella risposta JSON. Questi risultati vengono passati a `renderImageResults()`, che li scorre e converte ogni elemento in codice HTML.

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

L'API Ricerca immagini Bing può restituire quattro tipi di suggerimenti per la ricerca per facilitare le esperienze di ricerca degli utenti, ognuna nel proprio oggetto di primo livello:

| Suggerimento         | DESCRIZIONE                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Query che sostituiscono una parola pivot nella ricerca originale con una parola diversa. Ad esempio, se si cerca "fiori rossi", una parola pivot potrebbe essere "rossi" e un suggerimento pivot potrebbe essere "fiori gialli". |
| `queryExpansions`  | Query che restringono la ricerca originale con l'aggiunta di nuovi termini. Ad esempio, se si cerca "Microsoft Surface", un'espansione della query potrebbe essere "Microsoft Surface Pro".                                   |
| `relatedSearches`  | Query che sono state inserite anche da altri utenti che hanno immesso la ricerca originale. Ad esempio, se si cerca "Mount Rainier", una ricerca correlata potrebbe essere "Mt. Saint Helens".                       |
| `similarTerms`     | Query che sono simili nel significato alla ricerca originale. Ad esempio, se si cerca "gattino", un termine simile potrebbe essere "furbo".                                                                   |

L'applicazione esegue il rendering solo dei suggerimenti `relatedItems` e inserisce i collegamenti risultanti nella barra laterale della pagina.

## <a name="rendering-search-results"></a>Rendering dei risultati della ricerca

In questa applicazione l'oggetto `searchItemRenderers` contiene funzioni renderer che generano codice HTML per ciascun tipo di risultato della ricerca.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Queste funzioni renderer accettano i parametri seguenti:

| Parametro         | DESCRIZIONE                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | L'oggetto JavaScript che contiene le proprietà dell'elemento, ad esempio l'URL e la relativa descrizione. |
| `index` | L'indice dell'elemento di risultato all'interno della raccolta.                                          |
| `count` | Il numero di elementi nella raccolta degli elementi di risultati della ricerca.                                  |

I parametri `index` e `count` vengono usati per numerare i risultati, generare codice HTML per le raccolte e organizzare il contenuto. Nello specifico:

* Calcola le dimensioni dell'anteprima immagine; la larghezza varia da un minimo di 120 pixel, mentre l'altezza è fissa a 90 pixel.
* Compila il tag HTML `<img>` per visualizzare l'anteprima dell'immagine.
* Compila i tag HTML `<a>` che collegano l'immagine e la pagina che la contiene.
* Compila la descrizione che consente di visualizzare informazioni sull'immagine e il sito in cui si trova.

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

I valori `height` e `width` dell'immagine di anteprima vengono usati sia nel tag `<img>` che nei campi `h` e `w` dell'URL dell'anteprima. Questo consente a Bing di restituire [un'anteprima](resize-and-crop-thumbnails.md) esattamente di quelle dimensioni.

## <a name="persisting-client-id"></a>ID client persistente

Le risposte dalle API di ricerca Bing potrebbero includere un'intestazione `X-MSEdge-ClientID` che deve essere inviata all'API con le richieste successive. Se vengono usate più API di ricerca Bing, usare se possibile lo stesso ID client con tutte le API.

Fornire l'intestazione `X-MSEdge-ClientID` consente alle API Bing di associare tutte le ricerche di un utente, cosa che presenta due vantaggi importanti.

Innanzitutto, consente al motore di ricerca Bing di applicare un contesto passato alle ricerche per trovare i risultati che meglio soddisfano l'utente. Se un utente ha cercato in precedenza termini correlati alla navigazione, ad esempio, una successiva ricerca di "nodi" potrebbe restituire preferibilmente informazioni sui nodi utilizzati nella navigazione.

In secondo luogo, Bing potrebbe selezionare utenti in modo casuale per provare nuove funzionalità prima di renderle disponibili. Fornire lo stesso ID client per tutte le richieste fa sì che gli utenti selezionati per la visualizzazione di una funzione la visualizzino sempre. Senza un ID client, l'utente potrebbe vedere una funzionalità apparire e scomparire apparentemente in modo casuale nei risultati delle ricerche.

I criteri di sicurezza del browser (CORS) potrebbero impedire che l'intestazione `X-MSEdge-ClientID` sia disponibile per JavaScript. Questa limitazione si verifica quando la risposta della ricerca ha un'origine diversa dalla pagina che l'ha richiesta. In un ambiente di produzione è necessario soddisfare questo criterio tramite l'hosting di uno script sul lato server che esegue la chiamata API nello stesso dominio della pagina Web. Poiché lo script ha la stessa origine della pagina Web, l'intestazione `X-MSEdge-ClientID` diventa disponibile per JavaScript.

> [!NOTE]
> In un'applicazione Web di produzione è consigliabile comunque eseguire la richiesta lato server. In caso contrario, la chiave dell'API di ricerca Bing deve essere inclusa nella pagina Web, dove sarà disponibile per chiunque visualizzi l'origine. All'utente vengono addebitati costi per tutto l'utilizzo della chiave di sottoscrizione API, anche le richieste effettuate da parti non autorizzate, pertanto è importante che la chiave non sia visibile.

A scopo di sviluppo, è possibile effettuare la richiesta API Ricerca Web Bing tramite un proxy CORS. La risposta da un proxy di questo tipo ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

È facile installare un proxy CORS per consentire all'applicazione di esercitazione di accedere all'intestazione ID client. Se non è disponibile, per prima cosa [installare Node.js](https://nodejs.org/en/download/). Digitare quindi il comando seguente in una finestra di comando:

    npm install -g cors-proxy-server

Poi modificare l'endpoint di Ricerca Web Bing nel file HTML in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Infine avviare il proxy CORS con il comando seguente:

    cors-proxy-server

Lasciare aperta la finestra di comando mentre si usa l'app dell'esercitazione. La chiusura della finestra determina l'arresto del proxy. Nella sezione Intestazioni HTTP espandibile sotto i risultati della ricerca è ora possibile vedere l'intestazione `X-MSEdge-ClientID`, tra le altre, e verificare che sia la stessa per ogni richiesta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre i dettagli dell'immagine usando l'API Ricerca immagini Bing](tutorial-image-post.md)

## <a name="see-also"></a>Vedere anche 

* [Informazioni di riferimento per l'API Ricerca immagini Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
