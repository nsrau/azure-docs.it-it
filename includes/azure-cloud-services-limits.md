---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67180521"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| [Ruoli Web o di lavoro per distribuzione](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup> |25 |25 |
| [Endpoint di input dell'istanza](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribuzione |25 |25 |
| [Endpoint di input](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribuzione |25 |25 |
| [Endpoint interni](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribuzione |25 |25 |
| [Certificati di servizi ospitati](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribuzione |199 |199 |

<sup>1</sup> Ogni servizio cloud di Azure con ruoli Web o di lavoro può avere due distribuzioni, una per la produzione e una per la gestione temporanea. Questo limite indica il numero di ruoli distinti, ovvero la configurazione. Questo limite non si riferisce al numero di istanze per ruolo, ovvero il ridimensionamento.

