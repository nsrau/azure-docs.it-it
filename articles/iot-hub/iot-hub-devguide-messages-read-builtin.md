---
title: Informazioni sull'endpoint predefinito dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: illustra come usare l'endpoint predefinito compatibile con l'hub eventi per la lettura dei messaggi da dispositivo a cloud."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 52f1316b8167d2e1c3e37dbbfc0059b68e832172
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538562"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leggere messaggi da dispositivo a cloud dall'endpoint predefinito

Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messages/events**) compatibile con l'[hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/). Questo endpoint è attualmente esposto solo con il protocollo [AMQP](https://www.amqp.org/) sulla porta 5671. Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

| Proprietà            | DESCRIZIONE |
| ------------------- | ----------- |
| **Numero di partizioni** | Impostare questa proprietà in fase di creazione per definire il numero di [partizioni](../event-hubs/event-hubs-features.md#partitions) per l'inserimento di eventi da dispositivo a cloud. |
| **Tempo di conservazione**  | Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni. |

L'IoT Hub consente la conservazione dei dati in hub eventi predefinito per un massimo di 7 giorni. È possibile impostare il tempo di conservazione durante la creazione dell'IoT Hub. Dimensioni di conservazione dei dati nell'IoT Hub dipendono dal livello dell'hub IoT e tipo di unità. In termini di dimensioni, l'hub di eventi predefiniti consente di conservare i messaggi della dimensione massima dei messaggi fino a 24 ore della quota. Ad esempio, per 1 unità S1 che dell'IoT Hub fornisce spazio di archiviazione sufficiente a conservare almeno 400 KB di messaggi di 4 KB dimensioni ognuno. Se i dispositivi inviano messaggi più piccoli, si potrebbe essere conservati per più tempo (fino a 7 giorni) a seconda di quanto spazio di archiviazione viene usato. Microsoft garantisce il mantenimento dati per il periodo di conservazione specificato come minimo.

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud.

Se si usa [routing dei messaggi](iot-hub-devguide-messages-d2c.md) e il [route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route) è abilitata, tutti i messaggi che non corrispondono a una query su qualsiasi route passano all'endpoint predefinito. Se si disabilita questa route di fallback, i messaggi che non corrispondono a tutte le query vengono eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource) oppure con il [portale di Azure](https://portal.azure.com).

L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

## <a name="read-from-the-built-in-endpoint"></a>Eseguire la lettura dall'endpoint predefinito

Alcune integrazioni dei prodotti e SDK di hub eventi sono a conoscenza dell'IoT Hub e consentono di usare la stringa di connessione del servizio dell'hub IoT per la connessione all'endpoint predefinito.

Quando si usa il SDK di hub eventi o le integrazioni dei prodotti che non sono consapevoli dell'IoT Hub, è necessario un endpoint compatibile con Hub eventi e un nome compatibili con Hub eventi. È possibile recuperare questi valori dal portale come indicato di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Fare clic su **Endpoint predefiniti**.

3. Il **eventi** sezione contiene i valori seguenti: **Le partizioni**, **nome compatibile con Hub eventi**, **endpoint compatibile con Hub eventi**, **tempo di conservazione**, e **gruppidiConsumer**.

    ![Impostazioni da dispositivo a cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Nel portale, il campo dell'endpoint compatibile con Hub eventi contiene una stringa di connessione hub eventi completa che è simile a: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se il SDK in uso richiede altri valori, quindi sarebbero:

| NOME | Valore |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome host | abcd1234namespace.servicebus.windows.net |
| Spazio dei nomi | abcd1234namespace |

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

il SDK da che usare per connettersi all'endpoint predefinito compatibile con Hub eventi esposti dall'IoT Hub includono:

| Linguaggio | SDK | Esempio | Note |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Guida introduttiva](quickstart-send-telemetry-dotnet.md) | Usa informazioni compatibile con hub eventi |
 Java | https://github.com/Azure/azure-event-hubs-java | [Guida introduttiva](quickstart-send-telemetry-java.md) | Usa informazioni compatibile con hub eventi |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Guida introduttiva](quickstart-send-telemetry-node.md) | Usa stringa di connessione dell'IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa stringa di connessione dell'IoT Hub |

Di seguito sono riportate le integrazioni del prodotto che è possibile usare con l'endpoint predefinito compatibile con Hub eventi esposti dall'IoT Hub:

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Visualizzare [l'elaborazione dei dati dall'IoT Hub con funzioni di Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/). Visualizzare [Stream dei dati come input in Stream Analitica](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Visualizzare [aggiungere un'origine evento di hub IoT all'ambiente Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout di Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Le [guide introduttive](quickstart-send-telemetry-node.md) illustrano come inviare messaggi da dispositivo a cloud da dispositivi simulati e come leggere i messaggi dall'endpoint integrato. 

Per altri dettagli, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route](tutorial-routing.md).

* Per instradare i messaggi da dispositivo a cloud verso endpoint personalizzati, vedere [Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud](iot-hub-devguide-messages-read-custom.md).
