---
title: Inviare eventi a un ambiente di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare un hub eventi e su come eseguire un'applicazione di esempio per eseguire il push degli eventi che è possibile visualizzare in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237705"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Inviare eventi a un ambiente Time Series Insights usando un hub eventi

Questo articolo illustra come creare e configurare un hub eventi in hub eventi di Azure. Viene inoltre descritto come eseguire un'applicazione di esempio per indirizzare gli eventi di Azure Time Series Insights dagli hub eventi. Se si dispone di un hub eventi con eventi in formato JSON, saltare questa esercitazione e visualizzare l'ambiente in [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurare un hub eventi

1. Per informazioni su come creare un hub eventi, vedere la [documentazione di Hub eventi](https://docs.microsoft.com/azure/event-hubs/).
1. Nella casella di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.
1. Selezionare l'hub eventi.
1. Quando si crea un hub eventi, si crea uno spazio dei nomi dell'hub eventi. Se è ancora stato creato un hub eventi nello spazio dei nomi, nel menu sotto **entità**, creare un hub eventi.  

    [![Elenco degli hub eventi](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Dopo aver creato un hub eventi, selezionarlo nell'elenco degli hub eventi.
1. Nel menu sotto **Entities**, selezionare **hub eventi**.
1. Selezionare il nome dell'hub eventi per configurarlo.
1. In **Entità** selezionare **Gruppi di consumer** e quindi selezionare **Gruppo di consumer**.

    [![Creare un gruppo di consumer](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Assicurarsi di che creare un gruppo di consumer usato esclusivamente dall'origine evento Time Series Insights.

    > [!IMPORTANT]
    > Assicurarsi che questo gruppo di consumer non viene usato da nessun altro servizio, ad esempio un processo Azure Stream Analitica o di un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, le operazioni di lettura sono compromesse negativamente per questo ambiente e per altri servizi. Se si usa **$Default** come gruppo di consumer, altri lettori potrebbero potenzialmente usare nuovamente il gruppo di consumer.

1. Nel menu sotto **impostazioni**, selezionare **criteri di accesso condiviso**, quindi selezionare **Aggiungi**.

    [![Selezionare i criteri di accesso condiviso e quindi selezionare il pulsante Aggiungi](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. Nel riquadro **Aggiungi nuovi criteri di accesso condiviso** creare un accesso condiviso denominato **MySendPolicy** Si usa questo criterio di accesso condiviso per inviare eventi nel C# esempi più avanti in questo articolo.

    [![Nella casella nome dei criteri, immettere MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. Sotto **attestazione**, selezionare la **inviare** casella di controllo.

## <a name="add-a-time-series-insights-instance"></a>Aggiungere un'istanza di Time Series Insights

L'aggiornamento di Time Series Insights usa le istanze per aggiungere dati contestuali ai dati di telemetria in ingresso. I dati sono uniti in fase di query tramite un **ID serie temporale**. Il **ID serie tempo** per il windmills esempio progetto che viene usato più avanti in questo articolo è `id`. Per altre informazioni sulle istanze di Time Series Insight e sull'**ID serie temporale**, vedere [Time Series Models](./time-series-insights-update-tsm.md) (Modelli Time Series).

### <a name="create-a-time-series-insights-event-source"></a>Creare un'origine evento di Time Series Insights

1. Completare i passaggi per [creare un'origine evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) se non ne è stata ancora creata una.

1. Impostare un valore per `timeSeriesId`. Per altre informazioni su **ID serie temporale**, vedere [Time Series Models](./time-series-insights-update-tsm.md) (Modelli Time Series).

### <a name="push-events"></a>Eseguire il push degli eventi (esempio windmills)

1. Nella barra di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.

1. Selezionare l'hub eventi.

1. Andare a **Criteri di accesso condiviso** > **RootManageSharedAccessKey**. Copiare il valore per **connessione stringa chiave primaria**.

    [![Copiare il valore per la stringa di connessione chiave primaria](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Passare a https://tsiclientsample.azurewebsites.net/windFarmGen.html. L'URL esegue i dispositivi windmill simulati.
1. Nella casella **Event Hub Connection String** (Stringa di connessione hub eventi) nella pagina Web incollare la stringa di connessione copiata in [Eseguire il push degli eventi](#push-events).
  
    [![Incollare la stringa di connessione chiave primaria nella casella stringa di connessione dell'Hub eventi](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Selezionare **Click to start** (Fare clic per avviare). Il simulatore genera istanza JSON che è possibile usare direttamente.

1. Tornare all'hub eventi nel portale di Azure. Nel **Panoramica** pagina, noterete che i nuovi eventi che vengono ricevuti dall'hub eventi.

    [![Una pagina di panoramica di hub eventi che mostra le metriche dell'hub eventi](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Forme JSON supportate

### <a name="example-one"></a>Esempio n. 1

* **Input**: Un oggetto JSON semplice.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Output**: Un evento.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Esempio 2

* **Input**: Una matrice JSON con due oggetti JSON. Ogni oggetto JSON viene convertito in un evento.

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

* **Output**: Due eventi.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Esempio 3

* **Input**: Oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON.

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

* **Output**: Due eventi. La proprietà **location** viene copiata in ogni evento.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Esempio 4

* **Input**: Oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON. Questo input dimostra che le proprietà globali possono essere rappresentate dall'oggetto JSON complesso.

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

* **Output**: Due eventi.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'ambiente](https://insights.timeseries.azure.com) in Time Series Insights explorer.
