---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274592"
---
## <a name="azure-cognitive-service-resource-types"></a>Tipi di risorse del servizio cognitivo di Azure

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
È possibile accedere ai servizi cognitivi di Azure tramite due risorse diverse: Una risorsa multiservizio o un singolo servizio. Queste sottoscrizioni consentono di connettersi contemporaneamente a un singolo servizio cognitivo o a più servizi cognitivi.

### <a name="multi-service-resource"></a>Risorsa multiservizio

Sottoscrizione di una risorsa Servizi cognitivi multiservizio:
* Consente di usare una singola risorsa di Azure per la maggior parte dei servizi cognitivi di Azure.
* Si ottiene una singola chiave che può essere usata con più servizi cognitivi di Azure.
* Consolida la fatturazione dai servizi che usi. Per altre informazioni, vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> Attualmente questi servizi **non** supportano le chiavi multiservizio: QnA Maker, servizi vocali, Visione personalizzata e rilevamento anomalie.

### <a name="single-service-resource"></a>Risorsa a servizio singolo

Sottoscrizione di una risorsa Servizi cognitivi a servizio singolo:
* Consente di usare un servizio cognitivo specifico per il quale si crea la risorsa (ad esempio Visione artificiale o servizi vocali).
* Si ottiene una chiave specifica per il servizio cognitivo per il quale si crea una risorsa.