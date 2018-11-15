---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572299"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Gruppi di contenitori per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Numero di contenitori per gruppo di contenitori | 60 |
| Numero di volumi per gruppo di contenitori | 20 |
| Porte per IP | 5 |
| Creazioni di contenitori ogni ora |300<sup>1</sup> |
| Creazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |
| Eliminazioni di contenitori ogni ora | 300<sup>1</sup> |
| Eliminazioni di contenitori ogni 5 minuti | 100<sup>1</sup> |
| Più contenitori per gruppo di contenitori | Solo Linux<sup>2</sup> |
| Volumi di File di Azure | Solo Linux<sup>2</sup> |
| Volumi GitRepo | Solo Linux<sup>2</sup> |
| Volumi segreti | Solo Linux<sup>2</sup> |

<sup>1</sup> Creare una [richiesta di supporto di Azure][azure-support] per richiedere un aumento del limite.<br />
<sup>2</sup> È previsto il supporto Windows per questa funzionalità.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
