---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334245"
---
## <a name="azure-cognitive-service-resource-types"></a>Tipi di risorse del servizio cognitivo di Azure

> [!NOTE]
> I proprietari delle sottoscrizioni possono disabilitare la creazione di risorse di servizi cognitivi per i gruppi di risorse e le sottoscrizioni applicando [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assegnando una definizione dei criteri di tipo "non consentiti" e specificando  **Microsoft. CognitiveServices/accounts** come tipo di risorsa di destinazione.

È possibile accedere ai servizi cognitivi di Azure tramite due risorse diverse: Una risorsa multiservizio o un singolo servizio. Queste sottoscrizioni consentono di connettersi a un singolo servizio o a più servizi contemporaneamente.

### <a name="multi-service-resource"></a>Risorsa multiservizio

>[!WARNING]
> Attualmente questi servizi **non** supportano le chiavi multiservizio: QnA Maker, servizi vocali, Visione personalizzata e rilevamento anomalie.

Sottoscrizione di una risorsa Servizi cognitivi multiservizio:
* Consente di usare una singola risorsa di Azure per la maggior parte dei servizi cognitivi di Azure.
* Consolida la fatturazione dai servizi che usi. Per altre informazioni, vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-resource"></a>Risorsa a servizio singolo

Le risorse a servizio singolo (ad esempio Visione artificiale o servizi vocali) sono limitate al servizio specificato.