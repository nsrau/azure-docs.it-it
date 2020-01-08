---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437002"
---
|  | **Peering privato** | **Peering Microsoft** |  **Peering pubblico** (deprecato per i circuiti nuovi) |
| --- | --- | --- | --- |
| **Numero massimo di prefissi supportati per peering** |4000 per impostazione predefinita, 10.000 con ExpressRoute Premium |200 |200 |
| **Intervalli di indirizzi IP supportati** |Qualsiasi indirizzo IP valido entro la rete WAN. |Indirizzi IP pubblici di proprietà dell'utente o del provider di connettività. |Indirizzi IP pubblici di proprietà dell'utente o del provider di connettività. |
| **Requisiti del numero AS** |Numeri AS pubblici e privati. È necessario possedere un numero AS pubblico se si sceglie di usarne uno. |Numeri AS pubblici e privati. È tuttavia necessario dimostrare la proprietà degli indirizzi IP pubblici. |Numeri AS pubblici e privati. È tuttavia necessario dimostrare la proprietà degli indirizzi IP pubblici. |
| **Protocolli IP supportati**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Indirizzi IP per l'interfaccia di routing** |Indirizzi IP pubblici e RFC1918 |Indirizzi IP pubblici registrati a nome dell'utente nei registri di routing. |Indirizzi IP pubblici registrati a nome dell'utente nei registri di routing. |
| **Supporto per Hash MD5** |Sì |Sì |Sì |