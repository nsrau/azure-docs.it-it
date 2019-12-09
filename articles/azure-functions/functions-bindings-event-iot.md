---
title: Binding dell'hub Azure per funzioni di Azure
description: Informazioni su come usare le associazioni dell'hub Internet in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 2940f9e2adff82c100ed347431e8c1d27c30202e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924436"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Binding dell'hub Azure per funzioni di Azure

Questo articolo illustra come usare le associazioni di funzioni di Azure per l'hub Internet. Il supporto per hub eventi è basato sull' [associazione di hub eventi di Azure](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Per le funzioni di Azure versione 1. x, le associazioni dell'hub Internet sono disponibili nel pacchetto NuGet [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) versione 2. x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages-Functions 2. x e versioni successive

Per le funzioni 2. x e versioni successive, usare il pacchetto [Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , versione 3. x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Sebbene gli esempi di codice seguenti usino l'API dell'hub eventi, la sintassi specificata è applicabile alle funzioni dell'hub Internet.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
