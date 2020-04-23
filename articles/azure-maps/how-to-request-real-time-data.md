---
title: Richiedi i dati di trasporto pubblico in tempo reale Mappe di Microsoft Azure
description: Richiedere dati di trasporto pubblico in tempo reale usando il servizio mobili di Microsoft Azure Maps.Request real-time public transit data using the Microsoft Azure Maps Mobility Service.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086078"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Richiedere dati di trasporto pubblico in tempo reale usando il servizio per dispositivi mobili di Azure MapsRequest real-time public transit data using the Azure Maps Mobility Service

Questo articolo illustra come usare il servizio [per dispositivi mobili](https://aka.ms/AzureMapsMobilityService) di Azure Maps per richiedere dati di trasporto pubblico in tempo reale.

In questo articolo, imparerai come richiedere i prossimi arrivi in tempo reale per tutte le linee che arrivano a una determinata fermata

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

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito utilizzando il servizio Mobility:Learn how to request transit data using Mobility Service:

> [!div class="nextstepaction"]
> [Come richiedere i dati di transito](how-to-request-transit-data.md)

Esplorare la documentazione dell'API del servizio per dispositivi mobili di Azure Maps:Explore the Azure Maps Mobility Service API documentation:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
