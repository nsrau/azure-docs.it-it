---
title: Come usare il controllo mappa di Mappe di Azure | Microsoft Docs
description: Informazioni su come usare la libreria JavaScript lato client del controllo mappa di Mappe di Azure.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57850f67b56113036cb6cc37e9f1f2694ba9eb8f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672730"
---
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di Mappe di Azure

La libreria JavaScript lato client del controllo mappa consente di eseguire il rendering delle mappe e delle funzionalità incorporate di Mappe di Azure nelle applicazioni Web e per dispositivi mobili.

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client del controllo mappa.

1. Creare un nuovo file HTML.

2. Caricare Azure Maps Web SDK. A tale scopo, è possibile procedere nei due modi descritti di seguito:
    
    a. Usare la versione CDN di Azure Maps Web SDK ospitata a livello globale aggiungendo gli endpoint dell'URL al foglio di stile e i riferimenti a script nell'elemento `<head>` del file:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. In alternativa, caricare il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) e ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript.

    > npm install azure-maps-control

    Aggiungere quindi i riferimenti alle origini del foglio di stile e dello script di Mappe di Azure all'elemento `<head>` del file:

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Per eseguire il rendering della mappa in modo che riempia l'intero corpo della pagina, aggiungere l'elemento `<style>` seguente all'elemento `<head>`.

    ```html
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

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Per inizializzare il controllo mappa, definire una nuova sezione nel corpo HTML e creare uno script. Usare la propria chiave dell'account di Mappe di Azure oppure le credenziali di Azure Active Directory (AAD) per autenticare la mappa usando le [opzioni di autenticazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Se è necessario creare un account o trovare la chiave, vedere [Come gestire l'account e le chiavi dell'account Mappe di Azure](how-to-manage-account-keys.md). L'opzione **language** specifica la lingua da usare per le etichette e i controlli mappa. Per altre informazioni sulle lingue supportate, vedere [Lingue supportate](supported-languages.md). Se si usa una chiave di sottoscrizione per l'autenticazione:

    ```html
    <script type='text/javascript'>
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

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Visualizzare [l'autenticazione con le mappe di Azure](azure-maps-authentication.md) per altri dettagli.
6. Facoltativamente, può risultare utile aggiungere gli elementi di tag meta seguenti all'inizio della pagina:

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Riassumendo, il file HTML dovrebbe essere simile al seguente:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
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
        
        <script type='text/javascript'>
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

8. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering. Il codice sarà simile al seguente:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Come usare il controllo mappa" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control</a> (Come usare il controllo mappa) per Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare e interagire con una mappa:

> [!div class="nextstepaction"]
> [Creare una mappa](map-create.md)

Informazioni su come applicare uno stile a una mappa:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](choose-map-style.md)
