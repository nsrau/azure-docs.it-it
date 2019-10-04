---
title: Informazioni sull'endpoint predefinito dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: illustra come usare l'endpoint predefinito compatibile con l'hub eventi per la lettura dei messaggi da dispositivo a cloud."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: e7b8f8a33b741a8dcf2d1a68ae3cf86d6e3687eb
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950417"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leggere messaggi da dispositivo a cloud dall'endpoint predefinito

Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messages/events**) compatibile con l'[hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/). Questo endpoint è attualmente esposto solo con il protocollo [AMQP](https://www.amqp.org/) sulla porta 5671. Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

| Proprietà            | Descrizione |
| ------------------- | ----------- |
| **Numero di partizioni** | Impostare questa proprietà in fase di creazione per definire il numero di [partizioni](../event-hubs/event-hubs-features.md#partitions) per l'inserimento di eventi da dispositivo a cloud. |
| **Tempo di conservazione**  | Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni. |

L'hub Internet delle cose consente la conservazione dei dati nell'hub eventi predefinito per un massimo di 7 giorni. È possibile impostare il periodo di conservazione durante la creazione dell'hub Internet. Il tempo di conservazione dei dati nell'hub di tutto dipende dal livello e dal tipo di unità dell'hub Internet. In termini di dimensioni, gli hub eventi predefiniti possono conservare i messaggi della dimensione massima dei messaggi fino ad almeno 24 ore di quota. Ad esempio, per 1 unità di misura S1 l'hub è disponibile spazio di archiviazione sufficiente per conservare almeno 400.000 messaggi di dimensioni 4K. Se i dispositivi inviano messaggi più piccoli, possono essere conservati per un periodo più lungo (fino a 7 giorni), a seconda della quantità di spazio di archiviazione utilizzata. Garantiamo la conservazione dei dati per il periodo di memorizzazione specificato come minimo.

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud. È possibile avere fino a 20 gruppi di consumer per ogni hub Internet.

Se si usa il [routing dei messaggi](iot-hub-devguide-messages-d2c.md) e la [Route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route) è abilitata, tutti i messaggi che non corrispondono a una query su qualsiasi route passano all'endpoint predefinito. Se si disabilita questa route di fallback, i messaggi che non corrispondono ad alcuna query vengono eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource) oppure con il [portale di Azure](https://portal.azure.com).

L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

## <a name="read-from-the-built-in-endpoint"></a>Eseguire la lettura dall'endpoint predefinito

Alcune integrazioni dei prodotti e gli SDK di hub eventi sono consapevoli dell'hub Internet e consentono di usare la stringa di connessione del servizio hub Internet per connettersi all'endpoint predefinito.

Quando si usano gli SDK di hub eventi o le integrazioni dei prodotti che non sono a conoscenza dell'hub Internet, sono necessari un endpoint compatibile con hub eventi e un nome compatibile con hub eventi. È possibile recuperare questi valori dal portale come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Fare clic su **Endpoint predefiniti**.

3. La sezione **Events** contiene i valori seguenti: **Partizioni**, **nome compatibile con l'hub eventi**, **endpoint compatibile con hub eventi**, **tempo di conservazione**e **gruppi**di consumer.

    ![Impostazioni da dispositivo a cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Nel portale il campo endpoint compatibile con hub eventi contiene una stringa di connessione di hub eventi completa simile alla seguente: **Endpoint = SB://abcd1234namespace.ServiceBus.Windows.NET/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-ehub-ABCD-1234-123456**. Se per l'SDK in uso sono necessari altri valori, saranno:

| Name | Value |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome host | abcd1234namespace.servicebus.windows.net |
| Spazio dei nomi | abcd1234namespace |

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Gli SDK che è possibile usare per connettersi all'endpoint compatibile con hub eventi predefinito che espone l'hub Internet includono:

| Linguaggio | SDK | Esempio | Note |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Guida introduttiva](quickstart-send-telemetry-dotnet.md) | Usa le informazioni compatibili con hub eventi |
 Java | https://github.com/Azure/azure-event-hubs-java | [Guida introduttiva](quickstart-send-telemetry-java.md) | Usa le informazioni compatibili con hub eventi |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Guida introduttiva](quickstart-send-telemetry-node.md) | Usa la stringa di connessione dell'hub Internet |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa la stringa di connessione dell'hub Internet |

Le integrazioni di prodotti che è possibile usare con l'endpoint compatibile con l'hub eventi che espone l'hub degli eventi includono:

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Vedere [elaborazione dei dati dall'hub Internet con funzioni di Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/). Vedere [trasmettere dati come input in analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Vedere [aggiungere un'origine evento dell'hub Internet all'ambiente Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout di Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Le [guide introduttive](quickstart-send-telemetry-node.md) illustrano come inviare messaggi da dispositivo a cloud da dispositivi simulati e come leggere i messaggi dall'endpoint integrato. 

Per altri dettagli, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route](tutorial-routing.md).

* Per instradare i messaggi da dispositivo a cloud verso endpoint personalizzati, vedere [Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud](iot-hub-devguide-messages-read-custom.md).
