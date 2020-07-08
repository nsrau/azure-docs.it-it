---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85839037"
---
| Risorsa | Limite |
| --- | --- |
| [Ruoli Web o di lavoro per distribuzione](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Endpoint di input dell'istanza](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribuzione |25 |
| [Endpoint di input](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribuzione |25 |
| [Endpoint interni](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribuzione |25 |
| [Certificati di servizi ospitati](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribuzione |199 |

<sup>1</sup> Ogni servizio cloud di Azure con ruoli Web o di lavoro può avere due distribuzioni, una per la produzione e una per la gestione temporanea. Questo limite indica il numero di ruoli distinti, ovvero la configurazione. Questo limite non si riferisce al numero di istanze per ruolo, ovvero il ridimensionamento.

