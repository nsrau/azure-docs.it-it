---
title: Esercitazione sul caricamento di immagini in Ricerca visiva Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Suddivide il processo di caricamento di un'immagine in Bing per ottenere informazioni dettagliate sull'immagine stessa nonché il processo di analisi e di visualizzazione della risposta.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 07/10/2018
ms.author: scottwhi
ms.openlocfilehash: 1352ccbcda35c693c5ac0b36156af199ae46bee9
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068669"
---
# <a name="tutorial-breaking-down-bing-visual-search-upload"></a>Esercitazione: Suddivisione del processo di caricamento in Ricerca visiva Bing

Questa esercitazione suddivide il processo di caricamento di un'immagine in Bing per ottenere informazioni dettagliate. L'esercitazione illustra anche come accedere e visualizzare le informazioni dettagliate nella risposta JSON. Per gli esempi HTML e JavaScript completi, vedere [Codice completo](#complete-code).

Questa esercitazione è destinata agli sviluppatori che vogliono esaminare il contenuto della risposta di Ricerca visiva Bing. Non si applica a tutti i requisiti per l'uso e la visualizzazione (ad esempio non contiene alcun collegamento all'Informativa sulla privacy di Microsoft). Per tutti i requisiti per l'uso e la visualizzazione, vedere [Requisiti per l'uso e la visualizzazione delle API di ricerca Bing](./use-and-display-requirements.md).


## <a name="where-to-start"></a>Dove iniziare?

Iniziamo con una pagina HTML che invia un'immagine di Bing e che consente di ottenere informazioni dettagliate e di visualizzarle. Nell'editor preferito creare un file denominato uploaddemo.html. Aggiungere al file la struttura HTML di base seguente.

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

Per iniziare, è possibile dividere la pagina in una sezione di richiesta, in cui l'utente specifica tutte le informazioni necessarie per eseguire la richiesta, e una sezione di risposta in cui vengono visualizzate le informazioni dettagliate. Aggiungere i tag \<div\> seguenti alla sezione \<body\>. Il tag \<hr\> divide visivamente la sezione di richiesta da quella di risposta.

```html
        <div id="requestSection"></div>

        <hr />
        
        <div id="responseSection"></div>
```

## <a name="get-the-file-to-upload"></a>Ottenere il file da caricare

Per consentire all'utente di selezionare l'immagine da caricare, la demo usa il tag \<input\> tag con l'attributo type impostato su file. Nell'interfaccia utente deve essere chiaro che la demo usa Bing per ottenere i risultati della ricerca. 

Aggiungere il codice \<div\> seguente al codice div della sezione requestSection. L'input accetta un unico file per ogni tipo di immagine, ad esempio file con estensione jpg, gif, png. L'evento `onchange` specifica il gestore chiamato quando un utente seleziona un file.

Il tag \<output\> tag consente di visualizzare un'anteprima dell'immagine selezionata.


```html
            <div>
                <p>Select image to get insights from Bing:
                    <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
                </p>

                <output id="thumbnail"></output>
            </div>
```

Prima di aggiungere il gestore, aggiungere un tag \<script\> tag al tag \<head\>.

```html
        <script>
        <\script>
```

Di seguito viene illustrato il gestore che acquisisce l'immagine selezionata. Il gestore include funzioni di logica per verificare che il file selezionato sia un file di immagine e che la relativa dimensione sia pari a 1 MB o inferiore. È possibile consentire all'utente di selezionare file di dimensioni maggiori, ma è necessario ridurre le dimensioni dell'immagine a meno di 1 MB prima di caricarla in Bing. L'ultima operazione che il gestore esegue è quella di visualizzare un'anteprima dell'immagine, in modo che l'utente disponga di un promemoria visivo del file selezionato.

```javascript
        function handleFileSelect(selector) {
            var files = document.getElementById(selector).files; // FileList object

            // The list will contain only one file.
            for (var i = 0, f; f = files[i]; i++) {
      
                // Only process image files.
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


## <a name="what-else-is-needed-before-making-the-call-to-bing"></a>Quali altri elementi sono necessari prima di effettuare la chiamata a Bing?

La demo ha bisogno anche di una chiave di sottoscrizione. Tale chiave viene ottenuta in genere da un archivio sicuro, ma per la semplicità di questa dimostrazione, è necessario specificarla nell'interfaccia utente. Aggiungere il tag \<input\> seguente (con l'attributo type impostato su text) alla sezione \<body\> sotto il tag \<output\> del file.

```html
        <div>
            <p>Subscription key: 
                <input type="text" id="key" name="subscription" size=40 maxlength="32" />
            </p>
        </div>
```

Con l'immagine e la chiave di sottoscrizione a disposizione, è possibile effettuare la chiamata a Ricerca visiva Bing per ottenere informazioni approfondite sull'immagine. La chiamata usa i valori di mercato e di ricerca sicura predefiniti (en-us e moderate, rispettivamente).

Questa demo consente all'utente di modificare tali valori. Aggiungere il codice seguente \<div\> sotto l'elemento div della chiave di sottoscrizione. La demo una un tag \<select\> tag per specificare un elenco a discesa per valori di mercato e di ricerca sicura. Entrambi gli elenchi visualizzano il valore predefinito di Bing.

 
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

La demo nasconde gli elenchi presenti in un elemento div comprimibile controllato dal collegamento Query options. Quando si fa clic sul collegamento Query options, la sezione div si espande in modo che sia possibile visualizzare e modificare le opzioni di query. Se si fa clic sul collegamento Query options di nuovo, la sezione div viene compressa e nascosta. Di seguito viene illustrato il gestore dell'evento onclick del collegamento Query options. Il gestore controlla se la sezione div è espansa o compressa. Aggiungere il gestore alla sezione \<script\>. Il gestore viene usato da tutte le sezioni div comprimibili nella demo.

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


## <a name="making-the-call"></a>Effettuare la chiamata

Aggiungere il pulsante Get insights seguente sotto la sezione options div nel corpo. Il pulsante consente all'utente di avviare la chiamata. Quando l'utente fa clic sul pulsante, il cursore viene modificato nel pulsante rotante di attesa e viene chiamato il gestore dell'evento onclick.

```html
        <p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Aggiungere il gestore dell'evento onclick del pulsante al tag \<script\>. Il gestore assicura che la chiave di sottoscrizione sia presente e che sia composta da 32 caratteri e che sia stata selezionata un'immagine e cancella anche eventuali informazioni dettagliate risultanti da una query precedente. Se la verifica restituisce un risultato corretto, il gestore chiama la funzione sendRequest per effettuare la chiamata.

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

La funzione sendRequest formatta l'URL dell'endpoint, imposta l'intestazione Ocp-Apim-Subscription-Key sulla chiave di sottoscrizione, aggiunge il file binario dell'immagine da caricare, specifica il gestore della risposta ed effettua la chiamata. 

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

## <a name="handling-the-response"></a>Gestione della risposta

La funzione handleResponse gestisce la risposta alla chiamata a Ricerca visiva Bing. Se la chiamata ha esito positivo, viene analizzata la risposta JSON in singoli tag, che contengono le informazioni dettagliate. In seguito la funzione aggiunge la stringa, ovvero i risultati della ricerca di Bing in Internet, alla pagina per comunicare all'utente che i dati provengono da Bing.

La demo potrebbe inserire tutte le informazioni dettagliate nella pagina, ma alcune immagini di restituiscono una grande quantità di dati, che possono renderne difficile l'uso. La demo crea quindi una sezione div comprimibile per ogni tag, in modo che l'utente possa gestire la quantità di dati da visualizzare.

Aggiungere il gestore alla sezione \<script\>.

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

La funzione buildTagSections esegue l'iterazione dei tag JSON analizzati e chiama la funzione buildDiv per creare una sezione div per ogni tag. In modo analogo all'elemento Query option, ogni tag viene visualizzato come collegamento. Quando l'utente fa clic sul collegamento, il tag si espande per mostrare le informazioni dettagliate associate al tag stesso. Se l'utente fa clic di nuovo sul collegamento, la sezione si comprime e nasconde le informazioni dettagliate all'utente.

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

La funzione buildDiv chiama la funzione addDivContent per compilare il contenuto di ogni sezione div. comprimibile del tag

Il contenuto del tag include il codice JSON della risposta per il tag. La demo include il codice JSON per gli sviluppatori che vogliono visualizzare il codice JSON alla base della risposta. Inizialmente vengono visualizzati solo i primi 100 caratteri del file JSON, ma è possibile scegliere la stringa JSON per mostrare tutto il file JSON. Se si fa clic di nuoco, la stringa JSON viene compressa nuovamente a 100 caratteri.

Successivamente, aggiungere i tipi di azione trovati nel tag. Per ogni tipo di azione, chiamare le varie funzioni per aggiungerne le informazioni dettagliate.

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

Di seguito vengono indicate tutte le funzioni che consentono di visualizzare informazioni dettagliate per le diverse azioni. Per la maggior parte, queste funzioni sono molto semplici e restituiscono un'immagine o un collegamento selezionabile che indirizza l'utente a una pagina Web in cui è possibile ottenere altre informazioni sull'immagine (sito Bing.com o pagina Web che ospita l'immagine). L'esercitazione non visualizza tutti i dati associati alle informazioni dettagliate. Per visualizzare tutti i campi disponibili per le informazioni dettagliate, vedere [Bing Visual Search Reference](https://aka.ms/bingvisualsearchreferencedoc) (Riferimento alla Ricerca visiva Bing).

Tenere presente che è necessario visualizzare solo una quantità minima di dati, il resto è a discrezione dell'utente. Per verificare di rispettare la conformità, vedere [Requisiti per l'uso e la visualizzazione delle API di ricerca Bing](./use-and-display-requirements.md).


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


        // If Bing recognized any text in the image, display the text.
        function addTextResult(div, action) {
            var text = document.createElement('p');
            text.textContent = action.displayName;
            div.appendChild(text);
        }


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


        // Adds a clickable image to the div that takes the user to
        // Bing.com search results.
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



## <a name="adding-styles-to-make-the-page-display-correctly"></a>Aggiunta di stili per visualizzare la pagina in modo corretto

Aggiungere la sezione \<style\> seguente al tag \<head\>.

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



## <a name="complete-code"></a>Codice completo

Di seguito viene indicato l'esempio di codice HTML e JavaScript completo.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Bing Visual Search Demo</title>
        
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

        <script>

            // Handles the user's file selection. The file input button specifies this handler.
            function handleFileSelect(selector) {
                var files = document.getElementById(selector).files; // FileList object

                // The list will contain only one file.
                for (var i = 0, f; f = files[i]; i++) {
        
                    // Only process image files.
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


            // Called when the user clicks the Query insights button.
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


            // Format the request and send it.
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


            // Handles the response from Bing. Parses the response and 
            // the tag divs.
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


            // Parses the json response by tags.
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


            // Builds divs fro each tag in the response.
            function buildTagSections(tags) {
                for (var tag in tags) {
                    if (tags.hasOwnProperty(tag)) {
                        var tagSection = buildDiv(tags, tag);
                        document.getElementById('responseSection').appendChild(tagSection);
                    }
                }  
            }


            // Builds the div for the specified tag. The div is shown as a
            // link that when clicked, expands or collapses. The divs are 
            // initially collapsed.
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


            // Adds the tag's action types to the div.
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


            // Display the first 10 related images.
            // TODO: Add 'more' link in case the user wants to see all of them.
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


            // If Bing recognized any text in the image, display the text.
            function addTextResult(div, action) {
                var text = document.createElement('p');
                text.textContent = action.displayName;
                div.appendChild(text);
            }


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


            // Adds a clickable image to the div that takes the user to
            // Bing.com search results.
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

        </script>

    </head>

    <body>

        <div id="requestSection">
            <div>
                <p>Select image to get insights from Bing:
                    <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
                </p>

                <output id="thumbnail"></output>
            </div>

            <div>
                <p>Subscription key: 
                    <input type="text" id="key" name="subscription" size=40 maxlength="32" />
                </p>
            </div>

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
        </div>
        
        <p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>

        <hr />
        
        <div id="responseSection"></div>
    </body>
</html>      
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come ottenere informazioni dettagliate tramite token di informazioni dettagliate, vedere [Esercitazione: SDK di Ricerca visiva Bing - ImageInsightsToken e risultati](.\tutorial-visual-search-insights-token.md).