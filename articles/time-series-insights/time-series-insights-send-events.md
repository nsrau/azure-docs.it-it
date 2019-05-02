---
title: Inviare eventi a un ambiente di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare un hub eventi e su come eseguire un'applicazione di esempio per eseguire il push degli eventi che è possibile visualizzare in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 55b19a6cf71730858fcf42880f71a2c9c07a3b31
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683971"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Inviare eventi a un ambiente Time Series Insights usando un hub eventi

Questo articolo illustra come creare e configurare un hub eventi in Hub eventi di Azure e come eseguire un'applicazione di esempio per eseguire il push degli eventi. Se è presente un hub eventi con eventi in formato JSON, saltare questa esercitazione e visualizzare l'ambiente in [Azure Time Series Insights ](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurare un hub eventi

1. Per informazioni su come creare un hub eventi, vedere la [documentazione di Hub eventi](https://docs.microsoft.com/azure/event-hubs/).
1. Nella casella di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.
1. Selezionare l'hub eventi.
1. Quando si crea un hub eventi, in realtà si crea uno spazio dei nomi dell'hub eventi. Se nello spazio dei nomi non è ancora stato creato un hub eventi, crearne uno in **Entità**.  

    ![Elenco degli hub eventi][1]

1. Dopo aver creato un hub eventi, selezionarlo nell'elenco degli hub eventi.
1. Nel menu, in **Entità** selezionare **Hub eventi**.
1. Selezionare il nome dell'hub eventi per configurarlo.
1. In **Entità** selezionare **Gruppi di consumer** e quindi selezionare **Gruppo di consumer**.

    ![Creare un gruppo di consumer][2]

1. Assicurarsi di creare un gruppo di consumer usato esclusivamente dall'origine evento di Time Series Insights.

    > [!IMPORTANT]
    > Verificare che questo gruppo di consumer non venga usato da altri servizi, ad esempio un processo di Analisi di flusso di Azure o un altro ambiente Time Series Insights. Se il gruppo di consumer viene usato da altri servizi, le operazioni di lettura sono compromesse negativamente per questo ambiente e per altri servizi. Se si usa **$Default** come gruppo di consumer, altri lettori potrebbero potenzialmente usare nuovamente il gruppo di consumer.

1. Nel menu, in **Impostazioni** selezionare **Criteri di accesso condiviso** e quindi selezionare **Aggiungi**.

    ![Selezionare i criteri di accesso condiviso e quindi selezionare il pulsante Aggiungi][3]

1. Nel riquadro **Aggiungi nuovi criteri di accesso condiviso** creare un accesso condiviso denominato **MySendPolicy** che verrà usato per inviare gli eventi negli esempi C# più avanti in questo articolo.

    ![Nella casella Nome criteri immettere MySendPolicy][4]

1. In **Attestazione** selezionare la casella di controllo **Invia**.

## <a name="add-a-time-series-insights-instance"></a>Aggiungere un'istanza di Time Series Insights

L'aggiornamento di Time Series Insights usa le istanze per aggiungere dati contestuali ai dati di telemetria in ingresso. I dati sono uniti in fase di query tramite un **ID serie temporale**. L'**ID serie temporale** per il progetto windmills di esempio usato più avanti in questo articolo è **Id**. Per altre informazioni sulle istanze di Time Series Insight e sull'**ID serie temporale**, vedere [Time Series Models](./time-series-insights-update-tsm.md) (Modelli Time Series).

### <a name="create-a-time-series-insights-event-source"></a>Creare un'origine evento di Time Series Insights

1. Completare i passaggi per [creare un'origine evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) se non ne è stata ancora creata una.

1. Impostare un valore per `timeSeriesId`. Per altre informazioni su **ID serie temporale**, vedere [Time Series Models](./time-series-insights-update-tsm.md) (Modelli Time Series).

### <a name="push-events"></a>Eseguire il push degli eventi (esempio windmills)

1. Nella barra di ricerca cercare **hub eventi**. Nell'elenco restituito selezionare **Hub eventi**.

1. Selezionare l'hub eventi.

1. Andare a **Criteri di accesso condiviso** > **RootManageSharedAccessKey**. Copiare il valore per **Stringa di connessione - chiave primaria**.

    ![Copiare il valore per la stringa di connessione della chiave primaria][5]

1. Passare a https://tsiclientsample.azurewebsites.net/windFarmGen.html. L'URL esegue i dispositivi windmill simulati.
1. Nella casella **Event Hub Connection String** (Stringa di connessione hub eventi) nella pagina Web incollare la stringa di connessione copiata in [Eseguire il push degli eventi](#push-events).
  
    ![Incollare la stringa di connessione della chiave primaria nella casella della stringa di connessione dell'hub eventi][6]

1. Selezionare **Click to start** (Fare clic per avviare). Il simulatore genera istanza JSON che è possibile usare direttamente.

1. Tornare all'hub eventi nel portale di Azure. Nella pagina **Panoramica** pagina vengono visualizzati i nuovi eventi ricevuti dall'hub eventi:

    ![Pagina di panoramica dell'hub eventi che mostra le metriche per l'hub eventi][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Forme JSON supportate

### <a name="sample-1"></a>Esempio 1

#### <a name="input"></a>Input

Oggetto JSON semplice:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Output: Un evento

|id| timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Esempio 2

#### <a name="input"></a>Input

Una matrice JSON con due oggetti JSON. Ogni oggetto JSON viene convertito in un evento.

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

#### <a name="output-two-events"></a>Output: Due eventi

|id| timestamp|
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

#### <a name="output-two-events"></a>Output: Due eventi

La proprietà **location** viene copiata in ogni evento.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Esempio 4

#### <a name="input"></a>Input

Oggetto JSON con una matrice JSON annidata che contiene due oggetti JSON. Questo input dimostra che le proprietà globali possono essere rappresentate dall'oggetto JSON complesso.

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

#### <a name="output-two-events"></a>Output: Due eventi

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'ambiente](https://insights.timeseries.azure.com) in Time Series Insights explorer.

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
