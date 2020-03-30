---
title: Azure IoT Hub output binding for Azure Functions
description: Informazioni su come scrivere messaggi nei flussi di Hub IoT di Azure usando Funzioni di Azure.Learn to write messages to Azure IoT Hubs streams using Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277427"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure IoT Hub output binding for Azure Functions

Questo articolo illustra come usare le associazioni di output di Funzioni di Azure per l'hub IoT.This article explains how to work with Azure Functions output bindings for IoT Hub. Il supporto dell'hub IoT è basato sull'associazione hub eventi di [Azure.](functions-bindings-event-hubs.md)

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Mentre gli esempi di codice seguenti usano l'API Hub eventi, la sintassi specificata è applicabile per le funzioni Dell'hub IoT.While the following code samples use the Event Hub API, the given syntax is applicable for IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Rispondere agli eventi inviati a un flusso di eventi dell'hub eventi (Trigger)Respond to events sent to an event hub event event stream (Trigger)](./functions-bindings-event-iot-trigger.md)
