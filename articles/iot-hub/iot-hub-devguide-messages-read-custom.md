---
title: Conoscere gli endpoint personalizzati di Hub IoTdi Azure | Microsoft Docs
description: 'Guida per gli sviluppatori: uso delle regole di routing per instradare i messaggi da dispositivo a cloud per gli endpoint personalizzati.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: dobett
ms.openlocfilehash: d1e22a4378caf69d2077d79f78682c4d438dbcd2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Usare endpoint personalizzati e il routing dei messaggi per i messaggi da dispositivo a cloud

L'hub IoT consente di eseguire il routing dei [messaggi da dispositivo a cloud][lnk-device-to-cloud] agli endpoint per il servizio dellhub IoT in base alle proprietà dei messaggi. Le regole di routing offrono la flessibilità necessaria per inviare messaggi alle destinazioni desiderate, senza dover attivare altri servizi per elaborare i messaggi o scrivere codice aggiuntivo. Ogni regola di routing configurata ha le proprietà seguenti:

| Proprietà      | Descrizione |
| ------------- | ----------- |
| **Nome**      | Il nome univoco che identifica la regola. |
| **Origine**    | L'origine del flusso dati su cui intervenire. Ad esempio, i dati di telemetria del dispositivo. |
| **Condition** | L'espressione di query per la regola di routing che viene eseguita rispetto alle intestazione e al corpo del messaggio e usata per determinare se si tratti di una corrispondenza per l'endpoint. Per altre informazioni sulla creazione di una condizione di route, vedere il [Riferimento: linguaggio query per dispositivi gemelli e processi][lnk-devguide-query-language]. |
| **Endpoint**  | Il nome dell'endpoint dove l'hub IoT invia i messaggi corrispondenti alla condizione. Gli endpoint devono trovarsi nella stessa area dell'hub IoT, in caso contrario potrebbero essere addebitati dei costi per le scritture tra aree. |

Un singolo messaggio può corrispondere alla condizione su più regole di routing. In questo caso, l'hub IoT invia il messaggio all'endpoint associato a ciascuna regola corrispondente. L'hub IoT deduplica automaticamente la consegna dei messaggi, quindi se uno di questi corrisponde a più regole che hanno tutte la stessa destinazione, verrà scritto in quella destinazione una sola volta.

Un hub IoT dispone di un [endpoint predefinito][lnk-built-in]. È possibile creare endpoint personalizzati per indirizzare i messaggi collegando all'hub gli altri servizi nella sottoscrizione. L'hub IoT supporta attualmente i contenitori di Archiviazione di Azure, l'Hub eventi, le code e gli argomenti del bus di servizio come endpoint personalizzati.

Quando si usa il routing insieme a endpoint personalizzati, i messaggi vengono recapitati solo all'endpoint predefinito se non corrispondono ad alcuna regola. Per recapitare i messaggi all'endpoint predefinito e anche a un endpoint personalizzato, aggiungere una route per l'invio dei messaggi all'endpoint **events**.

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

Per altre informazioni sugli endpoint dell'hub IoT, vedere [Endpoint dell'hub IoT][lnk-devguide-endpoints].

Per altre informazioni sul linguaggio di query che è possibile per definire le regole di routing, vedere [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing dei messaggi][lnk-devguide-query-language].

L'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route][lnk-d2c-tutorial] illustra come usare le regole di routing e gli endpoint personalizzati.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
