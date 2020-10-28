---
title: Richiedere dati di transito pubblico in tempo reale | Mappe Microsoft Azure
description: Informazioni su come richiedere dati di transito pubblico in tempo reale, ad esempio arrivi a un arresto di transito. Per questo scopo, vedere come usare il servizio Mobility di Azure maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e6f6d0738cb1673b752e35761a112f2ca22a409e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895716"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Richiedere dati di transito pubblico in tempo reale usando il servizio Mobility di Azure Maps

Questo articolo illustra come usare il [servizio Mobility](/rest/api/maps/mobility) di Azure Maps per richiedere dati di transito pubblico in tempo reale.

In questo articolo si apprenderà come richiedere i prossimi arrivi in tempo reale per tutte le righe che arrivano a una determinata interruzione

## <a name="prerequisites"></a>Prerequisiti

Prima di tutto è necessario avere un account Azure Maps e una chiave di sottoscrizione per effettuare chiamate alle API di transito pubblico di Azure maps. Per informazioni, seguire le istruzioni riportate in [creare un account](quick-demo-map-app.md#create-an-azure-maps-account) per creare un account Azure maps. Seguire i passaggi in [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per l'account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="request-real-time-arrivals-for-a-stop"></a>Richiedi arrivi in tempo reale per un'interruzione

Per richiedere i dati di arrivo in tempo reale di un particolare arresto di transito pubblico, è necessario effettuare una richiesta all' [API arrivi in tempo reale](/rest/api/maps/mobility/getrealtimearrivalspreview) del [servizio Mobility](/rest/api/maps/mobility)di Azure maps. Per completare la richiesta è necessario disporre di **metroID** e **stopID** . Per altre informazioni su come richiedere questi parametri, vedere la guida su come [richiedere route di transito pubbliche](./how-to-request-transit-data.md).

Si userà "522" come ID metro, ovvero l'ID metro per l'area "Seattle – Tacoma – Bellevue, WA". Usare "522---2060603" come ID di arresto, questo arresto del bus si trova in "ne 24 St & 162nd Ave ne, Bellevue WA". Per richiedere i cinque dati di arrivo in tempo reale successivi, per tutti gli arrivi Live successivi a questa interruzione, seguire questa procedura:

1. Aprire l'app postazione e creare una raccolta per archiviare le richieste. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea).

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente, come percorso in cui salvare la richiesta. Selezionare quindi **Salva** .

    ![Creare una richiesta in post](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente per creare una richiesta GET. Sostituire `{subscription-key}` con la chiave primaria di Azure maps.

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

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati di transito](how-to-request-transit-data.md)

Esplorare la documentazione dell'API del servizio Mobility di Azure Maps:

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](/rest/api/maps/mobility)