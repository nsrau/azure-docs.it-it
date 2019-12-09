---
title: Associazioni di Hub eventi di Azure per Funzioni di Azure
description: Informazioni su come usare le associazioni di Hub eventi di Azure in Funzioni di Azure.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 998ba122d6582a86122bbe65ff47bad1ea24a900
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925544"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Associazioni di Hub eventi di Azure per Funzioni di Azure

Questo articolo illustra come usare le associazioni di [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per Hub eventi.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Per Funzioni di Azure versione 1.x, le associazioni di Hub eventi sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) versione 2.x.
Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages-Functions 2. x e versioni successive

Per le funzioni 2. x e versioni successive, usare il pacchetto [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , versione 3. x.
Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
