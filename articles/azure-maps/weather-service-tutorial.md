---
title: 'Esercitazione: Unire i dati dei sensori con i dati delle previsioni meteo usando Azure Notebooks (Python) | Microsoft Docs'
description: 'Esercitazione: Questa esercitazione illustra come unire i dati dei sensori con i dati delle previsioni meteo del servizio Mappe di Azure usando Azure Notebooks (Python).'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f641640ff6cf4174e2e1374404d47fc0760f79f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979577"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Esercitazione: Unire i dati dei sensori con i dati delle previsioni meteo usando Azure Notebooks (Python)

L'energia eolica è una delle fonti energetiche alternative ai combustibili fossili per contrastare il cambiamento climatico. Poiché il vento stesso non è costante in natura, gli operatori dell'energia eolica devono creare modelli di Machine Learning (ML) per prevedere la capacità di energia eolica sufficiente per soddisfare la domanda di elettricità e assicurare la stabilità della rete elettrica. Questa esercitazione illustra come combinare i dati delle previsioni meteo di Mappe di Azure con un set di dati demo delle posizioni dei lettori con letture del meteo. I dati delle previsioni meteo vengono richiesti con una chiamata al servizio meteo di Mappe di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare file di dati in [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) nel cloud.
> * Caricare i dati demo da un file.
> * Chiamare le API REST di Mappe di Azure in Python.
> * Eseguire il rendering dei dati della posizione sulla mappa.
> * Arricchire i dati demo con i dati delle [previsioni meteo giornaliere](https://aka.ms/AzureMapsWeatherDailyForecast) di Mappe di Azure.
> * Tracciare i dati delle previsioni sui grafici.


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario prima di tutto:

1. Creare una sottoscrizione dell'account di Mappe di Azure nel piano tariffario S0 seguendo le istruzioni riportate in [Gestire l'account di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account).
2. Ottenere la chiave di sottoscrizione primaria per l'account seguendo le istruzioni riportate in [Ottenere la chiave primaria per l'account](./tutorial-search-location.md#getkey).

Per acquisire familiarità con Azure Notebooks e per informazioni introduttive, seguire le istruzioni riportate in [Creare un notebook di Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Il file di Jupyter Notebook per questo progetto può essere scaricato dal [repository di notebook di Jupyter per mappe meteo](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Caricare i moduli e i framework necessari

Per caricare tutti i moduli e i framework necessari, eseguire lo script seguente:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Importare i dati meteo

Per questa esercitazione verranno usate le letture dei dati meteo dei sensori installati in quattro differenti turbine eoliche. I dati di esempio sono costituiti da 30 giorni di letture di dati meteo raccolte dai data center situati vicino a ogni turbina. I dati demo contengono letture dei dati relativi a temperatura, velocità e direzione del vento. È possibile scaricare i dati demo [qui](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Lo script seguente importa i dati demo in Azure Notebooks.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Richiedere i dati delle previsioni giornaliere

Nello scenario di esempio verranno richieste le previsioni giornaliere per ogni posizione dei sensori. Lo script seguente chiama l'[API Daily Forecast](https://aka.ms/AzureMapsWeatherDailyForecast) del servizio meteo di Mappe di Azure per ottenere le previsioni meteo giornaliere per ogni turbina eolica relative ai 15 giorni successivi alla data odierna.


```python
subscription_key = "Your Azure Maps primary subscription key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Lo script seguente esegue il rendering delle posizioni delle turbine sulla mappa chiamando il [servizio Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) di Mappe di Azure.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Posizioni delle turbine](./media/weather-service-tutorial/location-map.png)


Per ampliare i dati demo con i dati delle previsioni, i due tipi di dati verranno raggruppati in base all'ID stazione per il data center meteo.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

La tabella seguente mostra i dati cronologici combinati e i dati delle previsioni per una delle posizioni delle turbine.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Dati raggruppati](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Tracciare i dati delle previsioni

Per verificare la variazione della velocità e della direzione del vento nel corso dei successivi 15 giorni, verranno tracciati i valori previsti rispetto ai giorni per i quali vengono effettuate previsioni.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

I grafici seguenti visualizzano i dati delle previsioni per la variazione della velocità del vento (grafico a sinistra) e della direzione (grafico a destra) nei 15 giorni successivi alla data odierna.

<center>

![Tracciato della velocità del vento](./media/weather-service-tutorial/speed-date-plot.png) ![Tracciato di direzione del vento](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come chiamare le API REST di Mappe di Azure per ottenere i dati delle previsioni meteo e visualizzarli su grafici.

Per altre informazioni su come chiamare le API REST di Mappe di Azure all'interno di Azure Notebooks, vedere [Pianificare itinerari per veicoli elettrici con Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Per esplorare le API di Mappe di Azure usate in questa esercitazione, vedere:

* [Previsioni giornaliere](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Per un elenco completo delle API REST di Mappe di Azure, vedere: [API REST di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/#reference).

Per altre informazioni su Azure Notebooks, vedere: [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
