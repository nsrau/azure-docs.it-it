---
title: Introduzione al controllo della mappa Web nelle mappe di Azure | Microsoft Docs
description: Informazioni su come usare la libreria JavaScript lato client del controllo mappa di Azure maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c764460fb65287dab0031b27291d2f332cec3b3d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756544"
---
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di mappe di Azure

La libreria JavaScript lato client del controllo mappa consente di eseguire il rendering delle mappe e delle funzionalità incorporate di Mappe di Azure nelle applicazioni Web e per dispositivi mobili.

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client del controllo mappa.

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK. A tale scopo, è possibile procedere nei due modi descritti di seguito:

    a. Usare la versione CDN di Azure Maps Web SDK ospitata a livello globale aggiungendo gli endpoint dell'URL al foglio di stile e i riferimenti a script nell'elemento `<head>` del file:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. In alternativa, caricare il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) e ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript.

    > npm install azure-maps-control

    Aggiungere quindi i riferimenti alle origini del foglio di stile e dello script di Mappe di Azure all'elemento `<head>` del file:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > È possibile importare le definizioni typescript nell'applicazione aggiungendo:
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

4. Nel corpo della pagina aggiungere un elemento `<div>` e assegnargli un `id` **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Per inizializzare il controllo mappa, definire una nuova sezione nel corpo HTML e creare uno script. Passare il `id` della `<div>` della mappa o un `HTMLElement` (ad esempio, `document.getElementById('myMap')`) come primo parametro durante la creazione di un'istanza della classe `Map`. Usare la propria chiave dell'account di Mappe di Azure oppure le credenziali di Azure Active Directory (AAD) per autenticare la mappa usando le [opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se è necessario creare un account o trovare la chiave, vedere [Come gestire l'account e le chiavi dell'account Mappe di Azure](how-to-manage-account-keys.md). L'opzione **language** specifica la lingua da usare per le etichette e i controlli mappa. Per altre informazioni sulle lingue supportate, vedere [Lingue supportate](supported-languages.md). Se si usa una chiave di sottoscrizione per l'autenticazione:

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

    Se si usa Azure Active Directory (AAD) per l'autenticazione:

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
    
    Per altre informazioni, vedere il documento relativo all' [autenticazione con Maps di Azure](azure-maps-authentication.md) .

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

8. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering. Il codice dovrebbe essere simile al seguente:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Come usare il controllo mappa" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Vedere la pagina relativa all' <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>uso del controllo map</a> da parte di mappe di Azure (<a href='https://codepen.io/azuremaps'> @azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizzazione della mappa

Azure Maps offre due modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. La prima opzione consiste nell'aggiungere queste informazioni allo spazio dei nomi `atlas` globale, che determinerà l'impostazione predefinita di tutte le istanze del controllo mappa nell'app. Il codice seguente imposta la lingua in francese ("fr-FR") e la vista regionale su "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

La seconda opzione consiste nel passare queste informazioni nelle opzioni della mappa quando si carica la mappa, ad esempio:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Con il Web SDK è possibile caricare più istanze della mappa nella stessa pagina con impostazioni diverse per lingua e area geografica. Inoltre, è possibile aggiornare queste impostazioni dopo il caricamento della mappa utilizzando la funzione `setStyle` della mappa. 

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR" e la visualizzazione regionale impostata su "auto".

![Immagine mappa che mostra le etichette in francese](./media/how-to-use-map-control/websdk-localization.png)

Un elenco completo delle lingue supportate e delle viste internazionali è documentato [qui](supported-languages.md).

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
> [Esempi di autenticazione di AAD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)