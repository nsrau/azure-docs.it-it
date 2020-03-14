---
title: Eseguire query sui dati di telemetria inseriti
description: Questo articolo descrive come eseguire query sui dati di telemetria inseriti.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b871ccb5cb110cfc6154a415059541c4b94f4106
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137306"
---
# <a name="query-ingested-telemetry-data"></a>Eseguire query sui dati di telemetria inseriti

Questo articolo descrive come eseguire query sui dati dei sensori inseriti da Azure FarmBeats.

L'inserimento di dati da risorse di Internet delle cose, ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Si creano metadati per dispositivi e sensori e quindi si inseriscono i dati cronologici in FarmBeats in un formato canonico. Quando i dati del sensore sono disponibili nell'hub dati FarmBeats, è possibile eseguire query sullo stesso per generare informazioni dettagliate di utilità pratica o modelli di compilazione.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurarsi di aver installato i dati di telemetria del sensore FarmBeats e inseriti dai dispositivi FarmBeats.
Per inserire i dati di telemetria del sensore, vedere Inserire [dati di telemetria cronologici](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Prima di procedere, assicurarsi di avere familiarità con le API REST di FarmBeats, poiché verranno eseguite query sulla telemetria inserita usando le API. Per altre informazioni sulle API FarmBeats, vedere [API REST di FarmBeats](rest-api-in-azure-farmbeats.md). **Assicurarsi di essere in grado di effettuare richieste API all'endpoint dell'hub dati FarmBeats**

## <a name="query-ingested-sensor-telemetry-data"></a>Eseguire query sui dati di telemetria del sensore inseriti

Esistono due modi per accedere ai dati di telemetria ed eseguire query da FarmBeats: API e Time Series Insights (TSI). 

### <a name="query-using-rest-api"></a>Eseguire query con l'API REST

Seguire questa procedura per eseguire una query sui dati di telemetria del sensore inseriti usando le API REST di FarmBeatsri:

1. Identificare il sensore a cui si è interessati. A tale scopo, è possibile effettuare una richiesta GET sull'API/Sensor. Annotare l' **ID** e il **sensorModelId** dell'oggetto sensore interessato.

2. Eseguire un'operazione GET/{ID} sull'API/SensorModel per **sensorModelId** come indicato nel passaggio 1. Il "modello di sensore" include tutti i metadati e i dettagli relativi ai dati di telemetria inseriti dal sensore. Ad esempio, "misura sensore" all'interno dell'oggetto "modello sensore" contiene informazioni dettagliate sulle misure che inviano il sensore e sui tipi e le unità. Ad esempio,

```json
{
    "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
    "dataType": "Double <Data Type - eg. Double>",
    "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
    "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
    "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
    "description": "<Description of the measure>"
}
```
Prendere nota della risposta dalla chiamata GET/{ID} per il modello del sensore.

3. Eseguire una chiamata POST sull'API/Telemetry con il payload di input seguente

```json
{
  "sensorId": "<id of the sensor as noted in step 1>",
  "searchSpan": {
    "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
    "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
  },
  "filter": {
    "tsx": "string"
  },
  "projectedProperties": [
    {
      "additionalProp1": "string",
      "additionalProp2": "string",
      "additionalProp3": "string"
    }
  ]
}
```
4. La risposta dall'API/Telemetry sarà simile alla seguente:

```json
{
  "timestamps": [
    "2020-XX-XXT07:30:00Z",
    "2020-XX-XXT07:45:00Z"
  ],
  "properties": [
    {
      "values": [
        "<id of the sensor>",
        "<id of the sensor>"
      ],
      "name": "Id",
      "type": "String"
    },
    {
      "values": [
        2.1,
        2.2
      ],
      "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
      "type": "Double <Data Type of the value - eg. Double>"
    }
  ]
}
```
Nella risposta di esempio precedente, la telemetria dei sensori sottoposti a query fornisce dati per due timestamp insieme al nome della misura ("moist_soil_last") e ai valori dei dati di telemetria segnalati nei due timestamp. È necessario fare riferimento al modello di sensore associato (come descritto nel passaggio 2) per interpretare il tipo e l'unità dei valori segnalati.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query con Azure Time Series Insights (TSI)

FarmBeats sfrutta [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) per inserire, archiviare, eseguire query e visualizzare i dati a livello di Internet delle cose, ovvero i dati altamente contestuale e ottimizzati per le serie temporali.

I dati di telemetria vengono ricevuti in un EventHub e quindi elaborati e inseriti in un ambiente TSI nel gruppo di risorse FarmBeats. I dati possono quindi essere sottoposti a query direttamente dalla TSI. Per altre informazioni, vedere la [documentazione di TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Attenersi ai passaggi seguenti per visualizzare i dati in TSI

1. Passare al portale di Azure-> gruppo di risorse DataHub di FarmBeats-> selezionare ambiente Time Series Insights (TSI-xxxx)-> criteri di accesso ai dati. Aggiungere un utente con accesso in lettura o collaboratore.
2. Passare alla pagina Panoramica di Time Series Insights ambiente (TSI-xxxx) e selezionare "URL Time Series Insights Explorer". A questo punto sarà possibile visualizzare i dati di telemetria inseriti.

Oltre all'archiviazione, all'esecuzione di query e alla visualizzazione dei dati di telemetria, TSI consente anche l'integrazione in un dashboard Power BI. Altre informazioni sono disponibili [qui]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile eseguire query sui dati dei sensori dall'istanza di Azure FarmBeats. A questo punto, viene illustrato come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le farm.
