---
title: Richiesta di dati di transito Mappe di Microsoft Azure
description: In this article, you will learn how to request public transit data using the Microsoft Azure Maps Mobility Service.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335467"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Richiedere dati di trasporto pubblico usando il servizio per dispositivi mobili di Azure MapsRequest public transit data using the Azure Maps Mobility Service 

Questo articolo illustra come usare il servizio [per dispositivi mobili](https://aka.ms/AzureMapsMobilityService) di Azure Maps per richiedere i dati di trasporto pubblico. I dati di transito includono le fermate di transito, le informazioni sul percorso e le stime del tempo di percorrenza.

In questo articolo imparerai come:

* Ottenere un ID area metropolitana utilizzando [l'API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro)
* La richiesta di transito nelle vicinanze smette di utilizzare il servizio [Get Nearby Transit.](https://aka.ms/AzureMapsMobilityNearbyTransit)
* Eseguire query [sull'API Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) per pianificare un percorso tramite il trasporto pubblico.
* Richiedere la geometria del percorso di transito e la pianificazione dettagliata per il percorso utilizzando [l'API Get Transit Itinerary](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Prerequisiti

È innanzitutto necessario disporre di un account di Azure Maps e di una chiave di sottoscrizione per effettuare chiamate alle API di trasporto pubblico di Azure Maps.You first need to have an Azure Maps account and a subscription key to make any calls to the Azure Maps public transit APIs. Per informazioni, seguire le istruzioni in Creare un account per creare un account di Azure Maps.For information, follow instructions in [Create an account](quick-demo-map-app.md#create-an-account-with-azure-maps) to create an Azure Maps account. Segui i passaggi descritti in Ottenere la [chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per il tuo account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).


Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="get-a-metro-area-id"></a>Ottenere un ID area metropolitanaGet a metro area ID

Per richiedere informazioni di transito per una particolare area `metroId` metropolitana, avrai bisogno di quella zona. [L'API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro) consente di richiedere aree metropolitane in cui è disponibile il servizio per dispositivi mobili di Azure Maps.The Get Metro Area API allows you to request metro areas, in which the Azure Maps Mobility Service is available. La risposta include dettagli `metroId` `metroName`quali , e la rappresentazione della geometria dell'area metropolitana in formato GeoJSON.

Facciamo una richiesta per ottenere l'area metropolitana per l'ID dell'area metropolitana di Seattle-Tacoma. La procedura seguente illustra come richiedere l'ID per un'area metropolitana:

1. Apri l'app Postman e creiamo una raccolta per archiviare le richieste. Nella parte superiore dell'app Postman selezionare **Nuovo**. Nella finestra **Crea nuovo** selezionare **Raccolta**.  Assegnare un nome alla raccolta e selezionare il pulsante **Crea.Name** the collection and select the Create button.

2. Per creare la richiesta, selezionare di nuovo **Nuovo.** Nella finestra **Crea nuovo** selezionare **Richiedi**. Immettere un **Nome richiesta** per la richiesta. Selezionare la raccolta creata nel passaggio precedente come percorso in cui salvare la richiesta. Selezionare quindi **Salva**.
    
    ![Creare una richiesta in Postman](./media/how-to-request-transit-data/postman-new.png)

3. Selezionare il metodo **GET** HTTP nella scheda Generatore e immettere l'URL seguente per creare una richiesta GET. Sostituire `{subscription-key}`, con la chiave primaria di Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Dopo una richiesta riuscita, riceverai la seguente risposta:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Copiare `metroId`il , sarà necessario utilizzarlo in un secondo momento.

## <a name="request-nearby-transit-stops"></a>Richiesta fermate di transito nelle vicinanze

Il servizio Azure Maps [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) consente di cercare oggetti di transito.  l'API restituisce i dettagli dell'oggetto di transito, ad esempio le soste di transito pubblico e le biciclette condivise intorno a una determinata posizione. Successivamente faremo una richiesta al servizio per cercare le fermate dei mezzi pubblici nelle vicinanze entro un raggio di 300 metri intorno alla posizione specificata. Nella richiesta, è necessario `metroId` includere il recuperato in precedenza.

Per effettuare una richiesta al [Get Nearby Transit,](https://aka.ms/AzureMapsMobilityNearbyTransit)attenersi alla seguente procedura:

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Get Nearby **stops**.

2. Nella scheda Generatore selezionare il metodo **HTTP GET,** immettere l'URL di richiesta seguente per l'endpoint API e fare clic su **Invia**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Dopo una richiesta riuscita, la struttura di risposta dovrebbe essere simile a quella riportata di seguito:After a successful request, the response structure should look like the one below:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Se si osserva attentamente la struttura di risposta, si noterà che contiene parametri per ogni oggetto di transito. Ogni oggetto transit dispone `id` `type`di `stopName` `mainTransitType`parametri `mainAgencyName`quali , , , , e la posizione, in coordinate, dell'oggetto.

Ai fini dell'apprendimento, useremo una `id` delle fermate degli autobus come origine, per il nostro percorso nella sezione successiva.  


## <a name="request-a-transit-route"></a>Richiedere un percorso di transito

[L'API Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute) di Azure Maps consente la pianificazione del viaggio. Restituisce le migliori opzioni di percorso possibili da un'origine a una destinazione. Il servizio offre diversi tipi di modalità di viaggio, tra cui a piedi, in bicicletta e mezzi pubblici. Successivamente, cercheremo un percorso dalla fermata dell'autobus più vicina alla torre Space Needle di Seattle.

### <a name="get-location-coordinates-for-destination"></a>Ottenere le coordinate di posizione per la destinazioneGet location coordinates for destination

Per ottenere le coordinate di posizione della torre Space Needle, consente di usare il servizio di [ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)di Azure Maps .

Per effettuare una richiesta al servizio di ricerca Fuzzy, attenersi alla seguente procedura:

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Ottieni coordinate **posizione**.

2.  Nella scheda Generatore selezionare il metodo **HTTP GET,** immettere l'URL della richiesta seguente e fare clic su **Invia**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Se si guarda la risposta con attenzione, contiene più posizioni nei risultati per la ricerca Space Needle. Ogni risultato contiene le coordinate di posizione sotto la **posizione**. Copiare `lat` `lon` il e sotto la **posizione** del primo risultato.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Richiedi percorso

Per effettuare una richiesta di percorso, completare i passaggi seguenti:To make a route request, complete the steps below:

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Ottieni informazioni **percorso**.

2. Nella scheda Generatore selezionare il metodo **HTTP GET,** immettere l'URL di richiesta seguente per l'endpoint API e fare clic su **Invia**.

    Richiediamo percorsi di trasporto pubblico per `modeType` un `transitType` bus specificando i parametri e . L'URL della richiesta contiene le posizioni recuperate nelle sezioni precedenti. Per `originType`il , ora abbiamo un **stopId**. E per `destionationType`la , abbiamo la **posizione**.

    Vedere [l'elenco dei parametri URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) che è possibile utilizzare nella richiesta all'API [Get Transit Routes](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. In caso di richiesta riuscita, la struttura di risposta dovrebbe essere simile a quella riportata di seguito:Upon a successful request, the response structure should look like the one below:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Se si osserva attentamente, ci sono più linee **di autobus** nella risposta. Ogni percorso ha un **ID itinerario** univoco e un riepilogo che descrive ogni tappa del percorso. Una tappa del percorso è la parte del percorso tra due punti di sosta. Successivamente, chiederemo i dettagli per il `itineraryId` percorso più veloce utilizzando il nella risposta.

## <a name="request-fastest-route-itinerary"></a>Richiedi itinerario percorso più veloce

Il servizio Azure Maps Get Transit Itinerary consente di richiedere dati per una route specifica usando l'ID **itinerario** della route restituito dal servizio API Get Transit Routes.The Azure Maps [Get Transit Itinerary service](https://aka.ms/AzureMapsMobilityTransitItinerary) allows you to request data for a particular route using the route's itinerary ID ed by the [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) service. Per effettuare una richiesta, completare i passaggi seguenti:To make a request, complete the steps below:

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Ottieni informazioni **transito**.

2. Nella scheda Generatore selezionare il metodo **GET** HTTP. Immettere l'URL di richiesta seguente per l'endpoint API e fare clic su **Invia**.

    Il `detailType` parametro verrà impostato sulla **geometria** in modo che la risposta contenga informazioni di arresto per i trasporti pubblici e la navigazione turn-by-turn per le tratte camminate e in bicicletta del percorso.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. In caso di richiesta riuscita, la struttura di risposta dovrebbe essere simile a quella riportata di seguito:Upon a successful request, the response structure should look like the one below:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Scopri come richiedere dati in tempo reale utilizzando il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati in tempo reale](how-to-request-real-time-data.md)

Esplorare la documentazione dell'API del servizio per dispositivi mobili di Azure MapsExplore the Azure Maps Mobility Service API documentation

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)

