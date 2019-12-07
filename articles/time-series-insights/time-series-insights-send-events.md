---
title: Inviare eventi a un ambiente-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare un hub eventi, eseguire un'applicazione di esempio e inviare eventi all'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfd9a8d2723136ef68d983eb99bf2391fb87879
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894813"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Inviare eventi a un ambiente Time Series Insights usando un hub eventi

Questo articolo illustra come creare e configurare un hub eventi in hub eventi di Azure. Viene inoltre descritto come eseguire un'applicazione di esempio per effettuare il push degli eventi Azure Time Series Insights da Hub eventi. Se è presente un hub eventi con eventi in formato JSON, ignorare questa esercitazione e visualizzare l'ambiente in [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurare un hub eventi

1. Per informazioni su come creare un hub eventi, vedere la [documentazione di Hub eventi](https://docs.microsoft.com/azure/event-hubs/).
1. Nella casella di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.
1. Selezionare l'hub eventi.
1. Quando si crea un hub eventi, si sta creando uno spazio dei nomi dell'hub eventi. Se non è ancora stato creato un hub eventi nello spazio dei nomi, nel menu, in **entità**, creare un hub eventi.  

    [![elenco di hub eventi](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Dopo aver creato un hub eventi, selezionarlo nell'elenco degli hub eventi.
1. Nel menu in **entità**selezionare **Hub eventi**.
1. Selezionare il nome dell'hub eventi per configurarlo.
1. In **Panoramica**selezionare **gruppi di consumer**e quindi selezionare **gruppo di consumer**.

    [![creare un gruppo di consumer](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Assicurarsi di creare un gruppo di consumer usato esclusivamente dall'origine evento Time Series Insights.

    > [!IMPORTANT]
    > Assicurarsi che questo gruppo di consumer non venga usato da altri servizi, ad esempio un processo di analisi di flusso di Azure o un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, le operazioni di lettura sono compromesse negativamente per questo ambiente e per altri servizi. Se si usa **$Default** come gruppo di consumer, altri lettori potrebbero potenzialmente usare nuovamente il gruppo di consumer.

1. Nel menu, in **Impostazioni**, selezionare **criteri di accesso condiviso**, quindi selezionare **Aggiungi**.

    [![selezionare criteri di accesso condiviso e quindi selezionare il pulsante Aggiungi](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Nel riquadro **Aggiungi nuovi criteri di accesso condiviso** creare un accesso condiviso denominato **MySendPolicy** Usare questi criteri di accesso condiviso per inviare gli eventi negli C# esempi più avanti in questo articolo.

    [![nella casella Nome criterio immettere MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. In **Claim (attestazione**) selezionare la casella di controllo **Send (Invia** ).

## <a name="add-a-time-series-insights-instance"></a>Aggiungere un'istanza di Time Series Insights

L'aggiornamento di Time Series Insights usa le istanze per aggiungere dati contestuali ai dati di telemetria in ingresso. I dati sono uniti in fase di query tramite un **ID serie temporale**. L' **ID della serie temporale** per il progetto mulini di esempio usato più avanti in questo articolo è `id`. Per ulteriori informazioni sulle istanze di Time Series Insight e sull' **ID della serie**temporale, vedere [modelli Time Series](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Creare un'origine evento di Time Series Insights

1. Completare i passaggi per [creare un'origine evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) se non ne è stata ancora creata una.

1. Impostare un valore per `timeSeriesId`. Per altre informazioni su **ID serie temporale**, vedere [Time Series Models](./time-series-insights-update-tsm.md) (Modelli Time Series).

### <a name="push-events-to-windmills-sample"></a>Esempio di push di eventi a mulini a vento

1. Nella barra di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.

1. Selezionare l'istanza di hub eventi.

1. Passare a **criteri di accesso condiviso** > **MySendPolicy**. Copiare il valore per **stringa di connessione-chiave primaria**.

    [![copiare il valore per la stringa di connessione della chiave primaria](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Passare a https://tsiclientsample.azurewebsites.net/windFarmGen.html. L'URL esegue i dispositivi windmill simulati.
1. Nella casella **stringa di connessione dell'hub eventi** della pagina Web incollare la stringa di connessione copiata nel campo di [input Windmill](#push-events-to-windmills-sample).
  
    [![incollare la stringa di connessione della chiave primaria nella casella stringa di connessione dell'hub eventi](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selezionare **Click to start** (Fare clic per avviare). Il simulatore genera istanza JSON che è possibile usare direttamente.

1. Tornare all'hub eventi nel portale di Azure. Nella pagina **Overview (panoramica** ) vengono visualizzati i nuovi eventi ricevuti dall'hub eventi.

    [![una pagina Panoramica di hub eventi che mostra le metriche per l'hub eventi](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Forme JSON supportate

### <a name="example-one"></a>Esempio uno

* **Input**: un semplice oggetto JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Output**: un evento.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Esempio due

* **Input**: matrice JSON con due oggetti JSON. Ogni oggetto JSON viene convertito in un evento.

    ```JSON
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

* **Output**: due eventi.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Esempio tre

* **Input**: oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON.

    ```JSON
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

* **Output**: due eventi. La proprietà **location** viene copiata in ogni evento.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Esempio quattro

* **Input**: oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON. Questo input dimostra che le proprietà globali possono essere rappresentate dall'oggetto JSON complesso.

    ```JSON
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

* **Output**: due eventi.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare l'ambiente](https://insights.timeseries.azure.com) in Esplora Time Series Insights.

- Scopri di più sui [messaggi dei dispositivi dell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) Internet
