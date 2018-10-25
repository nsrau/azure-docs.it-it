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
ms.openlocfilehash: bbd5058be502839f83db484136d1c97bac4a3d79
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585952"
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

Un hub IoT dispone di un [endpoint predefinito](iot-hub-devguide-messages-read-builtin.md). È possibile creare endpoint personalizzati per indirizzare i messaggi collegando all'hub gli altri servizi nella sottoscrizione. L'hub IoT supporta attualmente i contenitori di Archiviazione di Azure, l'Hub eventi, le code e gli argomenti del bus di servizio come endpoint personalizzati.

Quando si usa il routing e gli endpoint personalizzati, i messaggi sono inviati solo all'endpoint predefinito se non corrispondono ad alcuna query. Per recapitare i messaggi all'endpoint predefinito e anche a un endpoint personalizzato, aggiungere una route per l'invio dei messaggi all'endpoint **events**.

> [!NOTE]
> * L'hub IoT supporta solo la scrittura dei dati nei contenitori di Archiviazione di Azure come BLOB.
> * Le code del bus di servizio e gli argomenti che hanno abilitato con **Sessioni** o **Rilevamento duplicati** non sono supportati come endpoint personalizzati.

Per altre informazioni sulla creazione di endpoint personalizzati nell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

Per altre informazioni sulla lettura da endpoint personalizzati, vedere:

* Lettura dai [contenitori di Archiviazione di Azure](../storage/blobs/storage-blobs-introduction.md).

* Lettura da [Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Lettura dalle [code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Lettura dagli [argomenti del bus di servizio](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint hub IoT](iot-hub-devguide-endpoints.md).

* Per altre informazioni sul linguaggio di query usato per definire le query di routing, vedere[sintassi della query del messaggio di routing](iot-hub-devguide-routing-query-syntax.md).

* L'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando le route](tutorial-routing.md) illustra come usare le query di routing e gli endpoint personalizzati.