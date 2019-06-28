---
title: Come richiedere i dati in tempo reale in mappe di Azure | Microsoft Docs
description: Richiedere i dati in tempo reale usando il servizio Mobility di mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329665"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>I dati in tempo reale usando il servizio Mobility di Azure esegue il mapping della richiesta

Questo articolo illustra come usare le mappe di Azure [il servizio Mobility](https://aka.ms/AzureMapsMobilityService) di richiedere i dati transiti in tempo reale.

In questo articolo si apprenderà come:


 * Richiesta successivi arrivi in tempo reale per tutte le righe in arrivo presso il determinato arresto
 * Richiedi informazioni in tempo reale per un alloggiamento bike specificato.


## <a name="prerequisites"></a>Prerequisiti

Per rendere tutte le chiamate per il transito pubblica le API di mappe di Azure, è necessario un account di mappe e la chiave. Per informazioni sulla creazione di un account e il recupero di una chiave, vedere [Come gestire l'account e le chiavi dell'account di Mappe di Azure](how-to-manage-account-keys.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="request-real-time-arrivals-for-a-stop"></a>Richieste in entrata in tempo reale per un arresto

Per richiedere dati arrivi in tempo reale per un punto di transito pubblico specifico, è necessario effettuare una richiesta per il [API arrivi in tempo reale](https://aka.ms/AzureMapsMobilityRealTimeArrivals) delle mappe di Azure [il servizio Mobility](https://aka.ms/AzureMapsMobilityService). È necessario il **metroID** e **stopID** per completare la richiesta. Per altre informazioni su come richiedere questi parametri, vedere la procedura dettagliata per la [richiedere le route di trasporto pubblico](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

È possibile usare "522" come nostro metro ID, ovvero nell'area metropolitana ID per l'area "Seattle – Tacoma: Bellevue, WA" e usare l'ID arresto "2060603", ovvero un bus di arrestare in "Ne 24 s & alva Ne, 162nd Bellevue WA". Per richiedere dati in tempo reale in entrata cinque per tutti i successivi arrivi in tempo reale a questo punto, completare i passaggi seguenti:

1. Creare una raccolta in cui archiviare le richieste. Selezionare l'app Postman **New**. Nel **Crea nuovo** finestra, seleziona **raccolta**. Denominare la raccolta e selezionare il **Create** pulsante.

2. Per creare la richiesta, selezionare **New** nuovamente. Nel **Crea nuovo** finestra, seleziona **richiesta**. Immettere un **nome della richiesta** per la richiesta, selezionare la raccolta è stato creato nel passaggio precedente come la posizione in cui si desidera salvare la richiesta e quindi selezionare **salvare**.

    ![Creare una richiesta in Postman](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo HTTP GET nella scheda builder e immettere l'URL seguente per creare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Dopo una richiesta ha esito positivo, si riceverà la risposta seguente.  Si noti che il parametro 'scheduleType' definisce se l'ora di arrivo stimato è basato su dati in tempo reale o statici.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dati in tempo reale per alloggiamento bike

Il [transito Dock Info API Get](https://aka.ms/AzureMapsMobilityTransitDock) del servizio di mobilità di mappe di Azure, consente di richiedere informazioni statiche e in tempo reale, ad esempio disponibilità e vacancy per un determinato bicicletta o scooter alloggiamento di espansione. Microsoft farà una richiesta per ottenere i dati in tempo reale per un alloggiamento di espansione di biciclette.

Per effettuare una richiesta all'API di ottenere transito Dock Info, è necessario il **dockId** per tale stazione. È possibile ottenere l'ID di ancoraggio eseguendo una richiesta di ricerca per il [ottenere API transito nelle vicinanze](https://aka.ms/AzureMapsMobilityNearbyTransit) e impostando il **objectType** parametro "bikeDock". Attenersi alla procedura seguente per ottenere i dati in tempo reale di un alloggiamento di espansione di biciclette.


### <a name="get-dock-id"></a>Ottenere l'ID di ancoraggio

Per ottenere **dockID**, attenersi alla procedura seguente per effettuare una richiesta all'API transito nelle vicinanze ottenere:

1. In Postman fare clic su **nuova richiesta** | **richiesta GET** e denominarlo **Get ancorare ID**.

2.  Nella scheda Builder, selezionare la **ottenere** metodo HTTP, immettere l'URL della richiesta seguente, quindi scegliere **inviare**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Dopo una richiesta ha esito positivo, si riceverà la risposta seguente. Si noti che è ora disponibile il **id** nella risposta, utilizzabile in un secondo momento come un parametro di query nella richiesta all'API di ottenere transito Dock Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Ottenere lo stato di ancoraggio di biciclette in tempo reale

Attenersi alla procedura seguente per effettuare una richiesta per il transito ancorare Info API Get per ottenere i dati in tempo reale di ancoraggio selezionato.

1. In Postman fare clic su **nuova richiesta** | **richiesta GET** e denominarlo **ottenere i dati in tempo reale dock**.

2.  Nella scheda Builder, selezionare la **ottenere** metodo HTTP, immettere l'URL della richiesta seguente, quindi scegliere **inviare**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Dopo una richiesta ha esito positivo, si riceverà una risposta la struttura seguente:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere i dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere i dati di transito](how-to-request-transit-data.md)

Esplora la documentazione dell'API servizio Mobility mappe di Azure:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
