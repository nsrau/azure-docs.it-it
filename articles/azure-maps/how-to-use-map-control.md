---
title: Guida introduttiva al controllo della mappa Web Mappe di Microsoft Azure
description: Informazioni su come usare la libreria JavaScript del controllo delle mappe di Microsoft Azure Maps per eseguire il rendering delle mappe e delle funzionalità di Mappe di Azure incorporate nell'applicazione Web o per dispositivi mobili.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335239"
---
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di Mappe di AzureUse the Azure Maps map control

La libreria JavaScript lato client di Controllo mappa consente di eseguire il rendering delle mappe e della funzionalità incorporata di Mappe di Azure nell'applicazione Web o per dispositivi mobili.

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web utilizzando la libreria JavaScript lato client di controllo mappa.

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK. È possibile scegliere una delle due opzioni;

    * Usare la versione CDN ospitata a livello globale di Azure Maps Web SDK `<head>` aggiungendo riferimenti a JavaScript e al foglio di stile nell'elemento del file HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Caricare il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto NPM di [controllo delle mappe di Azure](https://www.npmjs.com/package/azure-maps-control) e ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript.

        > **npm install azure-maps-control**

       Aggiungere quindi i riferimenti alle origini del foglio di stile e dello script di Mappe di Azure all'elemento `<head>` del file:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Le definizioni typescript possono essere importate nell'applicazione aggiungendo il codice seguente:Typescript definitions can be imported into your application by adding the following code:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Per eseguire il rendering della mappa in modo che riempia l'intero corpo della pagina, aggiungere l'elemento `<style>` seguente all'elemento `<head>`.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. Nel corpo della pagina aggiungere un elemento `<div>` e assegnargli un `id`**myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Per inizializzare il controllo mappa, definire un nuovo tag di script nel corpo html. Passare la `id` mappa `<div>` o `HTMLElement` un (ad `document.getElementById('myMap')`esempio, ) come primo parametro durante la creazione di un'istanza della `Map` classe. Usare la propria chiave dell'account di Mappe di Azure oppure le credenziali di Azure Active Directory (AAD) per autenticare la mappa usando le [opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Se è necessario creare un account o trovare la chiave, seguire le istruzioni in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e ottenere la chiave [primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   L'opzione **language** specifica la lingua da usare per le etichette e i controlli mappa. Per ulteriori informazioni sulle lingue supportate, vedere [lingue supportate.](supported-languages.md) Se si usa una chiave di sottoscrizione per l'autenticazione, usare quanto segue:If you are using a subscription key for authentication, use the following:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   Se si usa Azure Active Directory (AAD) per l'autenticazione, usare quanto segue:If you are using Azure Active Directory (AAD) for authentication, use the following:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   Un elenco di esempi che mostra come integrare Azure Active Directory (AAD) con Azure Maps è disponibile [qui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Per altre informazioni, vedere il documento [Autenticazione con Azure Maps](azure-maps-authentication.md) ed esempi di autenticazione di Azure Maps di [Azure Azure Azure AD.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

6. Facoltativamente, può risultare utile aggiungere gli elementi di tag meta seguenti all'inizio della pagina:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Mettere tutto insieme il file HTML dovrebbe essere simile al seguente codice:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering. Dovrebbe apparire come l'immagine qui sotto:

   ![Immagine della mappa che mostra il risultato di rendering](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Localizzare la mappa

Mappe di Azure offre due modi diversi per impostare la lingua e la visualizzazione regionale per la mappa di cui è stato eseguito il rendering. La prima opzione consiste nell'aggiungere `atlas` queste informazioni allo spazio dei nomi globale, che comporterà che tutte le istanze del controllo mappa nell'app vengano fornite per impostazione predefinita per queste impostazioni. Di seguito la lingua viene impostata sul francese ("fr-FR") e la vista regionale su "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

La seconda opzione consiste nel passare queste informazioni alle opzioni della mappa durante il caricamento della mappa in questo modo:The second option is to pass this information into the map options when loading the map like this:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Con Web SDK è possibile caricare più istanze della mappa nella stessa pagina con impostazioni di lingua e area geografica diverse. Inoltre, queste impostazioni possono essere aggiornate dopo `setStyle` il caricamento della mappa utilizzando la funzione della mappa. 

Ecco un esempio di Mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione regionale impostata su "Auto".

![Immagine della mappa con le etichette in francese](./media/how-to-use-map-control/websdk-localization.png)

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Supporto cloud di Azure per enti pubbliciAzure Government cloud support

Il Web SDK di Azure Maps supporta il cloud di Azure per enti pubblici. Tutti gli URL JavaScript e CSS usati per accedere a Azure Maps Web SDK rimangono invariati. Le attività seguenti dovranno essere eseguite per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Mappe di Azure.The following tasks will need to be done to connect to the Azure Government cloud version of the Azure Maps platform.

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

Informazioni su come creare e interagire con una mappa:

> [!div class="nextstepaction"]
> [Creare una mappa](map-create.md)

Informazioni su come applicare uno stile a una mappa:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](choose-map-style.md)

Per aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Creare una mappa](map-create.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Per un elenco di esempi che illustra come integrare Azure Active Directory (AAD) con Azure Maps, vedere:For a list of samples showing how to integrate Azure Active Directory (AAD) with Azure Maps, see:

> [!div class="nextstepaction"]
> [Esempi di autenticazione di Azure ADAzure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
