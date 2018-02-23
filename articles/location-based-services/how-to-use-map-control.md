---
title: Come usare il controllo mappa di Servizi Location Based di Azure | Microsoft Docs
description: Informazioni su come usare la libreria JavaScript lato client del controllo mappa di Servizi Location Based di Azure.
services: location-based-services
keywords: Non aggiungere o modificare parole chiave senza consultare l'esperto SEO.
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 494a8308a5ed4ae37ed9561d051155e7433e6193
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Come usare il controllo mappa di Servizi Location Based di Azure
La libreria JavaScript lato client del controllo mappa consente di eseguire il rendering delle mappe e delle funzionalità incorporate di Servizi Location Based di Azure nelle applicazioni Web e per dispositivi mobili. 

## <a name="prerequisites"></a>Prerequisiti
Un account Servizi Location Based di Azure e una chiave. Per informazioni sulla creazione di un account e sul recupero di una chiave, vedere [Come gestire l'account e le chiavi di Servizi Location Based di Azure](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Creare una nuova mappa in una pagina Web usando l'API del controllo mappa
È possibile incorporare una mappa in una pagina Web usando la libreria JavaScript lato client del controllo mappa.

1. Creare un nuovo file con il nome MapSearch.html.

2. Aggiungere i riferimenti alle origini del foglio di stile e dello script di Servizi Location Based di Azure all'elemento `<head>` del file:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Per eseguire il rendering di una nuova mappa nel browser, aggiungere un riferimento **#map** nell'elemento `<style>`.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Per inizializzare il controllo mappa, definire una nuova sezione nel corpo HTML e creare uno script. Usare la chiave dell'account Servizi Location Based di Azure nello script. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Aprire il file nel Web browser e visualizzare la mappa di cui è stato eseguito il rendering.