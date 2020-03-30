---
title: Azure IoT Hub bindings for Azure Functions
description: Informazioni su come rispondere agli eventi inviati a un flusso di eventi dell'hub IoT in Funzioni di Azure.Learn to respond to events sent to an IoT hub event stream in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589727"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger for Azure Functions

Questo articolo illustra come usare le associazioni di Funzioni di Azure per l'hub IoT.This article explains how to work to work with Azure Functions bindings for IoT Hub. Il supporto dell'hub IoT è basato sull'associazione hub eventi di [Azure.](functions-bindings-event-hubs.md)

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Mentre gli esempi di codice seguenti usano l'API Hub eventi, la sintassi specificata è applicabile per le funzioni Dell'hub IoT.While the following code samples use the Event Hub API, the given syntax is applicable for IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere eventi in un flusso di eventi (associazione di output)Write events to an event stream (Output binding)](./functions-bindings-event-iot-output.md)
