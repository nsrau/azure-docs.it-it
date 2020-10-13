---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906719"
---


| Funzionalità |[Piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Piano dedicato](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrizioni IP in ingresso e accesso al sito privato](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Sì|✅Sì|✅Sì|✅Sì|✅Sì|
|[Integrazione della rete virtuale](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Sì (Regionale)|✅Sì (Regional e Gateway)|✅Sì| ✅Sì|
|[Trigger della rete virtuale (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Sì |✅Sì|✅Sì|✅Sì|
|[Connessioni ibride](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (solo Windows)|❌No|✅Sì|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Sì|✅Sì|✅Sì|✅Sì|