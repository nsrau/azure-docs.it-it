---
title: Usare il modulo servizi di mappe di Azure | Mappe Microsoft Azure
description: Informazioni sul modulo servizi di Azure maps. Vedere come caricare e usare questa libreria helper per accedere ai servizi REST di Azure Maps in applicazioni Web o Node.js.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895427"
---
# <a name="use-the-azure-maps-services-module"></a>Usare il modulo servizi di mappe di Azure

Azure Maps Web SDK fornisce un *modulo dei servizi* . Questo modulo è una libreria helper che semplifica l'uso dei servizi REST di Maps di Azure in applicazioni Web o Node.js usando JavaScript o TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Usare il modulo servizi in una pagina Web

1. Creare un nuovo file HTML.
1. Caricare il modulo servizi di mappe di Azure. È possibile caricarlo in uno dei due modi seguenti:
    - Usare la versione di rete per la distribuzione di contenuti di Azure ospitata a livello globale del modulo servizi di Azure maps. Aggiungere un riferimento allo script all' `<head>` elemento del file:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - In alternativa, caricare localmente il modulo Servizi per il codice sorgente di Azure Maps Web SDK usando il pacchetto NPM [Azure-Maps-REST](https://www.npmjs.com/package/azure-maps-rest) e quindi ospitarlo nell'app. Questo pacchetto include anche le definizioni TypeScript. Usare questo comando:
    
        > **npm install azure-maps-rest**
    
        Aggiungere quindi un riferimento allo script all' `<head>` elemento del file:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Creare una pipeline di autenticazione. Prima di poter inizializzare un endpoint client dell'URL del servizio, è necessario creare la pipeline. Usare la chiave dell'account di Azure Maps personalizzata o le credenziali Azure Active Directory (Azure AD) per autenticare un client del servizio di ricerca di Azure maps. In questo esempio verrà creato il client dell'URL del servizio di ricerca. 

    Se si usa una chiave di sottoscrizione per l'autenticazione:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Se si usa Azure AD per l'autenticazione:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
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

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
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
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Per altre informazioni, vedere [autenticazione con mappe di Azure](azure-maps-authentication.md).

1. Il codice seguente usa il client dell'URL del servizio di ricerca di Azure Maps appena creato per la geocodifica di un indirizzo: "1 Microsoft Way, Redmond, WA". Il codice usa la `searchAddress` funzione e Visualizza i risultati come tabella nel corpo della pagina.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
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

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Ecco l'esempio di codice completo in esecuzione:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Uso del modulo Services" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>usando il modulo Servizi</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Supporto cloud di Azure per enti pubblici

Azure Maps Web SDK supporta il cloud di Azure per enti pubblici. Tutti gli URL JavaScript e CSS usati per accedere ad Azure Maps Web SDK rimangono invariati, ma è necessario eseguire le attività seguenti per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Azure maps.

Quando si usa il controllo mappa interattiva, aggiungere la riga di codice seguente prima di creare un'istanza della `Map` classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Assicurarsi di usare i dettagli di autenticazione di Azure Maps della piattaforma cloud di Azure per enti pubblici durante l'autenticazione della mappa e dei servizi.

Quando si usa il modulo Services, il dominio per i servizi deve essere impostato quando si crea un'istanza di un endpoint dell'URL dell'API. Ad esempio, il codice seguente crea un'istanza della `SearchURL` classe e punta il dominio al cloud di Azure per enti pubblici.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se si accede direttamente ai servizi REST di Azure Maps, modificare il dominio dell'URL in `atlas.azure.us` . Ad esempio, se si usa il servizio API di ricerca, modificare il dominio URL da `https://atlas.microsoft.com/search/` a `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Per altri esempi di codice che usano il modulo Services, vedere questi articoli:

> [!div class="nextstepaction"]
> [Visualizzare i risultati della ricerca sulla mappa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)