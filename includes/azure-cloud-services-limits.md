---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238757"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| [Ruoli Web o di lavoro per la distribuzione](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Endpoint di input istanza](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per ogni distribuzione |25 |25 |
| [Endpoint di input](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per ogni distribuzione |25 |25 |
| [Gli endpoint interni](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per ogni distribuzione |25 |25 |
| [Certificati di servizi ospitati](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribuzione |199 |199 |

<sup>1</sup>ogni servizio Cloud di Azure con ruoli web o di lavoro può avere due distribuzioni, uno per la produzione e uno per la gestione temporanea. Questo limite si riferisce al numero di ruoli distinti, ovvero configurazione. Questo limite non fa riferimento al numero di istanze per ruolo, vale a dire, la scalabilità.

