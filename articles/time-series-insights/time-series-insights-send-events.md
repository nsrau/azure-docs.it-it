---
title: Come inviare eventi a un ambiente Azure Time Series Insights | Microsoft Docs
description: Questa esercitazione illustra come creare e configurare un hub eventi ed eseguire un'applicazione di esempio per effettuare il push degli eventi da visualizzare in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: db528f5a02d90e7e1e2e2cd3da30f04755575777
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657789"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Inviare eventi a un ambiente Time Series Insights tramite un hub eventi
Questo articolo illustra come creare e configurare un hub eventi ed eseguire un'applicazione di esempio per effettuare il push degli eventi. Se è presente un hub eventi con eventi in formato JSON, saltare questa esercitazione e visualizzare l'ambiente in [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configurare un hub eventi
1. Per creare un hub eventi, seguire le istruzioni contenute nella [documentazione](../event-hubs/event-hubs-create.md) sugli hub eventi.

2. Cercare **hub eventi** nella barra di ricerca. Nell'elenco dei risultati fare clic su **Hub eventi**.

3. Per selezionare l'hub eventi, fare clic sul relativo nome.

4. In **Entità** nella finestra di configurazione centrale fare clic di nuovo su **Hub eventi**.

5. Selezionare il nome dell'hub eventi per configurarlo.

  ![Selezionare il gruppo di consumer dell'hub eventi](media/send-events/consumer-group.png)

6. In **Entità** selezionare **Gruppi di consumer**.
 
7. Assicurarsi di creare un gruppo di consumer usato esclusivamente dall'origine evento di Time Series Insights.

   > [!IMPORTANT]
   > Verificare che questo gruppo di consumer non venga usato da altri servizi, ad esempio un processo di Analisi di flusso o un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, l'operazione di lettura viene compromessa per questo ambiente e per gli altri servizi. Se si usa "$Default" come gruppo di consumer, è probabile che venga usato anche da altri lettori.

8. Sotto l'intestazione **Impostazioni** selezionare **Criteri di accesso condiviso**.

9. Nell'hub eventi creare **MySendPolicy** che viene usato per inviare eventi nell'esempio csharp.

  ![Selezionare Criteri di accesso condiviso e fare clic sul pulsante Aggiungi](media/send-events/shared-access-policy.png)  

  ![Aggiungere nuovi criteri di accesso condiviso](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>Aggiungere un set di dati di riferimento Time Series Insights 
L'uso di dati di riferimento in Time Series Insights consente di contestualizzare i dati di telemetria.  Tale contesto aggiunge significato ai dati e facilita le operazioni di filtro e aggregazione.  Time Series Insights esegue il join dei dati di riferimento in fase di ingresso e non può eseguire questa operazione retroattivamente.  È quindi fondamentale aggiungere i dati di riferimento prima di aggiungere un'origine eventi contenente dati.  I dati come il tipo di percorso o di sensore sono dimensioni utili da unire in join a un ID di dispositivo/tag/sensore per facilitare le operazioni di sezionamento e filtro.  

> [!IMPORTANT]
> La disponibilità di un set di dati di riferimento configurato è fondamentale quando si caricano dati cronologici.

Verificare che i dati di riferimento siano già disponibili quando si eseguono caricamenti in blocco di dati cronologici in Time Series Insights.  Tenere presente che Time Series Insights inizia immediatamente la lettura da un'origine eventi unita in join se tale origine eventi include dati.  È utile aspettare a creare il join tra un'origine eventi e Time Series Insights fino a quando i dati di riferimento non sono a disposizione, in particolare se l'origine eventi include dati. In alternativa, è possibile attendere a eseguire il push dei dati in tale origine eventi fino a quando non è disponibile il set di dati di riferimento.

Per gestire i dati di riferimento, sono disponibili l'interfaccia utente basata sul Web in TSI Explorer e un'API C# programmatica. TSI Explorer include un'esperienza utente visiva per caricare i file o incollare set di dati di riferimento esistenti in formato JSON o CSV. Con l'API è possibile creare app personalizzate all'occorrenza.

Per altre informazioni sulla gestione dei dati di riferimento in Time Series Insights, vedere l'[articolo sui dati di riferimento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="create-time-series-insights-event-source"></a>Creare un'origine evento di Time Series Insights
1. Se non è ancora stata creata un'origine evento, seguire [queste istruzioni](time-series-insights-how-to-add-an-event-source-eventhub.md) per crearne una.

2. Specificare **deviceTimestamp** come nome della proprietà Timestamp. Questa proprietà viene usata come timestamp effettivo nell'esempio C#. Il nome della proprietà timestamp applica la distinzione tra maiuscole e minuscole e i valori devono avere il formato __aaaa-MM-ggTHH:mm:ss.FFFFFFFK__ quando viene inviato come file JSON all'hub eventi. Se la proprietà non esiste nell'evento, verrà usata l'ora in cui l'evento è stato accodato nell'hub eventi.

  ![Creare un'origine evento](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Codice di esempio per effettuare il push degli eventi
1. Passare al criterio dell'hub eventi denominato **MySendPolicy**. Copiare la **stringa di connessione** con la chiave del criterio.

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
## <a name="supported-json-shapes"></a>Forme JSON supportate
### <a name="sample-1"></a>Esempio 1

#### <a name="input"></a>Input

Un oggetto JSON semplice.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>Output - un evento

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Esempio 2

#### <a name="input"></a>Input
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
#### <a name="output---two-events"></a>Output - due eventi

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Esempio 3

#### <a name="input"></a>Input

Un oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON:
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
#### <a name="output---two-events"></a>Output - due eventi
Si noti che la proprietà "location" viene copiata in ogni evento.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Esempio 4

#### <a name="input"></a>Input

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
#### <a name="output---two-events"></a>Output - due eventi

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|



## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Visualizzare l'ambiente in Time Series Insights Explorer](https://insights.timeseries.azure.com).
