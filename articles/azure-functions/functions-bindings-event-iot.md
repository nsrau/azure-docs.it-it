---
title: Azure IoT Hub bindings for Azure Functions
description: Informazioni su come usare il trigger e l'associazione dell'hub IoT in Funzioni di Azure.Learn to use IoT Hub trigger and binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586131"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub bindings for Azure Functions

Questo set di articoli illustra come usare le associazioni di Funzioni di Azure per l'hub IoT.This set of articles explains how to work with Azure Functions bindings for IoT Hub. Il supporto dell'hub IoT è basato sull'associazione hub eventi di [Azure.](functions-bindings-event-hubs.md)

> [!IMPORTANT]
> Mentre gli esempi di codice seguenti usano l'API Hub eventi, la sintassi specificata è applicabile per le funzioni Dell'hub IoT.While the following code samples use the Event Hub API, the given syntax is applicable for IoT Hub functions.

| Azione | Type |
|--------|------|
| Rispondere agli eventi inviati a un flusso di eventi dell'hub IoT.Respond to events sent to an IoT hub event stream. | [Grilletto](./functions-bindings-event-iot-trigger.md) |
| Scrivere eventi in un flusso di eventi IoTWrite events to an IoT event stream | [Associazione di output](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Rispondere agli eventi inviati a un flusso di eventi dell'hub eventi (Trigger)Respond to events sent to an event hub event event stream (Trigger)](./functions-bindings-event-iot-trigger.md)
- [Scrivere eventi in un flusso di eventi (associazione di output)Write events to an event stream (Output binding)](./functions-bindings-event-iot-output.md)
