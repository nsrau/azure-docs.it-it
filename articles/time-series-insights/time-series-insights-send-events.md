---
title: Inviare eventi a un ambiente - Azure Time Series Insights Documenti Microsoft
description: Informazioni su come configurare un hub eventi, eseguire un'applicazione di esempio e inviare eventi all'ambiente Azure Time Series Insights.Learn how to configure an event hub, run a sample application, and send events to your Azure Time Series Insights environment.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254248"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Inviare eventi a un ambiente Time Series Insights usando un hub eventi

Questo articolo illustra come creare e configurare un hub eventi negli hub eventi di Azure.This article explains how to create and configure an event hub in Azure Event Hubs. Viene inoltre descritto come eseguire un'applicazione di esempio per eseguire il push degli eventi in Azure Time Series Insights dagli hub eventi. Se si dispone di un hub eventi esistente con eventi in formato JSON, ignorare questa esercitazione e visualizzare l'ambiente in [Azure Time Series Insights.](./time-series-insights-update-create-environment.md)

## <a name="configure-an-event-hub"></a>Configurare un hub eventi

1. Per informazioni su come creare un hub eventi, leggere la documentazione di [Hub eventi.](https://docs.microsoft.com/azure/event-hubs/)
1. Nella casella di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.
1. Selezionare l'hub eventi.
1. Quando si crea un hub eventi, si crea uno spazio dei nomi dell'hub eventi. Se non è ancora stato creato un hub eventi all'interno dello spazio dei nomi, nel menu, in **Entità**, creare un hub eventi.  

    [![Elenco degli hub eventi](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Dopo aver creato un hub eventi, selezionarlo nell'elenco degli hub eventi.
1. Nel menu, in **Entità**, selezionare **Hub eventi**.
1. Selezionare il nome dell'hub eventi per configurarlo.
1. In **Panoramica**selezionare **Gruppi di consumer**, quindi Gruppo di **consumer**.

    [![Creare un gruppo di consumer](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Assicurati di creare un gruppo di consumer usato esclusivamente dall'origine eventi Time Series Insights.

    > [!IMPORTANT]
    > Assicurarsi che questo gruppo di consumer non sia usato da altri servizi, ad esempio un processo di Analisi di flusso di Azure o un altro ambiente Time Series Insights.Make sure that this consumer group isn't used by any other service, such as an Azure Stream Analytics job or another Time Series Insights environment. Se il gruppo di consumer viene usato da altri servizi, le operazioni di lettura sono compromesse negativamente per questo ambiente e per altri servizi. Se si usa **$Default** come gruppo di consumer, altri lettori potrebbero potenzialmente usare nuovamente il gruppo di consumer.

1. Nel menu, in **Impostazioni**, selezionare **Criteri di accesso condiviso**, quindi **Aggiungi**.

    [![Selezionare i criteri di accesso condiviso e quindi selezionare il pulsante Aggiungi](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Nel riquadro **Aggiungi nuovi criteri di accesso condiviso** creare un accesso condiviso denominato **MySendPolicy** Utilizzare questo criterio di accesso condiviso per inviare gli eventi negli esempi di C , più avanti in questo articolo.

    [![Nella casella Nome criteri immettere MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. In **Attestazione**selezionare la casella di controllo **Invia.**

## <a name="add-a-time-series-insights-instance"></a>Aggiungere un'istanza di Time Series Insights

L'aggiornamento di Time Series Insights usa le istanze per aggiungere dati contestuali ai dati di telemetria in ingresso. I dati sono uniti in fase di query tramite un **ID serie temporale**. **L'ID della serie temporale** per il progetto di mulini a vento di esempio utilizzato più avanti in questo articolo è `id`. Per ulteriori informazioni sulle istanze di Time Series Insight e **sull'ID serie temporali**, leggere [Time Series Models](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Creare un'origine evento di Time Series Insights

1. Completare i passaggi per [creare un'origine evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) se non ne è stata ancora creata una.

1. Impostare un valore per `timeSeriesId`. Per ulteriori informazioni **sull'ID serie temporali**, leggere [Modelli Time Series](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Esempio di eventi push per i mulini a vento

1. Nella barra di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.

1. Selezionare l'istanza dell'hub eventi.

1. Passare a **Criteri** > di accesso condiviso**MySendPolicy**. Copiare il valore di **Connection string-primary key**.

    [![Copiare il valore per la stringa di connessione della chiave primaria](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Passare a https://tsiclientsample.azurewebsites.net/windFarmGen.html. L'URL crea ed esegue dispositivi simulate per la mulina a vento.
1. Nella casella Stringa di **connessione hub eventi** della pagina Web incollare la stringa di connessione copiata nel campo di input del [mulino](#push-events-to-windmills-sample)a vento.
  
    [![Incollare la stringa di connessione della chiave primaria nella casella della stringa di connessione dell'hub eventi](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selezionare **Click to start** (Fare clic per avviare). 

    > [!TIP]
    > Il simulatore di mulino a vento crea anche JSON che è possibile usare come payload con le [API di query GA Time Series Insights.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)

    > [!NOTE]
    > Il simulatore continuerà a inviare dati fino alla chiusura della scheda del browser.

1. Tornare all'hub eventi nel portale di Azure. Nella pagina **Panoramica** vengono visualizzati i nuovi eventi ricevuti dall'hub eventi.

    [![Pagina di panoramica dell'hub eventi che mostra le metriche per l'hub eventi](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Forme JSON supportate

### <a name="example-one"></a>Esempio uno

* **Input**: Un semplice oggetto JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Output**: Un evento.

    |id| timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Esempio due

* **Input**: una matrice JSON con due oggetti JSON. Ogni oggetto JSON viene convertito in un evento.

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

    |id| timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Esempio tre

* **Input:** un oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON.

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

    |posizione|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Esempio quattro

* **Input:** un oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON. Questo input dimostra che le proprietà globali possono essere rappresentate dall'oggetto JSON complesso.

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

    |posizione|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare l'ambiente](https://insights.timeseries.azure.com) in Esplora time Series Insights.

- Ulteriori informazioni sui [messaggi del dispositivo dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
