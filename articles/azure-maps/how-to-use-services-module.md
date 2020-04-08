---
title: Usare il modulo Servizi di Mappe di Azure. Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come usare i servizi REST di Microsoft Azure Maps usando il modulo dei servizi di Azure Maps.In this article, you'll learn how to use the Microsoft Azure Maps REST services using the Azure Maps services module.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 6e9d1f35d021c3381f9c2887dfb1c150bb720871
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804657"
---
# <a name="use-the-azure-maps-services-module"></a>Usare il modulo dei servizi di Mappe di AzureUse the Azure Maps services module

Azure Maps Web SDK fornisce un modulo di *servizi*. Questo modulo è una libreria helper che semplifica l'uso dei servizi REST di Azure Maps nelle applicazioni Web o Node.js usando JavaScript o TypeScript.This module is a helper library that makes it easy to use the Azure Maps REST services in web or Node.js applications by using JavaScript or TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Utilizzare il modulo servizi in una pagina Web

1. Creare un nuovo file HTML.
1. Caricare il modulo dei servizi di Mappe di Azure.Load the Azure Maps services module. È possibile caricarlo in uno dei due modi seguenti:
    - Usare la versione della rete per la distribuzione di contenuti di Azure ospitata a livello globale del modulo dei servizi di Azure Maps.Use the globally hosted, Azure Content Delivery Network version of the Azure Maps services module. Aggiungere un riferimento `<head>` di script all'elemento del file:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - In alternativa, caricare il modulo di servizi per il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto [azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm e quindi ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript. Usare questo comando:
    
        > **npm install azure-maps-rest**
    
        Quindi, aggiungere un riferimento `<head>` di script all'elemento del file:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Creare una pipeline di autenticazione. La pipeline deve essere creata prima di poter inizializzare un endpoint client URL del servizio. Usare la chiave dell'account di Azure Maps o le credenziali di Azure Active Directory (Azure AD) per autenticare un client del servizio di ricerca di Azure Maps.Use your own Azure Maps account key or Azure Active Directory (Azure AD) credentials to authenticate an Azure Maps Search service client. In questo esempio verrà creato il client URL del servizio di ricerca. 

    Se si usa una chiave di sottoscrizione per l'autenticazione:If you use a subscription key for authentication:

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

    Se si usa Azure AD per l'autenticazione:If you use Azure AD for authentication:

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

    Per altre informazioni, vedere [Autenticazione con Azure Maps.For](azure-maps-authentication.md)more information, see Authentication with Azure Maps.

1. Il codice seguente usa il client URL del servizio Ricerca mappe di Azure appena creato per geocodificare un indirizzo: "1 Microsoft Way, Redmond, WA". Il codice `searchAddress` utilizza la funzione e visualizza i risultati come tabella nel corpo della pagina.

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

    Ecco l'esempio di codice completo e in esecuzione:Here's the full, running code sample:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Utilizzo del modulo Servizi" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna utilizzando il modulo<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>servizi</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Supporto cloud di Azure per enti pubbliciAzure Government cloud support

Il Web SDK di Azure Maps supporta il cloud di Azure per enti pubblici. Tutti gli URL JavaScript e CSS usati per accedere a Azure Maps Web SDK rimangono invariati, tuttavia sarà necessario eseguire le attività seguenti per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Azure Maps.

Quando si utilizza il controllo mappa interattivo, aggiungere la `Map` seguente riga di codice prima di creare un'istanza della classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Assicurarsi di usare i dettagli di autenticazione di Azure Maps dalla piattaforma cloud di Azure per enti pubblici durante l'autenticazione della mappa e dei servizi.

Quando si usa il modulo services, è necessario impostare il dominio per i servizi quando si crea un'istanza di un endpoint URL API. Ad esempio, il codice seguente `SearchURL` crea un'istanza della classe e punta il dominio al cloud di Azure per enti pubblici.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se si accede direttamente ai servizi REST `atlas.azure.us`di Azure Maps, modificare il dominio dell'URL in . Ad esempio, se si utilizza il servizio `https://atlas.microsoft.com/search/` API `https://atlas.azure.us/search/`di ricerca, modificare il dominio URL da in .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [MappeURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [URL di ricerca](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [URL percorso](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredentialSubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Per altri esempi di codice che usano il modulo dei servizi, vedere gli articoli seguenti:For more code samples that use the services module, see these articles:

> [!div class="nextstepaction"]
> [Visualizzare i risultati della ricerca sulla mappa](./map-search-location.md)

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)
