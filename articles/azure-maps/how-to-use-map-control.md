---
title: Introduzione al controllo mappa Web di Microsoft Azure Maps
description: Informazioni su come aggiungere mappe alle applicazioni Web e per dispositivi mobili usando la libreria JavaScript lato client controllo mappa in mappe di Azure. Vedere come localizzare le mappe.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 19db4fb0d75369a0f272eef1180e86f47d45d284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335331"
---
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di Mappe di Azure

La controllo mappa libreria JavaScript lato client consente di eseguire il rendering delle mappe e della funzionalità embedded di Azure Maps nell'applicazione Web o per dispositivi mobili.

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare il controllo mappa in una pagina Web, è necessario disporre di uno dei seguenti prerequisiti:

* [Creare un account Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) e [ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione.

* Ottenere le credenziali di Azure Active Directory (AAD) con le [Opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client controllo mappa.

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK. È possibile scegliere una delle due opzioni:

    * Usare la versione CDN ospitata a livello globale di Azure Maps Web SDK aggiungendo riferimenti a JavaScript e al foglio di stile nell' `<head>` elemento del file HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Caricare il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto NPM di [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) e ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript.

      > **npm install azure-maps-control**

    Aggiungere quindi i riferimenti al foglio di stile Maps di Azure all' `<head>` elemento del file:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > È possibile importare le definizioni typescript nell'applicazione aggiungendo il codice seguente:
    >
    > ```javascript
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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. A questo punto, verrà inizializzato il controllo mappa. Per autenticare il controllo, è necessario essere proprietari di una chiave di sottoscrizione di Azure Maps o usare le credenziali Azure Active Directory (AAD) con le [Opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Se si usa una chiave di sottoscrizione per l'autenticazione, copiare e incollare il seguente elemento script all'interno dell' `<head>` elemento e sotto il primo `<script>` elemento. Sostituire `<Your Azure Maps Key>` con la chiave di sottoscrizione primaria di Azure maps.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    Se si usa Azure Active Directory (AAD) per l'autenticazione, copiare e incollare il seguente elemento script all'interno dell' `<head>` elemento e sotto il primo `<script>` elemento.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
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
            }
        });
    </script>
   ```

    Per ulteriori informazioni sull'autenticazione con mappe di Azure, vedere il documento relativo all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . Inoltre, un elenco di esempi che illustrano come integrare Azure Active Directory (AAD) con mappe di Azure è disponibile [qui](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >In questo esempio è stato passato l'oggetto `id` della mappa `<div>` . Un altro modo per eseguire questa operazione consiste nel passare l' `HTMLElement` oggetto passando `document.getElementById('myMap')` come primo parametro.

6. Facoltativamente, può risultare utile aggiungere gli `meta` elementi seguenti all' `head` elemento della pagina:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Riunendola, il file HTML dovrebbe avere un aspetto simile al markup seguente:

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


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering. Dovrebbe essere simile all'immagine seguente:

   ![Immagine mappa che mostra il risultato del rendering](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Localizzare la mappa

Azure Maps offre due modi diversi per impostare la lingua e la visualizzazione regionale per la mappa di cui è stato eseguito il rendering. La prima opzione consiste nell'aggiungere queste informazioni allo `atlas` spazio dei nomi globale, che comporterà l'impostazione predefinita di tutte le istanze del controllo mappa nell'app. Il codice seguente imposta la lingua in francese ("fr-FR") e la vista regionale su "auto":

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

> [!NOTE]
> È possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per la lingua e l'area. Inoltre, è possibile aggiornare queste impostazioni dopo il caricamento della mappa utilizzando la `setStyle` funzione della mappa.

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione regionale impostata su "auto".

![Immagine mappa che mostra le etichette in francese](./media/how-to-use-map-control/websdk-localization.png)

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Supporto cloud di Azure per enti pubblici

Azure Maps Web SDK supporta il cloud di Azure per enti pubblici. Tutti gli URL JavaScript e CSS usati per accedere ad Azure Maps Web SDK rimangono invariati. Per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Azure Maps è necessario eseguire le attività seguenti.

Quando si usa il controllo mappa interattiva, aggiungere la riga di codice seguente prima di creare un'istanza della `Map` classe.

```javascript
atlas.setDomain('atlas.azure.us');
```

Assicurarsi di usare i dettagli di autenticazione di Azure Maps dalla piattaforma cloud di Azure per enti pubblici durante l'autenticazione della mappa e dei servizi.

Quando si usa il modulo Services, il dominio per i servizi deve essere impostato quando si crea un'istanza di un endpoint dell'URL dell'API. Ad esempio, il codice seguente crea un'istanza della `SearchURL` classe e punta il dominio al cloud di Azure per enti pubblici.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Se si accede direttamente ai servizi REST di Azure Maps, modificare il dominio dell'URL in `atlas.azure.us` . Ad esempio, se si usa il servizio API di ricerca, modificare il dominio URL da `https://atlas.microsoft.com/search/` a `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>Framework JavaScript

Se si sviluppa con un framework JavaScript, può risultare utile uno dei progetti open source seguenti:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): wrapper di Angular 10 intorno alle mappe di Azure.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): componente Blazor di Mappe di Azure.
- [Componente React di Mappe di Azure](https://github.com/WiredSolutions/react-azure-maps): wrapper React per il controllo di Mappe di Azure.
- [Mappe di Azure per Vue](https://github.com/rickyruiz/vue-azure-maps): componente di Mappe di Azure per l'applicazione Vue.

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
