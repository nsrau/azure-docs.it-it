---
title: "Esercitazione: Creare un'app Web di una pagina - API Ricerca Web Bing"
titleSuffix: Azure Cognitive Services
description: Questa app di una pagina offre una dimostrazione dell'uso dell'API Ricerca Web Bing per recuperare, analizzare e visualizzare i risultati della ricerca pertinenti in un'app composta da una sola pagina.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: aahi
ms.openlocfilehash: b94e6f70d4a28dc59ddaa62cfad55c6c8383b761
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161923"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Esercitazione: Creare un'app di una pagina con l'API Ricerca Web Bing

Questa app di una pagina offre una dimostrazione su come recuperare, analizzare e visualizzare i risultati della ricerca dall'API Ricerca Web Bing. L'esercitazione usa codice HTML e CSS boilerplate ed è incentrata sul codice JavaScript. I file HTML, CSS e JS sono disponibili su [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) insieme a istruzioni di avvio rapido.

Questa app di esempio può:

> [!div class="checklist"]
> * Chiamare l'API Ricerca Web Bing con le opzioni di ricerca
> * Visualizzare risultati su contenuti Web, immagini, notizie e video
> * Impaginare i risultati
> * Gestire le chiavi di sottoscrizione
> * Gestire gli errori

Per usare questa app è necessario un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con le API di ricerca Bing. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire l'app occorre:

* Node.js 8 o versione successiva
* Una chiave di sottoscrizione

## <a name="get-the-source-code-and-install-dependencies"></a>Ottenere il codice sorgente e installare le dipendenze

Il primo passaggio consiste nel clonare il repository con il codice sorgente dell'app.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Quindi eseguire `npm install`. Per questa esercitazione, Express.js è l'unica dipendenza.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Componenti dell'app

L'app di esempio che si sta creando è costituita da quattro parti:

* `bing-web-search.js`: l'app Express.js. Gestisce la logica di richiesta/risposta e il routing.
* `public/index.html`: lo scheletro dell'app. Definisce il modo in cui i dati vengono presentati all'utente.
* `public/css/styles.css`: definisce gli stili di pagina, come i tipi di carattere, i colori e le dimensioni del testo.
* `public/js/scripts.js`: contiene la logica per inviare richieste all'API Ricerca Web Bing, gestire le chiavi di sottoscrizione, gestire e analizzare le risposte e visualizzare i risultati.

Questa esercitazione è incentrata su `scripts.js` e sulla logica necessaria per chiamare l'API Ricerca Web Bing e gestire la risposta.

## <a name="html-form"></a>Form HTML

`index.html` include un modulo che consente agli utenti di cercare e selezionare opzioni di ricerca. L'attributo `onsubmit` viene attivato all'invio del modulo, chiamando il metodo `bingWebSearch()` definito in `scripts.js`. Accetta tre argomenti:

* Query di ricerca
* Opzioni selezionate
* Chiave della sottoscrizione

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Opzioni di query

Il modulo HTML include opzioni che eseguono il mapping ai parametri di query nell'[API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters). La tabella seguente mostra in che modo gli utenti possono filtrare i risultati della ricerca usando l'app di esempio:

| Parametro | DESCRIZIONE |
|-----------|-------------|
| `query` | Campo di testo in cui immettere una stringa di query. |
| `where` | Menu a discesa in cui selezionare il mercato (località e lingua). |
| `what` | Caselle di controllo per alzare di livello tipi di risultati specifici. Alzando di livello le immagini, ad esempio, se ne alza la classificazione nei risultati della ricerca. |
| `when` | Menu a discesa che consente all'utente di limitare i risultati della ricerca alla data odierna, alla settimana corrente o al mese corrente. |
| `safe` | Casella di controllo che consente di abilitare Ricerca sicura Bing, che esclude i contenuti per adulti. |
| `count` | Campo nascosto. Il numero di risultati della ricerca da restituire per ogni richiesta. Modificare questo valore per visualizzare un numero maggiore o minore di risultati per pagina. |
| `offset` | Campo nascosto. Lo scostamento del primo risultato della ricerca nella richiesta, che viene usato per l'impaginazione. Viene reimpostato su `0` con ogni nuova richiesta. |

> [!NOTE]
> L'API Ricerca Web Bing offre altri parametri di query per affinare i risultati della ricerca. Questo esempio ne usa solo alcuni. Per un elenco completo dei parametri disponibili, vedere [Informazioni di riferimento per l'API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters).

La funzione `bingSearchOptions()` converte queste opzioni nel formato richiesto dall'API di ricerca Bing.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch`può essere impostato su `strict`, `moderate` o `off`. `moderate` è l'impostazione predefinita per Ricerca Web Bing. Questo modulo usa una casella di controllo che presenta due stati. In questo frammento di codice Ricerca sicura è impostata su `strict` o `off`. `moderate` non viene usato.

Se una qualsiasi delle caselle di controllo **Alza di livello** è selezionata, il parametro `answerCount` viene aggiunto alla query. `answerCount` è necessario quando si usa il parametro `promote`. In questo frammento di codice il valore è impostato su `9` per restituire tutti i tipi di risultati disponibili.
> [!NOTE]
> L'innalzamento di livello di un tipo di risultato non *garantisce* che verrà incluso nei risultati della ricerca. ma si alza la classificazione di questi tipi di risultati rispetto alla loro classificazione consueta. Per limitare le ricerche a determinati tipi di risultati, usare il parametro di query `responseFilter` o chiamare un endpoint più specifico, ad esempio Ricerca immagini Bing o Ricerca notizie Bing.

I parametri di query `textDecoration` e `textFormat` sono hardcoded nello script e fanno in modo che il termine di ricerca appaia in grassetto nei risultati della ricerca. Questi parametri non sono obbligatori.

## <a name="manage-subscription-keys"></a>Gestire le chiavi di sottoscrizione

Per evitare l'hardcoding della chiave di sottoscrizione dell'API di ricerca Bing, questa app di esempio usa l'archiviazione permanente del browser per archiviare la chiave. Se non è archiviata alcuna chiave di sottoscrizione, all'utente viene chiesto di immetterne una. Se la chiave di sottoscrizione viene rifiutata dall'API, all'utente viene chiesto di reimmetterne una.

La funzione `getSubscriptionKey()` usa le funzioni `storeValue` e `retrieveValue` per archiviare e recuperare la chiave di sottoscrizione di un utente. Queste funzioni usano l'oggetto `localStorage`, se supportato, oppure i cookie.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Come abbiamo visto in precedenza, all'invio del modulo viene attivato `onsubmit`, che chiama `bingWebSearch`. Questa funzione inizializza e invia la richiesta. `getSubscriptionKey` viene chiamato a ogni invio per autenticare la richiesta.

## <a name="call-bing-web-search"></a>Chiamare Ricerca Web Bing

Dati la query, la stringa delle opzioni e la chiave di sottoscrizione, la funzione `BingWebSearch` crea un oggetto `XMLHttpRequest` per chiamare l'endpoint di Ricerca Web Bing.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Se la richiesta riesce, viene attivato il gestore dell'evento `load` che chiama la funzione `handleBingResponse`. `handleBingResponse` analizza l'oggetto risultato, visualizza i risultati e contiene la logica di errore per le richieste non riuscite.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
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

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Una richiesta HTTP corretta *non* indica necessariamente che la ricerca sia riuscita. Se si verifica un errore nell'operazione di ricerca, l'API Ricerca Web Bing restituisce un codice di stato HTTP non-200 e include le informazioni sull'errore nella risposta JSON. Se la richiesta era a frequenza limitata, l'API restituisce una risposta vuota.

La maggior parte del codice in entrambe le funzioni precedenti è dedicata alla gestione degli errori. Possono verificarsi errori nelle fasi seguenti:

| Fase | Errori potenziali | Gestiti da |
|-------|--------------------|------------|
| Compilazione dell'oggetto della richiesta | URL non valido | Blocco `try` / `catch` |
| Invio della richiesta | Errori di rete, connessioni interrotte | Gestori degli eventi `error` e `abort` |
| Esecuzione della ricerca | Richiesta non valida, JSON non valido, limiti di frequenza | Test nel gestore dell'evento `load` |

Gli errori vengono gestiti chiamando `renderErrorMessage()`. Se la risposta supera tutti i test di errore, viene chiamato `renderSearchResults()` per visualizzare i risultati della ricerca.

## <a name="display-search-results"></a>Visualizzare i risultati della ricerca

I risultati restituiti dall'API Ricerca Web Bing sono soggetti a [requisiti di utilizzo e visualizzazione](useanddisplayrequirements.md). Poiché una risposta può includere vari tipi di risultati, non è sufficiente scorrere la raccolta `WebPages` di primo livello. L'app di esempio usa invece `RankingResponse` per ordinare i risultati secondo un criterio specifico.

> [!NOTE]
> Se si vuole visualizzare un solo tipo di risultato, usare il parametro di query `responseFilter` oppure provare a usare uno degli altri endpoint di Ricerca Bing, come Ricerca immagini Bing.

Ogni risposta ha un oggetto `RankingResponse` che può includere fino a tre raccolte: `pole`, `mainline` e `sidebar`. `pole`, se presente, è il risultato della ricerca più pertinente e deve essere visualizzato in primo piano. `mainline` contiene la maggior parte dei risultati della ricerca e viene visualizzato immediatamente dopo `pole`. `sidebar` include risultati della ricerca ausiliari. Se possibile, questi risultati devono essere visualizzati nella barra laterale. Se i limiti dello schermo non consentono di mostrare una barra laterale, devono essere visualizzati dopo i risultati `mainline`.

Ogni `RankingResponse` include una matrice di `RankingItem` che specifica come devono essere ordinati i risultati. L'app di esempio usa i parametri `answerType` e `resultIndex` per identificare il risultato.

> [!NOTE]
> È possibile identificare e classificare i risultati anche in altri modi. Per altre informazioni, vedere [Uso della classificazione per visualizzare i risultati](rank-results.md).

Esaminiamo il codice:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

La funzione `renderResultsItems()` scorre gli elementi in ogni raccolta `RankingResponse`, esegue il mapping di ogni risultato della classificazione a un risultato della ricerca mediante i valori `answerType` e `resultIndex` e chiama la funzione di rendering appropriata per generare l'HTML. Se `resultIndex` non viene specificato per un elemento, `renderResultsItems()` scorre tutti i risultati di quel tipo e chiama la funzione di rendering per ogni elemento. Il codice HTML risultante viene inserito nell'elemento `<div>` appropriato in `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
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

## <a name="review-renderer-functions"></a>Esaminare le funzioni renderer

Nell'app di esempio l'oggetto `searchItemRenderers` include funzioni che generano codice HTML per ogni tipo di risultato della ricerca.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> L'app include renderer per pagine Web, notizie, immagini, video e ricerche correlate. Un'applicazione deve avere renderer per tutti i tipi di risultati che può ricevere, fra cui possono essere inclusi calcoli, suggerimenti di ortografia, entità, fusi orari e definizioni.

Alcune delle funzioni di rendering accettano solo il parametro `item`. Altre accettano parametri aggiuntivi, che possono essere usati per eseguire il rendering degli elementi in modo diverso in base al contesto. Un renderer che non usa queste informazioni non ha bisogno di accettare questi parametri.

Gli argomenti di contesto sono:

| Parametro  | DESCRIZIONE |
|------------|-------------|
| `section` | La sezione relativa ai risultati, ovvero `pole`, `mainline` o `sidebar`, in cui l'elemento compare. |
| `index`<br>`count` | Disponibile quando l'elemento `RankingResponse` specifica che tutti i risultati di una determinata raccolta devono essere visualizzati; `undefined` in caso contrario. L'indice dell'elemento all'interno della raccolta e il numero totale di elementi della raccolta. È possibile usare queste informazioni per numerare i risultati, per generare codice HTML diverso per il primo o l'ultimo risultato e così via. |

Nell'app di esempio entrambi i renderer `images` e `relatedSearches` usano gli argomenti di contesto per personalizzare il codice HTML generato. Analizziamo in dettaglio il renderer `images`:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
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
    },
    // Other renderers are omitted from this sample...
}
```

Il renderer dell'immagine:

* Calcola le dimensioni dell'anteprima immagine; la larghezza varia, mentre l'altezza è fissa a 60 pixel.
* Inserisce il codice HTML che precede l'immagine ottenuta come risultato in base al contesto.
* Compila il tag HTML `<a>` per il collegamento alla pagina contenente l'immagine.
* Compila il tag HTML `<img>` per visualizzare l'anteprima dell'immagine.

Usa il renderer di immagini per le variabili `section` e `index` per visualizzare i risultati in modo diverso a seconda di dove vengono mostrati. Un'interruzione di riga, tag `<br>`, viene inserita tra le immagini ottenute come risultato nella barra laterale, in modo che la barra laterale mostri una colonna di immagini. In altre sezioni la prima immagine dei risultati `(index === 0)` è preceduta da un tag `<p>`.

La dimensione dell'anteprima viene usata sia nel tag `<img>` che nei campi `h` e `w` dell'URL dell'anteprima. Gli attributi `title` e `alt`, una descrizione testuale dell'immagine, sono costruiti dal nome dell'immagine e il nome host nell'URL.

Ecco un esempio del modo in cui vengono visualizzate le immagini nell'app di esempio:

![[Immagine ottenute come risultato da Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Salvare l'ID client in modo permanente

Le risposte dalle API di ricerca Bing potrebbero includere un'intestazione `X-MSEdge-ClientID` che deve essere reinviata all'API con ogni richiesta successiva. Se l'app usa più di un'API di ricerca Bing, assicurarsi che venga inviato lo stesso ID client con ogni richiesta ai vari servizi.

Fornire l'intestazione `X-MSEdge-ClientID` consente alle API Bing di associare le ricerche di un utente. In questo modo consente al motore di ricerca Bing di applicare un contesto passato alle ricerche per trovare i risultati che meglio soddisfano la richiesta. Se un utente ha cercato in precedenza termini correlati alla navigazione, ad esempio, una successiva ricerca di "nodi" potrebbe restituire preferibilmente informazioni sui nodi utilizzati nella navigazione. In secondo luogo, Bing potrebbe selezionare utenti in modo casuale per provare nuove funzionalità prima di renderle disponibili. Fornire lo stesso ID client per tutte le richieste fa sì che gli utenti selezionati per la visualizzazione di una funzionalità la visualizzino sempre. Senza un ID client, l'utente potrebbe vedere una funzionalità apparire e scomparire apparentemente in modo casuale nei risultati delle ricerche.

I criteri di sicurezza del browser, come Condivisione risorse tra le origini (CORS), potrebbero impedire all'app di esempio di accedere all'intestazione `X-MSEdge-ClientID`. Questa limitazione si verifica quando la risposta della ricerca ha un'origine diversa dalla pagina che l'ha richiesta. In un ambiente di produzione è necessario soddisfare questo criterio tramite l'hosting di uno script sul lato server che esegue la chiamata API nello stesso dominio della pagina Web. Poiché lo script ha la stessa origine della pagina Web, l'intestazione `X-MSEdge-ClientID` diventa disponibile per JavaScript.

> [!NOTE]
> In un'applicazione Web di produzione è consigliabile comunque eseguire la richiesta lato server. In caso contrario, la chiave di sottoscrizione dell'API di ricerca Bing deve essere inclusa nella pagina Web, dove è disponibile per chiunque visualizzi l'origine. All'utente vengono addebitati costi per tutto l'utilizzo della chiave di sottoscrizione API, anche le richieste effettuate da parti non autorizzate, pertanto è importante che la chiave non sia visibile.

Ai fini dello sviluppo, è possibile effettuare una richiesta tramite un proxy CORS. La risposta da questo tipo di proxy ha un'intestazione `Access-Control-Expose-Headers` che inserisce le intestazioni di risposta in un elenco elementi consentiti e le rende disponibili a JavaScript.

È facile installare un proxy CORS per consentire all'app di esempio di accedere all'intestazione ID client. Eseguire questo comando:

```console
npm install -g cors-proxy-server
```

Poi modificare l'endpoint di Ricerca Web Bing nel file `script.js` in:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Avviare il proxy CORS con questo comando:

```console
cors-proxy-server
```

Lasciare aperta la finestra di comando mentre si usa l'app di esempio. La chiusura della finestra determina infatti l'arresto del proxy. Nella sezione espandibile Intestazioni HTTP sotto i risultati della ricerca dovrebbe essere visibile l'intestazione `X-MSEdge-ClientID`. Verificare che sia la stessa per ogni richiesta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Ricerca Web Bing v7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
