---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648836"
---
Per accesso privato s'intende la possibilità di rendere l'app accessibile soltanto da una rete privata, ad esempio da una rete virtuale di Azure.

* L'accesso privato è disponibile nei piani [Premium](../articles/azure-functions/functions-premium-plan.md), [A consumo](../articles/azure-functions/functions-scale.md#consumption-plan) e [Servizio app](../articles/azure-functions/functions-scale.md#app-service-plan) quando vengono configurati gli endpoint di servizio.
    * Gli endpoint di servizio possono essere configurati in base alle singole app in **Funzionalità della piattaforma** > **Rete** > **Configura restrizioni di accesso** > **Aggiungi regola**. È ora possibile selezionare le reti virtuali come tipo di regola.
    * Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Tenere presente che, con gli endpoint di servizio, la funzione ha ancora l'accesso in uscita completo a Internet, anche con l'integrazione della rete virtuale configurata.
* L'accesso privato è disponibile anche all'interno di un ambiente del servizio app configurato con un servizio di bilanciamento del carico interno. Per altre informazioni, vedere [Creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app](../articles/app-service/environment/create-ilb-ase.md).

Per informazioni su come configurare l'accesso privato, vedere [Stabilire l'accesso privato al sito per Funzioni di Azure](../articles/azure-functions/functions-create-private-site-access.md).