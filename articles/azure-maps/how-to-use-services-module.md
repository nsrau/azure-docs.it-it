---
title: Usando il modulo Servizi - mappe di Azure | Microsoft Docs
description: Informazioni su come usare il modulo di servizi di mappe di Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e89a4675f867e53c499bb82b239ddb9bec1aed6f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521200"
---
# <a name="using-the-azure-maps-services-module"></a>Uso del modulo di servizi di mappe di Azure

Mappe di Azure SDK per Web fornisce un modulo di servizi che è una libreria helper che rende più semplice usare i servizi REST di mappe di Azure in applicazioni web o Node. js usando JavaScript o TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>Uso del modulo di servizi in una pagina web

1. Creare un nuovo file HTML.
2. Caricare il modulo di servizi di mappe di Azure. A tale scopo, è possibile procedere nei due modi descritti di seguito:

    a. Usare la versione della rete CDN a livello globale ospitata di modulo di servizi di mappe di Azure aggiungendo un riferimento a script per il `<head>` elemento del file:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
    ```
    
    b. In alternativa, caricare il codice sorgente di Azure SDK per Web viene eseguito il mapping in locale usando il [azure-rest-mappe](https://www.npmjs.com/package/azure-maps-rest) NPM del pacchetto, quindi ospitalo con l'app. Questo pacchetto include anche le definizioni TypeScript.
    
    > npm install azure-mapping-rest
    
    Quindi aggiungere un riferimento allo script il `<head>` elemento del file:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Per inizializzare un endpoint client del servizio URL, è innanzitutto necessario creare una pipeline di autenticazione. Usare la propria chiave dell'account mappe di Azure o le credenziali di Azure Active Directory (AAD) per autenticare il client del servizio di ricerca. In questo esempio verrà creato il client di URL del servizio di ricerca. Se si usa una chiave di sottoscrizione per l'autenticazione:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Se si usa Azure Active Directory (AAD) per l'autenticazione:

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
        try {
            console.log("Renewing token");
            var token = await getAadToken();
            tokenCredential.token = token;
            tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
        } catch (error) {
            console.log("Caught error when renewing token");
            clearTimeout(tokenRenewalTimer);
            throw error;
        }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Per altre informazioni, vedere [l'autenticazione con le mappe di Azure](azure-maps-authentication.md).

4. Il codice seguente usa il client di URL del servizio di ricerca appena creato per la geocodifica un indirizzo, "1 Microsoft. Way, Redmond, WA" utilizzando il `searchAddress` funzione e visualizzare i risultati sotto forma di tabella nel corpo della pagina. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');
      
      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
                    response.results[i].address.freeformAddress, 
                    '</td><td>', 
                    response.results[i].position.lat,
                    '</td><td>', 
                    response.results[i].position.lon,
                    '</td></tr>');
      }
      
      html.push('</table>');
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Ecco il codice di esempio completo in esecuzione:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Uso del modulo di servizi" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>usando il modulo Servizi</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Vedere gli articoli seguenti per altri esempi di codice che usano il modulo Servizi:

> [!div class="nextstepaction"]
> [Visualizzare i risultati della ricerca sulla mappa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)