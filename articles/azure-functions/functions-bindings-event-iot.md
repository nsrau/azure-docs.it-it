---
title: Binding dell'hub Azure per funzioni di Azure
description: Informazioni su come usare trigger e binding dell'hub Internet in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77586131"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Binding dell'hub Azure per funzioni di Azure

Questo set di articoli spiega come usare le associazioni di funzioni di Azure per l'hub Internet. Il supporto per hub eventi è basato sull' [associazione di hub eventi di Azure](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Sebbene gli esempi di codice seguenti usino l'API dell'hub eventi, la sintassi specificata è applicabile alle funzioni dell'hub Internet.

| Action | Type |
|--------|------|
| Rispondere agli eventi inviati a un flusso di eventi dell'hub Internet. | [Trigger](./functions-bindings-event-iot-trigger.md) |
| Scrivere eventi in un flusso di eventi di un sacco | [Binding di output](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Rispondere agli eventi inviati a un flusso di eventi di hub eventi (trigger)](./functions-bindings-event-iot-trigger.md)
- [Scrivere eventi in un flusso di eventi (associazione di output)](./functions-bindings-event-iot-output.md)
