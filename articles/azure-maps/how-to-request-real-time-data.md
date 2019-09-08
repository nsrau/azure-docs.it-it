---
title: Come richiedere dati in tempo reale in Maps di Azure | Microsoft Docs
description: Richiedi dati in tempo reale usando il servizio Mobility di Azure maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 75fe9c120eae99e517aa52b704fbd6c170e78649
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802287"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Richiedi dati in tempo reale usando il servizio Mobility di Azure Maps

Questo articolo illustra come usare il [servizio Mobility](https://aka.ms/AzureMapsMobilityService) di Azure Maps per richiedere dati di transito in tempo reale.

In questo articolo si apprenderà come:


 * Richiedi arrivi successivi in tempo reale per tutte le righe in arrivo all'arresto specificato
 * Richiedere informazioni in tempo reale per una determinata stazione di ancoraggio della bicicletta.


## <a name="prerequisites"></a>Prerequisiti

Per eseguire chiamate alle API di transito pubblico di Azure Maps, sono necessari un account e una chiave di Maps. Per informazioni sulla creazione di un account e il recupero di una chiave, vedere [Come gestire l'account e le chiavi dell'account di Mappe di Azure](how-to-manage-account-keys.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="request-real-time-arrivals-for-a-stop"></a>Richiedi arrivi in tempo reale per un'interruzione

Per richiedere i dati di arrivo in tempo reale per un particolare arresto di transito pubblico, è necessario effettuare una richiesta all' [API arrivi in tempo reale](https://aka.ms/AzureMapsMobilityRealTimeArrivals) del [servizio Mobility](https://aka.ms/AzureMapsMobilityService)di Azure maps. Per completare la richiesta, è necessario disporre di **metroID** e **stopID** . Per altre informazioni su come richiedere questi parametri, vedere la Guida alle procedure per [richiedere route di transito pubbliche](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Si userà "522" come ID metro, ovvero l'ID metro per l'area "Seattle – Tacoma – Bellevue, WA" e si usa l'ID di arresto "522---2060603", che è un bus di stop a "ne 24 St & 162nd Ave ne, Bellevue WA". Per richiedere i prossimi cinque dati di arrivo in tempo reale per tutti i successivi arrivi Live a questa interruzione, seguire questa procedura:

1. Creare una raccolta in cui archiviare le richieste. Nell'app post, selezionare **nuovo**. Nella finestra **Crea nuova** selezionare **raccolta**. Assegnare un nome alla raccolta e selezionare il pulsante **Crea** .

2. Per creare la richiesta, selezionare nuovamente **nuovo** . Nella finestra **Crea nuova** selezionare **Request**. Immettere un **nome di richiesta** per la richiesta, selezionare la raccolta creata nel passaggio precedente come percorso in cui salvare la richiesta e quindi selezionare **Salva**.

    ![Creare una richiesta in post](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo GET HTTP nella scheda generatore e immettere l'URL seguente per creare una richiesta GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Dopo una richiesta con esito positivo, si riceverà la risposta seguente.  Si noti che il parametro ' scheduleType ' definisce se l'ora di arrivo stimata è basata sui dati in tempo reale o statici.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dati in tempo reale per la stazione di ancoraggio della bicicletta

L' [API Get Transit Dock info](https://aka.ms/AzureMapsMobilityTransitDock) del servizio Mobility di Azure Maps consente di richiedere informazioni statiche e in tempo reale, ad esempio la disponibilità e le informazioni sui posti vacanti per una determinata bicicletta o uno scooter docking station. Si effettuerà una richiesta per ottenere i dati in tempo reale per una stazione di ancoraggio per le biciclette.

Per effettuare una richiesta all'API Get Transit Dock info, è necessario **dockId** per tale stazione. È possibile ottenere l'ID Dock eseguendo una richiesta di ricerca per [ottenere l'API di transito vicina](https://aka.ms/AzureMapsMobilityNearbyTransit) e impostando il parametro **ObjectType** su "bikeDock". Seguire questa procedura per ottenere i dati in tempo reale di una stazione di ancoraggio per le biciclette.


### <a name="get-dock-id"></a>Ottieni ID Dock

Per ottenere **dockID**, seguire questa procedura per effettuare una richiesta all'API di transito vicina:

1. In post, fare clic su **nuova richiesta** | **Get request** e denominarla **Get Dock ID**.

2.  Nella scheda generatore selezionare il metodo **Get** http, immettere l'URL della richiesta seguente e fare clic su **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Dopo una richiesta con esito positivo, si riceverà la risposta seguente. Si noti che ora è disponibile l' **ID** nella risposta, che può essere usato in seguito come parametro di query nella richiesta all'API Get Transit Dock info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
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


### <a name="get-real-time-bike-dock-status"></a>Ottenere lo stato di docking bike in tempo reale

Attenersi alla procedura seguente per effettuare una richiesta all'API Get Transit Dock info per ottenere i dati in tempo reale per il Dock selezionato.

1. In postazione fare clic su **nuova richiesta** | Richiedi**richiesta** e assegnare un nome per **ottenere i dati di ancoraggio in tempo reale**.

2.  Nella scheda generatore selezionare il metodo **Get** http, immettere l'URL della richiesta seguente e fare clic su **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Dopo una richiesta con esito positivo, si riceverà una risposta della struttura seguente:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati di transito](how-to-request-transit-data.md)

Esplorare la documentazione dell'API del servizio Mobility di Azure Maps:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
