---
title: Informazioni sull'endpoint predefinito dell'hub IoT di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: illustra come usare l'endpoint predefinito compatibile con l'hub eventi per la lettura dei messaggi da dispositivo a cloud."
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 02ea4b94f8d1442360bebb36fdbba13d973f8555
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242416"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leggere messaggi da dispositivo a cloud dall'endpoint predefinito

Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messages/events**) compatibile con l'[hub eventi](https://azure.microsoft.com/documentation/services/event-hubs/
). Questo endpoint è attualmente esposto solo con il protocollo [AMQP](https://www.amqp.org/) sulla porta 5671. Un hub IoT espone le proprietà seguenti per consentire il controllo dell'endpoint di messaggistica predefinito **messages/events**, compatibile con Hub eventi.

| Proprietà            | DESCRIZIONE |
| ------------------- | ----------- |
| **Numero di partizioni** | Impostare questa proprietà in fase di creazione per definire il numero di [partizioni](../event-hubs/event-hubs-features.md#partitions) per l'inserimento di eventi da dispositivo a cloud. |
| **Tempo di conservazione**  | Questa proprietà specifica per quanti giorni i messaggi vengono conservati dall'hub IoT. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni. |

L'hub IoT consente inoltre di gestire i gruppi di consumer nell'endpoint di ricezione predefinito da dispositivo a cloud.

Se si usa il [routing dei messaggi](iot-hub-devguide-messages-d2c.md) e la [route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route) è abilitata, tutti i messaggi che non corrispondono a una query vengono scritti nell'endpoint predefinito. Se si disattiva questa route di fallback, i messaggi che non corrispondono a una query verranno eliminati.

È possibile modificare il tempo di conservazione a livello di codice con le [API REST del provider di risorse dell'hub IoT](/rest/api/iothub/iothubresource) oppure con il [portale di Azure](https://portal.azure.com).

L'hub IoT espone l'endpoint **messaggi/eventi** predefinito per permettere ai servizi back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. L'endpoint è compatibile con Hub eventi e consente quindi di usare uno dei meccanismi supportati da tale servizio per la lettura dei messaggi.

## <a name="read-from-the-built-in-endpoint"></a>Eseguire la lettura dall'endpoint predefinito

Quando si usa [Azure Service Bus SDK per .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o l'[host processore di eventi dell'hub eventi di Azure](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), è possibile usare qualsiasi stringa di connessione dell'hub IoT con le autorizzazioni corrette. Usare quindi **messaggi/eventi** come nome dell'hub eventi.

Quando si usano SDK o integrazioni del prodotto non compatibili con l'hub IoT, è necessario recuperare un endpoint e un nome compatibili con l'hub eventi:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Fare clic su **Endpoint predefiniti**.

3. La sezione **Eventi** contiene i valori seguenti: **Endpoint compatibile con l'hub eventi**, **Nome compatibile con l'hub eventi**, **Partizioni**, **Tempo di conservazione** e **Gruppi di consumer**.

    ![Impostazioni da dispositivo a cloud](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

L'SDK dell'hub IoT richiede il nome dell'endpoint dell'hub IoT, ossia **messagi/eventi** come mostrato in **Endpoint**.

Se l'SDK usato richiede un valore **Nome host** o **Spazio dei nomi**, rimuovere lo schema da **Endpoint compatibile con l'hub eventi**. Ad esempio, se l'endpoint compatibile con l'hub eventi è **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, il **Nome host** sarà **iothub-ns-myiothub-1234.servicebus.windows.net**. Lo **spazio dei nomi** sarà **iothub-ns-myiothub-1234**.

È quindi possibile usare qualsiasi criterio di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Se si deve compilare una stringa di connessione dell'hub eventi con le informazioni precedenti, è possibile usare il modello seguente:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Di seguito sono elencati gli SDK e le integrazioni che è possibile usare con gli endpoint compatibili con l'hub eventi esposti dall'hub IoT:

* [Client Java di Hub eventi](https://github.com/Azure/azure-event-hubs-java).
* [Spout di Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Le [guide introduttive](quickstart-send-telemetry-node.md) illustrano come inviare messaggi da dispositivo a cloud da dispositivi simulati e come leggere i messaggi dall'endpoint integrato. 

Per altri dettagli, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route](tutorial-routing.md).

* Per instradare i messaggi da dispositivo a cloud verso endpoint personalizzati, vedere [Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud](iot-hub-devguide-messages-read-custom.md).