---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020994"
---


| Funzionalità |[Piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Piano dedicato](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrizioni IP in ingresso e accesso al sito privato](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Sì|✅Sì|✅Sì|✅Sì|✅Sì|
|[Integrazione della rete virtuale](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Sì (Regionale)|✅Sì (Regional e Gateway)|✅Sì| ✅Sì|
|[Trigger della rete virtuale (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Sì |✅Sì|✅Sì|✅Sì|
|[Connessioni ibride](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (solo Windows)|❌No|✅Sì|✅Sì|✅Sì|✅Sì|
|[Restrizioni IP in uscita](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Sì|✅Sì|✅Sì|✅Sì|