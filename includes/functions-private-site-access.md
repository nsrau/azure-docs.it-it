---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578815"
---
L'[endpoint privato di Azure](../articles/private-link/private-endpoint-overview.md) è un'interfaccia di rete che si connette in modo privato e sicuro a un servizio basato sul collegamento privato di Azure.  L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale in modo efficace.

È possibile usare l'endpoint privato per le funzioni ospitate nei piani [Premium](../articles/azure-functions/functions-premium-plan.md) e di [servizio app](../articles/azure-functions/functions-scale.md#app-service-plan) .

Quando si crea una connessione all'endpoint privato in ingresso per le funzioni, sarà necessario anche un record DNS per risolvere l'indirizzo privato.  Per impostazione predefinita, viene creato un record DNS privato quando si crea un endpoint privato usando il portale di Azure.

Per altre informazioni, vedere [uso di endpoint privati per le app Web](../articles/app-service/networking/private-endpoint.md).