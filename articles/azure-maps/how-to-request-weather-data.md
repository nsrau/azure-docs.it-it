---
title: Richiedi dati meteorologici in tempo reale e previsione usando i servizi meteo di Azure Maps
description: Informazioni su come richiedere dati meteorologici in tempo reale (correnti) e previsti (minuto, orario giornaliero) con Microsoft Azure Maps Weather Services
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3e0dce10d582006ab8c1dabf6d4b3efc82d8f39f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957113"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Richiedi dati meteorologici in tempo reale e previsione usando i servizi meteo di Azure Maps

Il [servizio Weather](https://docs.microsoft.com/rest/api/maps/weather) Maps di Azure è un set di API RESTful che consente agli sviluppatori di integrare dati meteorologici e visualizzazioni cronologici, in tempo reale e previsioni estremamente dinamici nelle loro soluzioni. Questo articolo illustra come richiedere dati meteorologici in tempo reale e previsione.

In questo articolo si apprenderà come:

* Richiedi i dati meteo in tempo reale usando l' [API Ottieni condizioni correnti](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview).
* Richiedere avvisi meteorologici gravi usando l' [API per ottenere avvisi meteorologici gravi](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview).
* Richiedi previsioni giornaliere usando l' [API per ottenere previsioni giornaliere](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview).
* Richiedere previsioni orarie usando l'API per l' [ottenimento delle previsioni orarie](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview).
* Richiedi previsioni minuto per minuto usando l' [API di previsione del minuto Get](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview).

Questo video offre una panoramica di servizi meteorologici nelle mappe di Azure con esempi di chiamate REST.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player" width="960" height="540" allowFullScreen frameBorder="0" title="Servizi Meteo di Azure Maps per sviluppatori-video Microsoft Channel 9"></iframe>

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >L' [API per la previsione di Get minute](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) richiede una chiave del piano tariffario S1. Tutte le altre API richiedono una chiave del piano tariffario s0.

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), tuttavia è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="request-real-time-weather-data"></a>Richiedi dati meteorologici in tempo reale

L' [API Get Current Conditions](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) restituisce condizioni meteorologiche dettagliate, ad esempio precipitazione, temperatura e vento per una determinata posizione delle coordinate. Inoltre, è possibile recuperare le osservazioni delle ultime 6 o 24 ore per una determinata località. La risposta include dettagli come la data e l'ora di osservazione, la breve descrizione delle condizioni meteo, l'icona meteo, i flag indicatore di precipitazione e la temperatura. Vengono restituiti anche RealFeel™ temperature e l'indice ultravioletto (UV).

In questo esempio si userà l' [API Get Current Conditions](https://docs.microsoft.com/rest/api/maps/weather/getcurrentconditionspreview) per recuperare le condizioni meteo correnti alle coordinate situate a Seattle, WA.

1. Aprire l'app Postman. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea). Questa raccolta verrà usata per il resto degli esempi in questo documento.

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Fare clic sul pulsante di **invio** blu. Il corpo della risposta contiene le informazioni meteo correnti.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Richiedere avvisi meteorologici gravi

[Mappe di Azure Ottieni API per avvisi meteorologici gravi](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) restituisce gli avvisi meteorologici che sono disponibili in tutto il mondo dalle agenzie meteorologiche governative ufficiali e dai principali provider di avvisi meteorologici a livello globale. Il servizio può restituire dettagli quali il tipo di avviso, la categoria, il livello e le descrizioni dettagliate degli avvisi attivi gravi per la località richiesta, ad esempio uragani, temporali, fulmini, onde termiche o incendi di foreste. Ad esempio, i responsabili della logistica possono visualizzare le condizioni meteorologiche gravi su una mappa, insieme alle posizioni aziendali e alle route pianificate, e coordinano ulteriormente con i driver e i ruoli di lavoro locali.

In questo esempio si userà l' [API Get grave Alerts](https://docs.microsoft.com/rest/api/maps/weather/getsevereweatheralertspreview) per recuperare le condizioni meteo correnti alle coordinate situate a Cheyenne, WY.

>[!NOTE]
>In questo esempio vengono recuperati avvisi meteorologici gravi al momento della stesura di questo articolo. È probabile che non siano più presenti avvisi meteorologici gravi nella località richiesta. Per recuperare i dati effettivi gravi sugli avvisi quando si esegue questo esempio, è necessario recuperare i dati in un percorso di coordinate diverso.

1. Aprire l'app post, fare clic su **nuovo** e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella sezione precedente o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Fare clic sul pulsante di **invio** blu. Se non sono presenti avvisi meteorologici gravi, il corpo della risposta conterrà una `results[]` matrice vuota. Se sono presenti avvisi meteorologici gravi, il corpo della risposta contiene qualcosa di simile alla risposta JSON seguente:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Richiedi i dati delle previsioni meteo giornaliere

L' [API Get Daily Forecast](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) restituisce previsioni meteorologiche giornaliere dettagliate, ad esempio temperature e Wind. La richiesta può specificare il numero di giorni da restituire: 1, 5, 10, 15, 25 o 45 giorni per una determinata posizione delle coordinate. La risposta include dettagli quali temperatura, vento, precipitazione, qualità aria e indice UV.  In questo esempio, per cinque giorni viene richiesta l'impostazione `duration=5` .

>[!IMPORTANT]
>Nel piano tariffario s0 è possibile richiedere previsioni giornaliere per i successivi 1, 5, 10 e 15 giorni. Nel piano tariffario S1 è inoltre possibile richiedere previsioni giornaliere per i 25 giorni successivi e 45 giorni.

In questo esempio si userà l'API per [ottenere una previsione giornaliera](https://docs.microsoft.com/rest/api/maps/weather/getdailyforecastpreview) per recuperare le previsioni meteorologiche di cinque giorni per le coordinate situate a Seattle, WA.

1. Aprire l'app post, fare clic su **nuovo** e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella sezione precedente o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Fare clic sul pulsante di **invio** blu. Il corpo della risposta contiene i dati relativi alle previsioni meteorologiche di cinque giorni. Per motivi di brevità, la risposta JSON seguente mostra la previsione per il primo giorno.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Richiedi i dati delle previsioni meteorologiche orarie

L' [API per l'ottenimento delle previsioni orarie](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) restituisce previsioni meteorologiche dettagliate in base all'ora per i successivi 1, 12, 24 (1 giorno), 72 (3 giorni), 120 (5 giorni) e 240 ore (10 giorni) per il percorso di coordinate specificato. L'API restituisce dettagli come temperatura, umidità, vento, precipitazione e indice UV.

>[!IMPORTANT]
>Nel piano tariffario s0 è possibile richiedere previsioni orarie per le successive 1, 12, 24 ore (1 giorno) e 72 ore (3 giorni). Nel piano tariffario S1 è inoltre possibile richiedere una previsione oraria per i successivi 120 (5 giorni) e 240 ore (10 giorni).

In questo esempio si userà l'API per [ottenere una previsione oraria](https://docs.microsoft.com/rest/api/maps/weather/gethourlyforecastpreview) per recuperare le previsioni meteorologiche orarie per le 12 ore successive alle coordinate situate a Seattle, WA.

1. Aprire l'app post, fare clic su **nuovo** e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella sezione precedente o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Fare clic sul pulsante di **invio** blu. Il corpo della risposta contiene i dati delle previsioni meteo per le 12 ore successive. Per motivi di brevità, la risposta JSON seguente mostra la previsione per la prima ora.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Richiedi i dati delle previsioni meteorologiche minuto per minuto

 L' [API per la previsione di Get minute](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) restituisce previsioni minute per minuto per un determinato percorso per i prossimi 120 minuti. Gli utenti possono richiedere previsioni meteorologiche a intervalli di 1, 5 e 15 minuti. La risposta include dettagli quali il tipo di precipitazione (tra cui pioggia, neve o una combinazione di entrambi), ora di inizio e valore di intensità della precipitazione (dBZ).

In questo esempio si userà l'API per la [previsione dei minuti](https://docs.microsoft.com/rest/api/maps/weather/getminuteforecastpreview) di recupero per recuperare le previsioni meteorologiche minuto per minuto alle coordinate situate a Seattle, WA. Le previsioni meteorologiche vengono fornite per i prossimi 120 minuti. La query richiede che la previsione venga specificata a intervalli di 15 minuti, ma è possibile modificare il parametro in modo che sia 1 o 5 minuti.

1. Aprire l'app post, fare clic su **nuovo** e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella sezione precedente o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Fare clic sul pulsante di **invio** blu. Il corpo della risposta contiene i dati delle previsioni meteo per i prossimi 120 minuti, in intervalli di 15 minuti.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Concetti relativi ai servizi meteorologici di Azure Maps](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts)

> [!div class="nextstepaction"]
> [API REST del servizio meteo di Azure Maps](https://docs.microsoft.com/rest/api/maps/weather
)
