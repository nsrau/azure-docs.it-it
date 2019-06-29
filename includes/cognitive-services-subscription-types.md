---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461495"
---
## <a name="azure-cognitive-service-subscription-types"></a>Tipi di sottoscrizione di Azure di servizi cognitivi

> [!NOTE]
> I proprietari delle sottoscrizioni possono disabilitare la creazione di account Servizi cognitivi per gruppi di risorse o sottoscrizioni tramite i [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assegnando una definizione di criteri "Tipi di risorse non consentiti" e specificando **Microsoft.CognitiveServices/accounts** come tipo di risorsa di destinazione.

Servizi cognitivi di Azure è possibile accedere tramite due diverse sottoscrizioni: Una sottoscrizione di multi-service o un singolo servizio uno. Queste sottoscrizioni consentono di connettersi a un singolo servizio o più servizi in una sola volta.

### <a name="multi-service-subscription"></a>Sottoscrizione multiservizio

>[!WARNING]
> Attualmente questi servizi **non** supportano le chiavi multiservizio: QnA Maker, servizi di riconoscimento vocale, visione artificiale personalizzato e rilevatore di anomalie.

Una sottoscrizione multiservizio per servizi cognitivi di Azure ti permette di usare una singola sottoscrizione e risorse di Azure per la maggior parte dei servizi cognitivi di Azure e consente di consolidare la fatturazione di servizi usati. Per altre informazioni, vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-subscription"></a>Sottoscrizione di un singolo servizio

Una sottoscrizione a un singolo servizio, ad esempio visione artificiale o i servizi di riconoscimento vocale. Una sottoscrizione di un singolo servizio è limitata alla risorsa specifica. 
