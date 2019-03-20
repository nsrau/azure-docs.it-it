---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553594"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| [Ruoli Web o di lavoro per la distribuzione](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Endpoint di input istanza](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) per ogni distribuzione |25 |25 |
| [Endpoint di input](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) per ogni distribuzione |25 |25 |
| [Gli endpoint interni](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) per ogni distribuzione |25 |25 |
| [Certificati di servizi ospitati](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribuzione |199 |199 |

<sup>1</sup>ogni servizio Cloud di Azure con ruoli web o di lavoro può avere due distribuzioni, uno per la produzione e uno per la gestione temporanea. Questo limite si riferisce al numero di ruoli distinti, ovvero configurazione. Questo limite non fa riferimento al numero di istanze per ruolo, vale a dire, la scalabilità.

