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
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729979"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leggere messaggi da dispositivo a cloud dall'endpoint predefinito

Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messaggi/eventi**) compatibile con [Hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/). Questo endpoint è attualmente esposto solo con il protocollo [AMQP](https://www.amqp.org/) sulla porta 5671. Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

| Proprietà            | Descrizione |
| ------------------- | ----------- |
| **Numero di partizioni** | Impostare questa proprietà in fase di creazione per definire il numero di [partizioni](../event-hubs/event-hubs-features.md#partitions) per l'inserimento di eventi da dispositivo a cloud. |
| **Tempo di conservazione**  | Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni. |

L'hub IoT consente la conservazione dei dati negli hub eventi incorporati per un massimo di 7 giorni. È possibile impostare il tempo di conservazione durante la creazione dell'hub IoT.You can set the retention time during creation of your IoT Hub. Il tempo di conservazione dei dati nell'hub IoT dipende dal livello e dal tipo di unità dell'hub IoT.Data retention time in IoT Hub depends on your IoT hub tier and unit type. In termini di dimensioni, gli hub eventi incorporati possono mantenere i messaggi della dimensione massima dei messaggi fino ad almeno 24 ore di quota. Ad esempio, per 1 unità S1 l'hub IoT offre spazio di archiviazione sufficiente per mantenere almeno 400K messaggi di dimensioni 4k ciascuno. Se i dispositivi inviano messaggi più piccoli, possono essere conservati più a lungo (fino a 7 giorni) a seconda della quantità di spazio di archiviazione utilizzata. Garantiamo la conservazione dei dati per il tempo di conservazione specificato come minimo. I messaggi scadranno e non saranno accessibili dopo che il tempo di conservazione è trascorso. 

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud. Puoi avere fino a 20 gruppi di consumer per ogni hub IoT.You can have up to 20 consumer groups for each IoT Hub.

Se si usa il routing dei [messaggi](iot-hub-devguide-messages-d2c.md) e la route di [fallback](iot-hub-devguide-messages-d2c.md#fallback-route) è abilitata, tutti i messaggi che non corrispondono a una query in qualsiasi route vengono inviati all'endpoint predefinito. Se si disabilita questa route di fallback, i messaggi che non corrispondono ad alcuna query vengono eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource) oppure con il [portale di Azure](https://portal.azure.com).

L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

## <a name="read-from-the-built-in-endpoint"></a>Eseguire la lettura dall'endpoint predefinito

Alcune integrazioni di prodotti e SDK di Hub eventi sono a conoscenza dell'hub IoT e consentono di usare la stringa di connessione del servizio hub IoT per connettersi all'endpoint predefinito.

Quando si utilizzano SDK di Hub eventi o integrazioni di prodotti che non sono a conoscenza dell'hub IoT, sono necessari un endpoint compatibile con Hub eventi e un nome compatibile con Hub eventi. È possibile recuperare questi valori dal portale come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Fare clic su **Endpoint predefiniti**.

3. La sezione **Eventi** contiene i seguenti valori: **Partizioni**, **Nome compatibile con Hub eventi**, Endpoint **compatibile con Hub eventi**, Tempo di **conservazione**e **Gruppi consumer**.

    ![Impostazioni da dispositivo a cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Nel portale, il campo endpoint compatibile con Hub eventi contiene una stringa di connessione completa di Hub eventi simile a: **Endpoint sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName:iothubowner; SharedAccessKey, keykeykeykeykeykey; EntityPath: iothub-ehub-abcd-1234-123456**. Se l'SDK in uso richiede altri valori, sarà:

| Nome | valore |
| ---- | ----- |
| Endpoint | sb://abcd1234namespace.servicebus.windows.net/ |
| nomehost | abcd1234namespace.servicebus.windows.net |
| Spazio dei nomi | abcd1234spazio dei nomialeaaa |

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Gli SDK che è possibile usare per connettersi all'endpoint incorporato compatibile con Hub eventi includono:

| Linguaggio | SDK | Esempio | Note |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Guida introduttiva](quickstart-send-telemetry-dotnet.md) | Utilizza informazioni compatibili con Hub eventi |
 Java | https://github.com/Azure/azure-event-hubs-java | [Guida introduttiva](quickstart-send-telemetry-java.md) | Utilizza informazioni compatibili con Hub eventi |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Guida introduttiva](quickstart-send-telemetry-node.md) | Usa la stringa di connessione dell'hub IoT |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa la stringa di connessione dell'hub IoT |

Le integrazioni dei prodotti che è possibile usare con l'endpoint incorporato compatibile con Hub eventi vengono esposte dall'hub eventi includono:

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Vedere [Elaborazione dei dati dall'hub IoT con Funzioni](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)di Azure.
* [Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/)di Azure . Consultate [Eseguire il flusso dei dati come input in Analisi di flusso.](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)
* [Approfondimenti sulle serie temporali](https://docs.microsoft.com/azure/time-series-insights/). Consultate [Aggiungere un'origine eventi hub IoT all'ambiente Time Series Insights.](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)
* [Spout di Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Le [guide introduttive](quickstart-send-telemetry-node.md) illustrano come inviare messaggi da dispositivo a cloud da dispositivi simulati e come leggere i messaggi dall'endpoint integrato. 

Per altri dettagli, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route](tutorial-routing.md).

* Per instradare i messaggi da dispositivo a cloud verso endpoint personalizzati, vedere [Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud](iot-hub-devguide-messages-read-custom.md).
