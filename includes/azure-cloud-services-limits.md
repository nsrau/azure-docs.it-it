---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85839037"
---
| Risorsa | Limite |
| --- | --- |
| [Ruoli di lavoro o Web per distribuzione](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Endpoint di input istanza](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribuzione |25 |
| [Endpoint di input](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribuzione |25 |
| [Endpoint interni](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribuzione |25 |
| [Certificati di servizi ospitati](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribuzione |199 |

<sup>1</sup>Per ogni servizio cloud di Azure con ruoli di lavoro o Web possono esistere due distribuzioni, una per la produzione e l'altra per la gestione temporanea. Questo limite si riferisce al numero di ruoli distinti, ovvero alla configurazione, e non al numero di istanze per ruolo, ovvero al ridimensionamento.

