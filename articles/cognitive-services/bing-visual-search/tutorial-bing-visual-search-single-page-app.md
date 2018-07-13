---
title: Ricerca immagini Bing in un'app Web a pagina singola | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Illustra come usare l'API Ricerca immagini Bing in un'applicazione Web a pagina singola.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376412"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Esercitazione: Ricerca visiva in un'app Web a pagina singola

L'API Ricerca visiva Bing restituisce un'esperienza simile ai dettagli delle immagini visualizzati in Bing.com/images. Con Ricerca visiva è possibile specificare un'immagine e ottenere informazioni correlate dettagliate, ad esempio immagini visivamente simili, venditori degli oggetti raffigurati nelle immagini, pagine Web che includono l'immagine e altro ancora. 

Questa esercitazione estende l'app Web a pagina singola dell'esercitazione sulla Ricerca immagini Bing (vedere [App Web a pagina singola](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Per il codice sorgente completo di partenza per questa esercitazione, vedere [App Web a pagina singola (codice sorgente)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Per il codice sorgente finale di questa esercitazione, vedere [Ricerca visiva in un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app-source.md).

Sono descritte queste attività:

> [!div class="checklist"]
> * Chiamare l'API Ricerca visiva Bing con un token imageInsightsToken
> * Visualizzare immagini simili

## <a name="call-bing-visual-search"></a>Chiamare Ricerca visiva Bing
Modificare l'esercitazione sulla Ricerca immagini Bing e aggiungere il codice seguente alla fine dell'elemento dello script alla riga 409. Questo codice chiama l'API Ricerca visiva Bing e visualizza i risultati.

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}

function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Acquisire il token imageInsightsToken
Aggiungere il codice seguente nell'oggetto `searchItemsRenderer` alla riga 151. Questo codice aggiunge un collegamento **find similar** che chiama la funzione `bingVisualSearch` quando si fa clic. La funzione riceve il token imageInsightsToken come argomento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visualizzare immagini simili
Aggiungere il codice HTML seguente alla riga 601. Questo codice di markup aggiunge un elemento usato per visualizzare i risultati della chiamata all'API Ricerca visiva Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Dopo avere inserito tutto il nuovo codice JavaScript e gli elementi HTML, è possibile usare il collegamento **find similar** per visualizzare i risultati delle ricerche. Fare clic sul collegamento per popolare la sezione **Similar** con immagini simili a quella selezionata. Può essere necessario espandere la sezione **Similar** per mostrare le immagini.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Codice sorgente di Ricerca visiva in un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app-source.md)
> [Riferimenti dell'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)