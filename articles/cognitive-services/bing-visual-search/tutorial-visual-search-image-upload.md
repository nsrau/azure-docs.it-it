---
title: "Esercitazione: Come caricare un'immagine - Ricerca visiva Bing"
titleSuffix: Azure Cognitive Services
description: Suddivide il processo di caricamento di un'immagine in Bing per ottenere informazioni dettagliate sull'immagine stessa nonché il processo di analisi e di visualizzazione della risposta.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa221569cec6df298db4a90560ac7ea0992dd120
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318729"
---
# <a name="tutorial-uploading-images-to-the-bing-visual-search-api"></a>Esercitazione: Caricamento di immagini nell'API Ricerca visiva Bing

L'API Ricerca visiva Bing consente di cercare nel Web immagini simili a quelle caricate. Usare questa esercitazione per creare un'applicazione Web in grado di inviare un'immagine all'API e visualizzare le informazioni dettagliate restituite all'interno della pagina Web. Si noti che questa applicazione non è conforme a tutti i [Requisiti per l'uso e la visualizzazione di Bing](./use-and-display-requirements.md) per l'uso dell'API.

Il codice sorgente completo per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

L'applicazione di esercitazione illustra come:

> [!div class="checklist"]
> * Caricare un'immagine nell'API Ricerca visiva Bing
> * Visualizzare i risultati della ricerca di immagini in un'applicazione Web
> * Esaminare le diverse informazioni dettagliate fornite dall'API

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Creare e strutturare la pagina Web

Creare una pagina HTML che invia un'immagine a Bing e che consente di ottenere informazioni dettagliate e di visualizzarle. Usare l'ambiente di sviluppo integrato o l'editor preferito per creare un file denominato `uploaddemo.html`. Aggiungere al file la struttura HTML di base seguente.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>      
```

Dividere la pagina in una sezione di richiesta, in cui l'utente specifica tutte le informazioni necessarie per eseguire la richiesta, e una sezione di risposta in cui vengono visualizzate le informazioni dettagliate. Aggiungere i tag `<div>` seguenti alla sezione `<body>`. Il tag `<hr>` divide visivamente la sezione di richiesta da quella di risposta.

```html
<div id="requestSection"></div>
<hr />      
<div id="responseSection"></div>
```

Aggiungere un tag `<script>` al tag `<head>` per contenere il codice JavaScript per l'applicazione.

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Ottenere il file da caricare

Per consentire all'utente di selezionare un'immagine da caricare, l'applicazione usa il tag `<input>` con l'attributo del tipo impostato su `file`. Nell'interfaccia utente deve essere chiaro che l'applicazione usa Bing per ottenere i risultati della ricerca. 

Aggiungere il codice `<div>` seguente al codice div della sezione requestSection. L'input accetta un unico file per ogni tipo di immagine, ad esempio file con estensione jpg, gif, png. L'evento `onchange` specifica il gestore chiamato quando un utente seleziona un file.

Il tag `<output>` consente di visualizzare un'anteprima dell'immagine selezionata.


```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Creare un gestore di file 

Creare una funzione del gestore in grado di leggere l'immagine da caricare. Durante l'iterazione tra i file nell'oggetto `FileList`, il gestore deve verificare che il file selezionato sia un file di immagine e che le dimensioni pari a 1 MB o inferiori. Se l'immagine è più grande, è necessario ridurne le dimensioni prima di caricarla. Il gestore mostrerà infine un'anteprima dell'immagine.

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Aggiungere e archiviare una chiave di sottoscrizione

L'applicazione ha bisogno di una chiave di sottoscrizione per effettuare chiamate all'API Ricerca visiva Bing. Per questa esercitazione, verrà specificata nell'interfaccia utente. Aggiungere il tag `<input>` seguente (con l'attributo type impostato su text) alla sezione `<body>` sotto il tag `<output>` del file.

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Con l'immagine e la chiave di sottoscrizione, è possibile effettuare la chiamata a Ricerca visiva Bing per ottenere informazioni dettagliate sull'immagine. In questa esercitazione, la chiamata usa i valori di mercato e di ricerca sicura predefiniti, rispettivamente `en-us` e `moderate`.

Questa applicazione consente di modificare questi valori. Aggiungere il codice `<div>` seguente sotto l'elemento div della chiave di sottoscrizione. L'applicazione usa un tag `<select>` per specificare un elenco a discesa per valori di mercato e di ricerca sicura. Entrambi gli elenchi visualizzano il valore predefinito.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Aggiungere opzioni di ricerca alla pagina Web 

L'applicazione nasconde gli elenchi presenti in un elemento div comprimibile controllato dal collegamento Query options. Quando si fa clic sul collegamento Query options, la sezione div si espande in modo che sia possibile visualizzare e modificare le opzioni di query. Se si fa clic sul collegamento Query options di nuovo, la sezione div viene compressa e nascosta. Di seguito viene illustrato il gestore dell'evento onclick del collegamento Query options. Il gestore controlla se la sezione div è espansa o compressa. Aggiungere il gestore alla sezione `<script>`. Il gestore viene usato da tutte le sezioni div comprimibili nella demo.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Chiamare il gestore dell'evento onclick

Aggiungere il pulsante `"Get insights"` seguente sotto la sezione options div nel corpo. Il pulsante consente di avviare la chiamata. Quando si fa clic sul pulsante, il cursore viene modificato nel pulsante rotante di attesa e viene chiamato il gestore dell'evento onclick.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Aggiungere il gestore dell'evento onclick del pulsante, `handleQuery()`, al tag `<script>`. 

## <a name="handle-the-query"></a>Gestire la query

Il gestore `handleQuery()` assicura che la chiave di sottoscrizione sia presente e che sia composta da 32 caratteri e che sia stata selezionata un'immagine. e cancella anche eventuali informazioni dettagliate risultanti da una query precedente. Successivamente, chiama la funzione `sendRequest()` per eseguire la chiamata.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd 
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Inviare la richiesta di ricerca

La funzione `sendRequest()` formatta l'URL dell'endpoint, imposta l'intestazione Ocp-Apim-Subscription-Key sulla chiave di sottoscrizione, aggiunge il file binario dell'immagine da caricare, specifica il gestore della risposta ed effettua la chiamata. 

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Ottenere e gestire la risposta dell'API

La funzione `handleResponse()` gestisce la risposta alla chiamata a Ricerca visiva Bing. Se la chiamata ha esito positivo, viene analizzata la risposta JSON in singoli tag, che contengono le informazioni dettagliate. Successivamente, aggiunge i risultati della ricerca alla pagina. L'applicazione crea quindi una sezione div comprimibile per ogni tag, in modo che si possa gestire la quantità di dati da visualizzare. Aggiungere il gestore alla sezione `<script>`.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait curor set by query insights button
}
```

### <a name="parse-the-response"></a>Analizzare la risposta

La funzione `parseResponse` converte la risposta JSON in un oggetto dizionario eseguendo un'iterazione su `json.tags`.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];
        
        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Creare una sezione di tag

La funzione `buildTagSections()` esegue l'iterazione sui tag JSON analizzati e chiama la funzione `buildDiv()` per creare una sezione div per ogni tag. Ogni tag viene visualizzato come un collegamento. Quando si fa clic sul collegamento, il tag si espande per mostrare le informazioni dettagliate associate al tag stesso. Facendo nuovamente clic sul collegamento, la sezione viene compressa.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Visualizzare i risultati della ricerca nella pagina Web

La funzione `buildDiv()` chiama la funzione addDivContent per compilare il contenuto di ogni sezione div comprimibile del tag.

Il contenuto del tag include il codice JSON della risposta per il tag. Inizialmente vengono visualizzati solo i primi 100 caratteri del file JSON, ma è possibile scegliere la stringa JSON per mostrare tutto il file JSON. Se si fa clic di nuoco, la stringa JSON viene compressa nuovamente a 100 caratteri.

Successivamente, aggiungere i tipi di azione trovati nel tag. Per ogni tipo di azione, chiamare le funzioni appropriate per aggiungerne le informazioni dettagliate.

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Visualizzare informazioni dettagliate per azioni diverse

Le funzioni seguenti mostrano informazioni dettagliate per azioni differenti. Le funzioni restituiscono un'immagine o un collegamento selezionabile che indirizza a una pagina Web in cui è possibile ottenere altre informazioni sull'immagine. La pagina è ospitata da Bing.com o è il sito Web originale dell'immagine. L'applicazione non visualizza tutti i dati associati alle informazioni dettagliate. Per visualizzare tutti i campi disponibili per le informazioni dettagliate, vedere le [informazioni di riferimento su Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc).

> [!NOTE]
> È necessario visualizzar nella pagina solo una quantità minima di informazioni dettagliate. Per altre informazioni, vedere i [Requisiti per l'uso e la visualizzazione delle API di ricerca Bing.](./use-and-display-requirements.md).

### <a name="relatedimages-insights"></a>Informazioni dettagliate RelatedImages

La funzione `addRelatedImages()` crea un titolo per ognuno dei siti Web che ospitano l'immagine correlata eseguendo l'iterazione dell'elenco delle azioni `RelatedImages` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuno.

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays 
        // when the user hovers over the image. 

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Informazioni dettagliate PagesIncluding

La funzione `addPagesIncluding()` crea un collegamento per ognuno dei siti Web che ospitano l'immagine caricata eseguendo l'iterazione dell'elenco delle azioni `PagesIncluding` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuno.

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>Informazioni dettagliate RelatedSearches

La funzione `addRelatedSearches()` crea un collegamento per il sito Web che ospita l'immagine caricata eseguendo l'iterazione dell'elenco delle azioni `RelatedSearches` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuno.

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Informazioni dettagliate Recipes

La funzione `addRecipes()` crea un collegamento per ognuna delle ricette restituite eseguendo l'iterazione dell'elenco delle azioni `Recipes` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuna.

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available. 
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Informazioni dettagliate Shopping

La funzione `addShopping()` crea un collegamento per ognuno dei risultati di shopping restituiti eseguendo l'iterazione dell'elenco delle azioni `RelatedImages` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuno.

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Informazioni dettagliate Products

La funzione `addProducts()` crea un collegamento per ognuno dei risultati di prodotti restituiti eseguendo l'iterazione dell'elenco delle azioni `Products` e accodando un tag `<img>` all'elemento `<div>` esterno per ognuno.

```javascript

    // Display the first 10 related products. Display a clickable image of the 
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>Informazioni dettagliate TextResult

La funzione `addTextResult()` visualizzerà qualsiasi testo riconosciuto nell'immagine.

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

La funzione `addEntity()` visualizzerà un collegamento che indirizza l'utente a Bing.com, dove può ottenere i dettagli sul tipo di entità nell'immagine, se è stato rilevato.

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

La funzione `addImageWithWebSearchUrl()` visualizzerà un'immagine selezionabile nell'elemento div che indirizza l'utente ai risultati della ricerca su Bing.com. 

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Aggiungere uno stile CSS

Aggiungere la sezione `<style>` seguente al tag `<head>` per organizzare il layout della pagina Web.

```html
        <style>
            
            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Trovare immagini simili dalle ricerche precedenti con ImageInsightsToken](./tutorial-visual-search-insights-token.md).
