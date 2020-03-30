---
title: Eseguire query sui dati di telemetria ingeritiQuery ingested telemetry data
description: In questo articolo viene descritto come eseguire query sui dati di telemetria ingeriti.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349813"
---
# <a name="query-ingested-telemetry-data"></a>Eseguire query sui dati di telemetria ingeritiQuery ingested telemetry data

Questo articolo descrive come eseguire una query sui dati dei sensori ingeriti da Azure FarmBeats.This article describes how to queryinged sensor data from Azure FarmBeats.

L'inserimento di dati dalle risorse Internet of Things (IoT), ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Creare metadati per dispositivi e sensori e quindi immettere i dati cronologici in FarmBeats in un formato canonico. Una volta che i dati del sensore sono disponibili su FarmBeats Datahub, possiamo eseguire una query lo stesso per generare informazioni dettagliate utilizzabili o creare modelli.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurarsi di aver installato FarmBeats e dati di telemetria del sensore ingeriti dai dispositivi IoT a FarmBeats.

Per ingerire i dati di telemetria del sensore, visitare la visita [dati di telemetria cronologici](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Prima di procedere, è inoltre necessario assicurarsi di avere familiarità con le API REST FarmBeats, poiché verrà eseguito una query sui dati di telemetria ingeriti usando le API. Per ulteriori informazioni sulle API FarmBeats, vedere [FarmBeats REST APIs](rest-api-in-azure-farmbeats.md). **Assicurarsi di essere in grado di effettuare richieste API all'endpoint FarmBeats Datahub.**

## <a name="query-ingested-sensor-telemetry-data"></a>Interrogare i dati di telemetria del sensore ingeritiQuery ingested sensor telemetry data

Esistono due modi per accedere ed eseguire query sui dati di telemetria da FarmBeats:There are two ways to access and query telemetry data from FarmBeats:

- API e
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query using REST API

Seguire i passaggi per eseguire una query sui dati di telemetria del sensore ingeriti usando le API REST farmBeats:Follow the steps to query the ingested sensor telemetry data using FarmBeats REST APIs:

1. Identificare il sensore desiderato. A tale scopo, effettuare una richiesta GET sull'API /Sensor.You can do this by making a GET request on /Sensor API.

> [!NOTE]
> **L'id** e **sensorModelId** dell'oggetto sensore interessato.

2. Creare un'API GET/'id' in /SensorModel PER **sensorModelId** come indicato nel passaggio 1. Il "Modello Sensor" contiene tutti i metadati e i dettagli relativi ai dati di telemetria ingeriti dal sensore. Ad esempio, **Sensor Measure** all'interno dell'oggetto **Sensor Model** contiene dettagli sulle misure che inviano il sensore e in quali tipi e unità. Ad esempio,

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
Prendere nota della risposta dalla chiamata GET/ s id per il modello Sensor.

3. Eseguire una chiamata POST sull'API /Telemetry con il payload di input seguenteDo a POST call on /Telemetry API with the following input payload

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
4. La risposta dall'API /Telemetry sarà simile alla seguente:The response from the /Telemetry API will look something like this:

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
Nella risposta di esempio precedente, i dati di telemetria del sensore sottoposto a query forniscono i dati per due timestamp insieme al nome della misura ("moist_soil_last") e ai valori dei dati di telemetria segnalati nei due timestamp. È necessario fare riferimento al modello Sensor associato (come descritto nel passaggio 2) per interpretare il tipo e l'unità dei valori segnalati.

### <a name="query-using-azure-time-series-insights-tsi"></a>Eseguire query tramite Azure Time Series Insights (TSI)

FarmBeats sfrutta [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) per l'inserimento, l'archiviazione, l'archiviazione e la visualizzazione dei dati su scala IoT: dati altamente contestualizzati e ottimizzati per le serie temporali.

I dati di telemetria vengono ricevuti in un EventHub e quindi elaborati e inviati in un ambiente TSI all'interno del gruppo di risorse FarmBeats.Telemetry data is received on an EventHub and then processed and pushed to a TSI environment within FarmBeats resource group. I dati possono quindi essere interrogati direttamente dal TSI. Per ulteriori informazioni, vedere la [documentazione di TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Seguire i passaggi per visualizzare i dati su TSI:

1. Passare al gruppo di risorse DataHub di**FarmBeats portale di** **Azure** > > selezionare Ambiente Time **Series Insights** (tsi-xxxx) > Criteri di **accesso ai dati**. Aggiungere un utente con accesso Lettore o Collaboratore.Add user with Reader or Contributor access.
2. Passare alla pagina **Panoramica** dell'ambiente **Time Series Insights** (tsi-xxxx) e selezionare l'URL di Time Series Insights **Explorer.** A questo punto sarà possibile visualizzare i dati di telemetria ingeriti.

Oltre all'archiviazione, all'esecuzione di query e alla visualizzazione dei dati di telemetria, TSI consente anche l'integrazione in un dashboard di Power BI. Per ulteriori informazioni, vedere [qui]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Passaggi successivi

Sono ora stati sottoposti a query sui dati del sensore dall'istanza di Azure FarmBeats.You have now queryed sensor data from your Azure FarmBeats instance. A questo punto, scopri come [generare mappe](generate-maps-in-azure-farmbeats.md#generate-maps) per le tue farm.
