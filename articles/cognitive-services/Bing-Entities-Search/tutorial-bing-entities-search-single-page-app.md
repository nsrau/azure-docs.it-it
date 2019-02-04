---
title: "Esercitazione: Ricerca entità Bing in un'app Web a pagina singola"
titlesuffix: Azure Cognitive Services
description: Illustra come usare l'API Ricerca entità Bing in un'applicazione Web a pagina singola.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 12/08/2017
ms.author: aahi
ms.openlocfilehash: f8aa6733748bbdf9ffff069126f6481c8b6788c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197433"
---
# <a name="tutorial-single-page-web-app"></a>Esercitazione: App Web a singola pagina

L'API Ricerca entità Bing consente di cercare informazioni su *entità* e *località* nel Web. È possibile richiedere un tipo di risultato, oppure entrambi, in una determinata query. Di seguito vengono fornite le definizioni di località ed entità.

|||
|-|-|
|Entità|Persone famose, oggetti e località note individuabili in base al nome|
|Località|Ristoranti, hotel e altre aziende locali individuabili in base al nome *o* al tipo (ristoranti italiani)|

In questa esercitazione viene compilata un'applicazione Web a pagina singola che usa l'API Ricerca entità Bing per visualizzare i risultati della ricerca direttamente nella pagina. L'applicazione include componenti HTML, CSS e JavaScript.

L'API consente di definire priorità per i risultati in base alla località. In un'app per dispositivi mobili è possibile chiedere la posizione del dispositivo. In un'app Web è possibile usare la funzione `getPosition()`. Questa chiamata funziona comunque solo in contesti protetti e potrebbe non garantire una posizione precisa. L'utente può anche decidere di cercare entità vicino a una località diversa da quella in cui si trova.

L'app si affida quindi al servizio Bing Mappe per ottenere latitudine e longitudine da una località immessa dall'utente. L'utente può quindi immettere il nome di un punto di riferimento ("Space Needle") o un indirizzo completo o parziale ("New York City") e l'API Bing Mappe fornisce le coordinate.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Quando si fa clic sulle intestazioni JSON e HTTP nella parte inferiore della pagina, vengono visualizzate le informazioni relative alla risposta JSON e alla richiesta HTTP. Questi dettagli risultano utili durante l'esplorazione del servizio.

L'applicazione di esercitazione illustra come:

> [!div class="checklist"]
> * Eseguire una chiamata all'API Ricerca entità Bing in JavaScript
> * Eseguire una chiamata all'API `locationQuery` di Bing Mappe in JavaScript
> * Passare le opzioni di ricerca alle chiamate API
> * Visualizzare i risultati della ricerca
> * Gestire l'ID del client Bing e le chiavi di sottoscrizione dell'API
> * Gestire gli errori che potrebbero verificarsi

La pagina dell'esercitazione è completa, non usa framework esterni, fogli di stile o file di immagine. Usa solo funzionalità del linguaggio JavaScript ampiamente supportate e funziona con le versioni correnti di tutti i Web browser principali.

Questa esercitazione illustra solo alcune parti del codice sorgente. Il codice sorgente completo è disponibile [in un'altra pagina](tutorial-bing-entities-search-single-page-app-source.md). Copiare e incollare il codice in un editor di testo e salvarlo come `bing.html`.

> [!NOTE]
> Questa esercitazione è essenzialmente simile all'[esercitazione su Ricerca Web Bing in un'app a pagina singola](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ma gestisce solo i risultati delle ricerche di entità.

## <a name="app-components"></a>Componenti dell'app

Come qualsiasi app Web a pagina singola, l'applicazione di esercitazione include tre parti:

> [!div class="checklist"]
> * HTML, che definisce la struttura e il contenuto della pagina
> * CSS, che definisce l'aspetto della pagina
> * JavaScript, che definisce il comportamento della pagina

Questa esercitazione non tratta dettagliatamente il codice HTML o CSS, perché sono piuttosto semplici.

Il codice HTML contiene il modulo di ricerca in cui l'utente immette una query e sceglie le opzioni di ricerca. Il modulo è connesso a JavaScript che esegue effettivamente la ricerca usando l'attributo `onsubmit` del tag `<form>`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Il gestore `onsubmit` restituisce `false`, che impedisce che il modulo venga inviato a un server. Il codice JavaScript esegue le operazioni di raccolta delle informazioni necessarie dal modulo ed effettua la ricerca.

La ricerca viene eseguita in due fasi. In primo luogo, se l'utente ha immesso una restrizione a livello di località, viene eseguita una query di Bing Mappe per convertirla in coordinate. Il callback di questa query avvia la query di Ricerca entità Bing.

Il codice HTML contiene anche le divisioni (tag HTML `<div>`) in cui vengono visualizzati i risultati della ricerca.

## <a name="managing-subscription-keys"></a>Gestione delle chiavi di sottoscrizione

> [!NOTE]
> Questa app richiede chiavi di sottoscrizione per l'API di ricerca Bing e l'API di Bing Mappe. È possibile usare una [chiave di Ricerca Bing di valutazione](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) e una [chiave di Bing Mappe di base](https://www.microsoft.com/maps/create-a-bing-maps-key).

Per evitare di includere la chiave di sottoscrizione dell'API di ricerca Bing e dell'API di Bing Mappe nel codice, viene usata l'archiviazione permanente del browser per archiviare le chiavi. Se una chiave non è stata archiviata, verrà richiesto di archiviarla per un uso futuro. Se in un secondo momento la chiave viene rifiutata dall'API, viene invalidata la chiave archiviata in modo che l'utente riceverà nuovamente la richiesta alla ricerca successiva.

Vengono definite le funzioni `storeValue` e `retrieveValue` che usano l'oggetto `localStorage` (se il browser lo supporta) o un cookie. La funzione `getSubscriptionKey()` usa queste funzioni per archiviare e recuperare la chiave dell'utente.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

Il tag `<body>` HTML include un attributo `onload` che chiama `getSearchSubscriptionKey()` e `getMapsSubscriptionKey()` al termine del caricamento della pagina. Queste chiamate hanno lo scopo di richiedere subito all'utente le chiavi, se ancora non sono state immesse.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Selezione delle opzioni di ricerca

![[Modulo di Ricerca entità Bing]](media/entity-search-spa-form.png)

Il modulo HTML include i controlli seguenti:

| | |
|-|-|
|`where`|Un menu a discesa per la selezione del mercato, ovvero località e lingua, utilizzato per la ricerca.|
|`query`|Il campo di testo in cui inserire i termini di ricerca.|
|`safe`|Una casella di controllo che indica se SafeSearch è attivato (limita i risultati "per adulti")|
|`what`|Un menu per scegliere se cercare entità, località o entrambe.|
|`mapquery`|Il campo di testo in cui l'utente può immettere un indirizzo completo o parziale, un punto di riferimento e così via per consentire a Ricerca entità Bing di restituire risultati più pertinenti.|

> [!NOTE]
> I risultati relativi alle località sono attualmente disponibili solo negli Stati Uniti. I menu `where` e `what` includono codice per applicare questa restrizione. Se si sceglie un mercato diverso dagli Stati Uniti mentre Places (Località) è selezionato nel menu `what`, `what` diviene Anything (Qualsiasi cosa). Se si sceglie Places (Località) mentre è selezionato un mercato diverso dagli Stati Uniti nel menu `where`, `where` diviene US.

La funzione JavaScript `bingSearchOptions()` converte questi campi in una stringa di query parziale per l'API di ricerca Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Ad esempio, la funzione SafeSearch può essere `strict`, `moderate` o `off`, con `moderate` come impostazione predefinita. Il modulo, tuttavia, usa una casella di controllo che presenta solo due stati. Il codice JavaScript converte questa impostazione in `strict` o `off` (`moderate` non viene usato).

Il campo `mapquery` non è gestito in `bingSearchOptions()`, poiché viene usato per la query di località di Bing Mappe e non per Ricerca entità Bing.

## <a name="obtaining-a-location"></a>Come ottenere una località

L'API di Bing Mappe offre un [metodo `locationQuery`](//msdn.microsoft.com/library/ff701711.aspx), usato per trovare la latitudine e longitudine della località immessa dall'utente. Queste coordinate vengono poi passate all'API Ricerca entità Bing con la richiesta dell'utente. I risultati della ricerca assegnano priorità alle entità e alle località vicine alla posizione specificata.

Non è possibile accedere all'API di Bing Mappe con una query `XMLHttpRequest` normale in un'app Web, poiché il servizio non supporta query tra origini, ma supporta JSONP ("P" sta per "Padded"). Una risposta JSONP è una risposta JSON normale sottoposta a wrapping nella chiamata di una funzione. La richiesta viene eseguita mediante l'inserimento di un tag `<script>` nel documento. Il caricamento di script non è soggetto ai criteri di sicurezza del browser.

La funzione `bingMapsLocate()` crea e inserisce il tag `<script>` per la query. Il segmento `jsonp=bingMapsCallback` della stringa di query specifica il nome della funzione da chiamare con la risposta.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Se l'API di Bing Mappe non risponde, la funzione `bingMapsCallBack()` non viene mai chiamata. In genere, ciò implica che `bingEntitySearch()` non viene chiamata e non vengono visualizzati i risultati della ricerca di entità. Per evitare questo scenario, `bingMapsLocate()` imposta anche un timer per chiamare `bingEntitySearch()` dopo cinque secondi. La logica presente nella funzione di callback evita di eseguire due volte la ricerca di entità.

Al termine dell'esecuzione della query, viene chiamata la funzione `bingMapsCallback()` come richiesto.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Insieme a latitudine e longitudine, la query di Ricerca entità Bing richiede un *raggio* che indica la precisione delle informazioni sulla località. Il raggio viene calcolato usando il *rettangolo di selezione* fornito nella risposta di Bing Mappe. Il rettangolo di selezione racchiude l'intera località. Ad esempio, se l'utente immette `NYC`, il risultato contiene coordinate approssimativamente centrali di New York City e un rettangolo di selezione che racchiude la città. 

Vengono innanzitutto calcolate le distanze dalle coordinate primarie a ognuno dei quattro angoli del rettangolo di selezione usando la funzione `haversineDistance()` (non illustrata). Viene usata come raggio la distanza maggiore tra le quattro. Il raggio minimo è un chilometro. Questo valore viene anche usato come impostazione predefinita se nella risposta non viene fornito alcun rettangolo di selezione.

Dopo aver ottenuto le coordinate e il raggio, verrà chiamato `bingEntitySearch()` per eseguire la ricerca effettiva.

## <a name="performing-the-search"></a>Esecuzione della ricerca

Una volta fornite la query, una località, una stringa di opzioni e la chiave API, la funzione `BingEntitySearch()` effettua la richiesta di Ricerca entità Bing.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Una richiesta HTTP corretta *non* indica necessariamente che la ricerca stessa sia andata a buon fine. Se si verifica un errore nell'operazione di ricerca, l'API Ricerca entità Bing restituisce un codice di stato HTTP non-200 e include le informazioni sull'errore nella risposta JSON. Inoltre, se la richiesta era a frequenza limitata, l'API restituisce una risposta vuota.

La maggior parte del codice in entrambe le funzioni precedenti è dedicata alla gestione degli errori. Possono verificarsi errori nelle fasi seguenti:

|Fase|Errori potenziali|Gestiti da|
|-|-|-|
|Compilazione dell'oggetto richiesta JavaScript|URL non valido|Blocco `try`/`catch`|
|Invio della richiesta|Errori di rete, connessioni interrotte|Gestori degli eventi `error` e `abort`|
|Esecuzione della ricerca|Richiesta non valida, JSON non valido, limiti di frequenza|Test nel gestore dell'evento `load`|

Gli errori vengono gestiti chiamando `renderErrorMessage()` con tutti i dettagli noti dell'errore. Se la risposta supera la serie completa dei test di errore, viene chiamato `renderSearchResults()` per visualizzare i risultati della ricerca nella pagina.

## <a name="displaying-search-results"></a>Visualizzazione dei risultati della ricerca

L'API Ricerca entità Bing [richiede di visualizzare i risultati in un ordine specifico](use-display-requirements.md). Poiché l'API può restituire due diversi tipi di risposte, non è sufficiente eseguire l'iterazione alla raccolta `Entities` o `Places` di primo livello nella risposta JSON e visualizzare i risultati. Per ricevere un solo tipo di risultato, usare il parametro di query `responseFilter`.

Viene invece usata la raccolta `rankingResponse` nei risultati della ricerca per ordinare i risultati per la visualizzazione. Questo oggetto fa riferimento a elementi nelle raccolte `Entitiess` e/o `Places`.

`rankingResponse` può contenere fino a tre raccolte di risultati della ricerca, designate `pole`, `mainline` e `sidebar`. 

`pole`, se presente, è il risultato della ricerca più pertinente e deve essere visualizzato in primo piano. `mainline` fa riferimento alla maggior parte dei risultati della ricerca. I risultati di tipo mainline devono essere visualizzati immediatamente dopo `pole`, o prima se `pole` non è presente. 

Infine, `sidebar` si riferisce a risultati ausiliari della ricerca. Possono essere visualizzati in un'effettiva barra laterale o semplicemente dopo i risultati di tipo mainline. Per l'app di esercitazione è stato scelto il secondo caso.

Ogni elemento in una raccolta `rankingResponse` fa riferimento agli effettivi elementi dei risultati della ricerca in due modi diversi, ma equivalenti.

| | |
|-|-|
|`id`|`id` è simile a un URL, ma non deve essere usato per i collegamenti. Il tipo `id` di un risultato di classificazione corrisponde al tipo `id` di un elemento dei risultati della ricerca in una raccolta di risposte *o* un'intera raccolta di risposte, ad esempio `Entities`.
|`answerType`<br>`resultIndex`|`answerType` fa riferimento alla raccolta di risposte di massimo livello contenente il risultato, ad esempio `Entities`. `resultIndex` fa riferimento all'indice del risultato all'interno della raccolta. Se `resultIndex` viene omesso, il risultato di classificazione si riferisce all'intera raccolta.

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

    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Rendering degli elementi di risultati

Nel codice JavaScript l'oggetto `searchItemRenderers` contiene i *renderer*: funzioni che generano codice HTML per ciascun tipo di risultato della ricerca.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Una funzione renderer può accettare i parametri seguenti:

| | |
|-|-|
|`item`|L'oggetto JavaScript che contiene le proprietà dell'elemento, ad esempio l'URL e la relativa descrizione.|
|`index`|L'indice dell'elemento di risultato all'interno della raccolta.|
|`count`|Il numero di elementi nella raccolta degli elementi di risultati della ricerca.|

I parametri `index` e `count` possono essere usati per numerare i risultati, per generare codice HTML speciale per l'inizio o la fine di una raccolta, per inserire le interruzioni di riga dopo un certo numero di elementi e così via. Se un renderer non necessita di questa funzionalità, non è necessario che accetti questi due parametri. Nei renderer dell'app di esercitazione non verranno usati.

Analizziamo in dettaglio il renderer `entities`:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

La funzione renderer dell'entità:

> [!div class="checklist"]
> * Compila il tag HTML `<img>` per visualizzare l'anteprima dell'immagine, se disponibile. 
> * Compila il tag HTML `<a>` per il collegamento alla pagina contenente l'immagine.
> * Compila la descrizione che consente di visualizzare informazioni sull'immagine e il sito in cui si trova.
> * Incorpora la classificazione dell'entità mediante hint di visualizzazione, se presenti.
> * Include un collegamento a una ricerca Bing per ottenere altre informazioni sull'entità.
> * Visualizza le informazioni di licenza o attribuzione richieste da dalle origini dati.

## <a name="persisting-client-id"></a>ID client persistente

Le risposte dalle API di ricerca Bing potrebbero includere un'intestazione `X-MSEdge-ClientID` che deve essere inviata all'API con le richieste successive. Se vengono usate più API di ricerca Bing, usare se possibile lo stesso ID client con tutte le API.

Fornire l'intestazione `X-MSEdge-ClientID` consente alle API Bing di associare tutte le ricerche di un utente, cosa che presenta due vantaggi importanti.

Innanzitutto, consente al motore di ricerca Bing di applicare contesto passato alle ricerche per trovare i risultati che meglio soddisfano l'utente. Se un utente ha cercato in precedenza termini correlati alla navigazione, ad esempio, una successiva ricerca di "moli" potrebbe restituire preferibilmente informazioni sulle località con moli per una barca a vela.

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
> [Riferimento API Ricerca entità Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Documentazione sulle API di Bing Mappe](//msdn.microsoft.com/library/dd877180.aspx)
