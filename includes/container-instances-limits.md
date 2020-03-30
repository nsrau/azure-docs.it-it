---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334774"
---
| Risorsa | Limite |
| --- | :--- |
| Gruppi di contenitori sku standard per area per [sottoscrizioneStandard](../articles/billing-buy-sign-up-azure-subscription.md) sku container groups per region per subscription | 100<sup>1</sup> |
| Gruppi di contenitori sku dedicati per area per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) | 0(<sup>)</sup> |
| Numero di contenitori per gruppo di contenitori | 60 |
| Numero di volumi per gruppo di contenitori | 20 |
| Porte per IP | 5 |
| Dimensioni del log dell'istanza di contenitore - istanza in esecuzione | 4 MB |
| Dimensioni del log dell'istanza di contenitore - istanza arrestata | 16 KB o 1000 righe |
| Creazioni di contenitori ogni ora |300<sup>1 (in</sup> vie |
| Creazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |
| Eliminazioni di contenitori ogni ora | 300<sup>1 (in</sup> vie |
| Eliminazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |


<sup>1</sup> Per richiedere un aumento del limite, creare una [richiesta di supporto di Azure][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
