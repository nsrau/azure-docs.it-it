---
title: Inviare eventi a un ambiente di Azure Time Series Insights | Microsoft Docs
description: Questa esercitazione illustra come effettuare il push degli eventi all'ambiente Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

# Inviare eventi a un ambiente Time Series Insights tramite un hub eventi

Questa esercitazione illustra come creare e configurare un hub eventi ed eseguire un'applicazione di esempio per effettuare il push degli eventi. Se è presente un hub eventi con eventi in formato JSON, saltare questa esercitazione e visualizzare l'ambiente in [Time Series Insights](https://insights.timeseries.azure.com).

<a id="configure-an-event-hub" class="xliff"></a>

## Configurare un hub eventi
1. Per creare un hub eventi, seguire le istruzioni contenute nella [documentazione](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) sugli hub eventi.

2. Assicurarsi di creare un gruppo di consumer usato esclusivamente dall'origine evento di Time Series Insights.

  > [!IMPORTANT]
  > Verificare che questo gruppo di consumer non venga usato da altri servizi, ad esempio un processo di Analisi di flusso o un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, l'operazione di lettura viene compromessa per questo ambiente e per gli altri servizi. Se si usa "$Default" come gruppo di consumer, è probabile che venga usato anche da altri lettori.

  ![Selezionare il gruppo di consumer dell'hub eventi](media/send-events/consumer-group.png)

3. Nell'hub eventi creare "MySendPolicy" che viene usato per inviare eventi nell'esempio csharp.

  ![Selezionare Criteri di accesso condiviso e fare clic sul pulsante Aggiungi](media/send-events/shared-access-policy.png)  

  ![Aggiungere nuovi criteri di accesso condiviso](media/send-events/shared-access-policy-2.png)  

<a id="create-time-series-insights-event-source" class="xliff"></a>

## Creare un'origine evento di Time Series Insights
1. Se non è ancora stata creata un'origine evento, seguire le istruzioni indicate [qui](time-series-insights-add-event-source.md) per crearne una.

2. Specificare "deviceTimestamp" come nome della proprietà Timestamp. Questa proprietà viene usata come timestamp effettivo nell'esempio csharp. Il nome della proprietà Timestamp applica la distinzione tra maiuscole e minuscole e i valori devono avere il formato __aaaa-MM-ggTHH:mm:ss.FFFFFFFK__ quando viene inviato come file JSON all'hub eventi. Se la proprietà non esiste nell'evento, verrà usata l'ora in cui l'evento è stato accodato nell'hub eventi.

  ![Creare un'origine evento](media/send-events/event-source-1.png)

<a id="sample-code-to-push-events" class="xliff"></a>

## Codice di esempio per effettuare il push degli eventi
1. Passare al criterio dell'hub eventi "MySendPolicy" e copiare la stringa di connessione con la chiave del criterio.

  ![Copiare la stringa di connessione MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Eseguire questo codice per inviare 600 eventi per ognuno dei tre dispositivi. Aggiornare `eventHubConnectionString` con la stringa di connessione.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
<a id="supported-json-shapes" class="xliff"></a>

## Forme JSON supportate
<a id="sample-1" class="xliff"></a>

### Esempio 1

<a id="input" class="xliff"></a>

#### Input

Un oggetto JSON semplice.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
<a id="output---1-event" class="xliff"></a>

#### Output: 1 evento

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

<a id="sample-2" class="xliff"></a>

### Esempio 2

<a id="input" class="xliff"></a>

#### Input
Una matrice JSON con due oggetti JSON. Ogni oggetto JSON verrà convertito in un evento.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
<a id="output---2-events" class="xliff"></a>

#### Output: 2 eventi

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
<a id="sample-3" class="xliff"></a>

### Esempio 3

<a id="input" class="xliff"></a>

#### Input

Un oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON.
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
<a id="output---2-events" class="xliff"></a>

#### Output: 2 eventi
Si noti che la proprietà "location" viene copiata in ogni evento.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

<a id="sample-4" class="xliff"></a>

### Esempio 4

<a id="input" class="xliff"></a>

#### Input

Un oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON. Questo input dimostra che le proprietà globali possono essere rappresentate dall'oggetto JSON complesso.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
<a id="output---2-events" class="xliff"></a>

#### Output: 2 eventi

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

* Visualizzare l'ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com)

