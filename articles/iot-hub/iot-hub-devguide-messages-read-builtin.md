---
title: Informazioni sull'endpoint predefinito dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: illustra come usare l'endpoint predefinito compatibile con l'hub eventi per la lettura dei messaggi da dispositivo a cloud."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: amqp
ms.openlocfilehash: a9bc0564a69124618d0a7e3ff6bafb65b2be0ee4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907483"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leggere messaggi da dispositivo a cloud dall'endpoint predefinito

Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messages/events**) compatibile con l'[hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/). Questo endpoint è attualmente esposto solo con il protocollo [AMQP](https://www.amqp.org/) sulla porta 5671. Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

| Proprietà            | Descrizione |
| ------------------- | ----------- |
| **Numero di partizioni** | Impostare questa proprietà in fase di creazione per definire il numero di [partizioni](../event-hubs/event-hubs-features.md#partitions) per l'inserimento di eventi da dispositivo a cloud. |
| **Tempo di conservazione**  | Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni. |

L'hub IoT consente la conservazione dei dati nel servizio Hub eventi predefinito per un massimo di 7 giorni. È possibile impostare il periodo di conservazione durante la creazione dell'hub IoT. Il tempo di conservazione dei dati nell'hub IoT dipende dal tipo di unità e dal livello dell'hub IoT. In termini di dimensioni, il servizio Hub eventi predefinito può conservare i messaggi di dimensione massima fino ad almeno 24 ore di quota. Per un'unità S1, ad esempio, l'hub IoT fornisce spazio di archiviazione sufficiente per conservare almeno 400.000 messaggi con dimensioni di 4 K l'uno. Se i dispositivi inviano messaggi più piccoli, è possibile conservarli più a lungo (fino a 7 giorni), a seconda della quantità di spazio di archiviazione utilizzata. La conservazione dei dati viene garantita come minimo per il periodo di conservazione specificato. Trascorso tale periodo, i messaggi scadono e non sono più accessibili. 

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud. Sono consentiti al massimo 20 gruppi di consumer per ogni hub IoT.

Se si usa il [routing dei messaggi](iot-hub-devguide-messages-d2c.md) e la [route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route) è abilitata, tutti i messaggi che non corrispondono a una query in una route vengono indirizzati all'endpoint predefinito. Se si disabilita la route di fallback, i messaggi che non corrispondono a una query vengono eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource) oppure con il [portale di Azure](https://portal.azure.com).

L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

## <a name="read-from-the-built-in-endpoint"></a>Eseguire la lettura dall'endpoint predefinito

Alcune integrazioni del prodotto ed SDK di Hub eventi sono in grado di rilevare la presenza dell'hub IoT e consentono di usare la stringa di connessione del servizio hub IoT per connettersi all'endpoint predefinito.

Quando si usano SDK di Hub eventi o integrazioni del prodotto che non rilevano l'hub IoT, sono necessari un endpoint e un nome compatibili con l'hub eventi. È possibile recuperare questi valori dal portale come illustrato di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Fare clic su **Endpoint predefiniti**.

3. La sezione **Eventi** contiene i valori seguenti: **Partizioni**, **Nome compatibile con l'hub eventi**, **Endpoint compatibile con l'hub eventi**, **Tempo di conservazione** e **Gruppi di consumer**.

    ![Impostazioni da dispositivo a cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Nel portale il campo Endpoint compatibile con l'hub eventi contiene una stringa di connessione di Hub eventi completa simile alla seguente: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se per l'SDK in uso sono necessari altri valori, corrisponderanno a quanto segue:

| Nome | valore |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| nomehost | abcd1234namespace.servicebus.windows.net |
| Spazio dei nomi | abcd1234namespace |

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Gli SDK che è possibile usare per connettersi all'endpoint compatibile con l'hub eventi predefinito che l'hub IoT espone includono:

| Linguaggio | SDK | Esempio |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Guida introduttiva](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Guida introduttiva](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Guida introduttiva](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Guida introduttiva](quickstart-send-telemetry-python.md) |

Le integrazioni del prodotto che è possibile usare per connettersi all'endpoint compatibile con l'hub eventi predefinito che l'hub IoT espone includono:

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Vedere [Elaborazione dei dati dall'hub IoT con Funzioni di Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/). Vedere [Trasmettere dati come input in Analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Vedere [Aggiungere un'origine evento di un hub IoT in un ambiente Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout di Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Le [guide introduttive](quickstart-send-telemetry-node.md) illustrano come inviare messaggi da dispositivo a cloud da dispositivi simulati e come leggere i messaggi dall'endpoint integrato. 

Per altri dettagli, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route](tutorial-routing.md).

* Per instradare i messaggi da dispositivo a cloud verso endpoint personalizzati, vedere [Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud](iot-hub-devguide-messages-read-custom.md).
