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
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166031"
---
# <a name="use-the-azure-maps-map-control"></a>Usare il controllo mappa di Mappe di Azure

La libreria JavaScript lato client del controllo mappa consente di eseguire il rendering delle mappe e delle funzionalità incorporate di Mappe di Azure nelle applicazioni Web e per dispositivi mobili.

## <a name="create-a-new-map-in-a-web-page"></a>Creare una nuova mappa in una pagina Web

È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client del controllo mappa.

1. Creare un nuovo file con il nome **MapSearch.html**.

2. Aggiungere i riferimenti alle origini del foglio di stile e dello script di Mappe di Azure all'elemento `<head>` del file:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Per eseguire il rendering di una nuova mappa nel browser, aggiungere un riferimento **#map** nell'elemento `<style>`:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Per inizializzare il controllo mappa, definire una nuova sezione nel corpo HTML e creare uno script. Usare la chiave dell'account Mappe di Azure nello script. Se è necessario creare un account o trovare la chiave, vedere [Come gestire l'account e le chiavi dell'account Mappe di Azure](how-to-manage-account-keys.md). Il metodo **setLanguage** specifica la lingua da usare per le etichette e i controlli mappa. Per altre informazioni sulle lingue supportate, vedere [Lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare una mappa con un esempio completo:

> [!div class="nextstepaction"]
> [Creare una mappa](map-create.md)

Informazioni su come applicare uno stile a una mappa:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](choose-map-style.md)
