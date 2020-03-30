---
title: Richiedere i dati di transito in tempo reale Mappe di Microsoft Azure
description: Richiedere dati in tempo reale usando il servizio per dispositivi mobili di Microsoft Azure Maps.Request real-time data using the Microsoft Azure Maps Mobility Service.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335474"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Richiedere dati in tempo reale usando il servizio per dispositivi mobili di Azure MapsRequest real-time data using the Azure Maps Mobility Service

Questo articolo illustra come usare il servizio [per dispositivi mobili](https://aka.ms/AzureMapsMobilityService) di Azure Maps per richiedere dati di transito in tempo reale.

In questo articolo viene spiegato come:


 * Richiedi i prossimi arrivi in tempo reale per tutte le linee che arrivano a una determinata fermata
 * Richiedi informazioni in tempo reale per una determinata stazione di attracco per biciclette.


## <a name="prerequisites"></a>Prerequisiti

È innanzitutto necessario disporre di un account di Azure Maps e di una chiave di sottoscrizione per effettuare chiamate alle API di trasporto pubblico di Azure Maps.You first need to have an Azure Maps account and a subscription key to make any calls to the Azure Maps public transit APIs. Per informazioni, seguire le istruzioni in Creare un account per creare un account di Azure Maps.For information, follow instructions in [Create an account](quick-demo-map-app.md#create-an-account-with-azure-maps) to create an Azure Maps account. Segui i passaggi descritti in Ottenere la [chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per il tuo account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).


Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="request-real-time-arrivals-for-a-stop"></a>Richiedi arrivi in tempo reale per una sosta

Per richiedere i dati degli arrivi in tempo reale di un particolare arresto del trasporto pubblico, è necessario inviare una richiesta [all'API Arrivi](https://aka.ms/AzureMapsMobilityRealTimeArrivals) in tempo reale del servizio per dispositivi [mobili](https://aka.ms/AzureMapsMobilityService)di Azure Maps. Per completare la richiesta sono necessari **il metroID** e **stopID.** Per ulteriori informazioni su come richiedere questi parametri, consulta la nostra guida su come richiedere percorsi di [trasporto pubblico.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Usiamo "522" come ID della metropolitana, che è l'ID della metropolitana per l'area "Seattle-Tacoma–Bellevue, WA". Utilizzare "522---2060603" come ID fermata della fermata, questa fermata dell'autobus è a "Ne 24th St & 162nd Ave Ne, Bellevue WA". Per richiedere i successivi cinque dati degli arrivi in tempo reale, per tutti i prossimi arrivi in tempo reale a questa fermata, completare i seguenti passaggi:

1. Apri l'app Postman e creiamo una raccolta per archiviare le richieste. Nella parte superiore dell'app Postman selezionare **Nuovo**. Nella finestra **Crea nuovo** selezionare **Raccolta**.  Assegnare un nome alla raccolta e selezionare il pulsante **Crea.Name** the collection and select the Create button.

2. Per creare la richiesta, selezionare di nuovo **Nuovo.** Nella finestra **Crea nuovo** selezionare **Richiedi**. Immettere un **Nome richiesta** per la richiesta. Selezionare la raccolta creata nel passaggio precedente, come percorso in cui salvare la richiesta. Selezionare quindi **Salva**.

    ![Creare una richiesta in Postman](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo **GET** HTTP nella scheda Generatore e immettere l'URL seguente per creare una richiesta GET. Sostituire `{subscription-key}`, con la chiave primaria di Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Dopo una richiesta riuscita, riceverai la seguente risposta.  Si noti che il parametro 'scheduleType' definisce se l'ora di arrivo stimata è basata su dati statici o in tempo reale.

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


## <a name="real-time-data-for-bike-docking-station"></a>Dati in tempo reale per docking station in tempo reale

[L'API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) consente agli utenti di richiedere informazioni statiche e in tempo reale. Ad esempio, gli utenti possono richiedere informazioni sulla disponibilità e sui posti vacanti per una bicicletta o un'alloggiamento di espansione per scooter. Anche [l'API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) fa parte del servizio [Mobility](https://aka.ms/AzureMapsMobilityService)di Azure Maps.

Per effettuare una richiesta [all'API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock), è necessario il **dockId** per tale stazione. È possibile ottenere l'ID dock effettuando una richiesta di ricerca per [l'API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) con il parametro **objectType** assegnato a "bikeDock". Segui i passaggi qui sotto per ottenere i dati in tempo reale di un docking station per le biciclette.


### <a name="get-dock-id"></a>Ottenere l'ID dockGet dock ID

Per ottenere **dockID**, attenersi alla seguente procedura per effettuare una richiesta all'API Get Nearby Transit:

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Get dock **ID**.

2.  Nella scheda Generatore selezionare il metodo **HTTP GET,** immettere l'URL della richiesta seguente e fare clic su **Invia**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Dopo una richiesta riuscita, riceverai la seguente risposta. Si noti che ora abbiamo **l'id** nella risposta, che può essere utilizzato in un secondo momento come parametro di query nella richiesta per l'API Get Transit Dock Info.

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


### <a name="get-real-time-bike-dock-status"></a>Ottieni lo stato del molo ciclabile in tempo reale

Segui i passaggi riportati di seguito per effettuare una richiesta all'API Get Transit Dock Info per ottenere dati in tempo reale per il dock selezionato.

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Ottieni dati dock in tempo **reale**.

2.  Nella scheda Generatore selezionare il metodo **HTTP GET,** immettere l'URL della richiesta seguente e fare clic su **Invia**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Dopo una richiesta riuscita, riceverai una risposta della seguente struttura:

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

Informazioni su come richiedere dati di transito utilizzando il servizio Mobility:Learn how to request transit data using Mobility Service:

> [!div class="nextstepaction"]
> [Come richiedere i dati di transito](how-to-request-transit-data.md)

Esplorare la documentazione dell'API del servizio per dispositivi mobili di Azure Maps:Explore the Azure Maps Mobility Service API documentation:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
