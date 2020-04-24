---
title: Introduzione al controllo della mappa Web | Mappe Microsoft Azure
description: Informazioni su come usare la libreria JavaScript lato client del controllo mappa di Microsoft Azure Maps per eseguire il rendering delle mappe e della funzionalità embedded di Azure Maps nell'applicazione Web o per dispositivi mobili.
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
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di mappe di Azure

La controllo mappa libreria JavaScript lato client consente di eseguire il rendering delle mappe e della funzionalità embedded di Azure Maps nell'applicazione Web o per dispositivi mobili.

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client controllo mappa.

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK. È possibile scegliere una delle due opzioni seguenti:

    * Usare la versione CDN ospitata a livello globale di Azure Maps Web SDK aggiungendo riferimenti a JavaScript e al foglio di stile `<head>` nell'elemento del file HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Caricare il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto NPM di [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) e ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript.

        > **npm install azure-maps-control**

       Aggiungere quindi i riferimenti alle origini del foglio di stile e dello script di Mappe di Azure all'elemento `<head>` del file:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > È possibile importare le definizioni typescript nell'applicazione aggiungendo il codice seguente:
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

5. Per inizializzare il controllo mappa, definire un nuovo tag script nel corpo HTML. Passare `id` l'oggetto della `<div>` mappa o un `HTMLElement` (ad esempio, `document.getElementById('myMap')`) come primo parametro durante la creazione di un'istanza della `Map` classe. Usare la propria chiave dell'account di Mappe di Azure oppure le credenziali di Azure Active Directory (AAD) per autenticare la mappa usando le [opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Se è necessario creare un account o trovare la chiave, seguire le istruzioni in [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   L'opzione **language** specifica la lingua da usare per le etichette e i controlli mappa. Per ulteriori informazioni sulle lingue supportate, vedere [linguaggi supportati](supported-languages.md). Se si utilizza una chiave di sottoscrizione per l'autenticazione, utilizzare quanto segue:

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

   Se si usa Azure Active Directory (AAD) per l'autenticazione, usare quanto segue:

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

   Un elenco di esempi che illustrano come integrare Azure Active Directory (AAD) con mappe di Azure è disponibile [qui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Per altre informazioni, vedere il documento relativo all' [autenticazione con mappe di Azure](azure-maps-authentication.md) ed [esempi di autenticazione di Azure Maps Azure ad](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Facoltativamente, può risultare utile aggiungere gli elementi di tag meta seguenti all'inizio della pagina:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. L'inserimento di tutti i file HTML dovrebbe avere un aspetto simile al codice seguente:

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

8. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering. Dovrebbe essere simile all'immagine seguente:

   ![Immagine mappa che mostra il risultato del rendering](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Localizzare la mappa

Azure Maps offre due modi diversi per impostare la lingua e la visualizzazione regionale per la mappa di cui è stato eseguito il rendering. La prima opzione consiste nell'aggiungere queste informazioni allo spazio dei `atlas` nomi globale, che comporterà l'impostazione predefinita di tutte le istanze del controllo mappa nell'app. Il codice seguente imposta la lingua in francese ("fr-FR") e la vista regionale su "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

La seconda opzione consiste nel passare queste informazioni nelle opzioni della mappa quando si carica la mappa come segue:

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
> Con il Web SDK è possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per lingua e area geografica. Inoltre, è possibile aggiornare queste impostazioni dopo il caricamento della mappa utilizzando `setStyle` la funzione della mappa. 

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione regionale impostata su "auto".

![Immagine mappa che mostra le etichette in francese](./media/how-to-use-map-control/websdk-localization.png)

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Supporto cloud di Azure per enti pubblici

Azure Maps Web SDK supporta il cloud di Azure per enti pubblici. Tutti gli URL JavaScript e CSS usati per accedere ad Azure Maps Web SDK rimangono invariati. Per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Azure Maps è necessario eseguire le attività seguenti.

Quando si usa il controllo mappa interattiva, aggiungere la riga di codice seguente prima di creare un'istanza `Map` della classe. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Assicurarsi di usare i dettagli di autenticazione di Azure Maps dalla piattaforma cloud di Azure per enti pubblici durante l'autenticazione della mappa e dei servizi.

Quando si usa il modulo Services, il dominio per i servizi deve essere impostato quando si crea un'istanza di un endpoint dell'URL dell'API. Ad esempio, il codice seguente crea un'istanza della `SearchURL` classe e punta il dominio al cloud di Azure per enti pubblici.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se si accede direttamente ai servizi REST di Azure Maps, modificare il dominio dell' `atlas.azure.us`URL in. Ad esempio, se si usa il servizio API di ricerca, modificare il dominio `https://atlas.microsoft.com/search/` URL `https://atlas.azure.us/search/`da a.

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

Per un elenco di esempi che illustrano come integrare Azure Active Directory (AAD) con mappe di Azure, vedere:

> [!div class="nextstepaction"]
> [Esempi di autenticazione Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
