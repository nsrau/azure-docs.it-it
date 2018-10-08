---
title: Conoscere gli endpoint personalizzati di Hub IoTdi Azure | Microsoft Docs
description: 'Guida per sviluppatori: uso delle query di routing per instradare i messaggi da dispositivo a cloud per gli endpoint personalizzati.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956962"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il [Routing dei messaggi](iot-hub-devguide-routing-query-syntax.md) di hub IoT consente agli utenti di instradare i messaggi da dispositivo a cloud per gli endpoint del servizio. Il routing fornisce anche una funzionalità di query per filtrare i dati prima di indirizzarli agli endpoint. Ogni query di routing configurata ha le proprietà seguenti:

| Proprietà      | DESCRIZIONE |
| ------------- | ----------- |
| **Nome**      | Il nome univoco che identifica la query. |
| **Origine**    | L'origine del flusso dati su cui intervenire. Ad esempio, i dati di telemetria del dispositivo. |
| **Condition** | L'espressione di query per la query di routing che si oppone alle proprietà del messaggio dell'applicazione, alle proprietà di sistema, al corpo del messaggio, ai tag del dispositivo gemello e alle proprietà del dispositivo gemello per determinare se costituisce una corrispondenza per l'endpoint. Per altre informazioni sulla creazione di una query, vedere la [sintassi della query di routing del messaggio](iot-hub-devguide-routing-query-syntax.md) |
| **Endpoint**  | Il nome dell'endpoint in cui l'hub IoT invia i messaggi corrispondenti alla query. È consigliabile scegliere un endpoint nella stessa area dell'hub IoT. |

Un singolo messaggio può corrispondere alla condizione di più query di routing. In questo caso, l'hub IoT invia il messaggio all'endpoint associato a ciascuna query associata. L'hub IoT deduplica automaticamente anche la consegna dei messaggi, quindi se un messaggio soddisfa più query con la stessa destinazione, viene scritto solo una volta in quella destinazione.

## <a name="endpoints-and-routing"></a>Endpoint e routing

Un hub IoT dispone di un [endpoint predefinito][lnk-built-in]. È possibile creare endpoint personalizzati per indirizzare i messaggi collegando all'hub gli altri servizi nella sottoscrizione. L'hub IoT supporta attualmente i contenitori di Archiviazione di Azure, l'Hub eventi, le code e gli argomenti del bus di servizio come endpoint personalizzati.

Quando si usa il routing e gli endpoint personalizzati, i messaggi sono inviati solo all'endpoint predefinito se non corrispondono ad alcuna query. Per recapitare i messaggi all'endpoint predefinito e anche a un endpoint personalizzato, aggiungere una route per l'invio dei messaggi all'endpoint **events**.

> [!NOTE]
> L'hub IoT supporta solo la scrittura dei dati nei contenitori di Archiviazione di Azure come BLOB.

> [!WARNING]
> Le code del bus di servizio e gli argomenti che hanno abilitato con **Sessioni** o **Rilevamento duplicati** non sono supportati come endpoint personalizzati.

Per altre informazioni sulla creazione di endpoint personalizzati nell'hub IoT, vedere [Endpoint dell'hub IoT][lnk-devguide-endpoints].

Per altre informazioni sulla lettura da endpoint personalizzati, vedere:

* Lettura dai [contenitori di Archiviazione di Azure][lnk-getstarted-storage].
* Lettura da [hub eventi][lnk-getstarted-eh].
* Lettura dalle [code del bus di servizio][lnk-getstarted-queue].
* Lettura dagli [argomenti del bus di servizio][lnk-getstarted-topic].

### <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint dell'hub IoT][lnk-devguide-endpoints].
* Per altre informazioni sul linguaggio di query usato per definire le query di routing, vedere[sintassi della query del messaggio di routing](iot-hub-devguide-routing-query-syntax.md).
* L'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando le route][lnk-d2c-tutorial] illustra come usare le query di routing e gli endpoint personalizzati.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
