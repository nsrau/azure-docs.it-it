---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384829"
---
| Risorsa | Limite |
| --- | :--- |
| Gruppi di contenitori dello SKU standard per area e per sottoscrizione | 100<sup>1</sup> |
| Gruppi di contenitori dello SKU dedicato per area e per sottoscrizione | 0<sup>1</sup> |
| Numero di contenitori per gruppo di contenitori | 60 |
| Numero di volumi per gruppo di contenitori | 20 |
| Core (CPU) dello SKU standard per area e per sottoscrizione | 10<sup>1,2</sup> | 
| Core (CPU) dello SKU standard per GPU K80, per area e per sottoscrizione | 18<sup>1,2</sup> |
| Core (CPU) dello SKU standard per GPU P100 o V100, per area e per sottoscrizione | 0<sup>1,2</sup> |
| Porte per IP | 5 |
| Dimensioni del log dell'istanza di contenitore - istanza in esecuzione | 4 MB |
| Dimensioni del log dell'istanza di contenitore - istanza arrestata | 16 KB o 1000 righe |
| Creazioni di contenitori ogni ora |300<sup>1</sup> |
| Creazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |
| Eliminazioni di contenitori ogni ora | 300<sup>1</sup> |
| Eliminazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |


<sup>1</sup>Per richiedere un aumento del limite, creare una [richiesta di supporto di Azure][azure-support]. Le sottoscrizioni gratuite, tra cui [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) e [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/), non sono idonee per gli aumenti di limite o quota. Se si ha una sottoscrizione gratuita, è possibile [eseguire l'aggiornamento](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) a una sottoscrizione con pagamento in base al consumo.<br />
<sup>2</sup>Limite predefinito per la sottoscrizione [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Il limite può essere diverso per altri tipi di categoria.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
