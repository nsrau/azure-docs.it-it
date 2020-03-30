---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335069"
---
**Zone DNS pubbliche**

| Risorsa | Limite |
| --- | --- |
| Aree DNS pubbliche per sottoscrizione |250 <sup>1</sup> |
| Set di record per zona DNS pubblica |10.000 <sup>1</sup> |
| Record per set di record nella zona DNS pubblica |20 |
| Numero di record Alias per una singola risorsa di AzureNumber of Alias records for a single Azure resource |20|
| Aree DNS private per sottoscrizionePrivate DNS zones per subscription |1000|
| Set di record per zona DNS privata |25000|
| Record per set di record per le zone DNS private |20|
| Collegamenti di rete virtuale per zona DNS privata |1000|
| Collegamenti reti virtuali per zone DNS private con registrazione automatica abilitata |100|
| Numero di zone DNS private a cui una rete virtuale può essere collegata con la registrazione automatica abilitata |1|
| Numero di zone DNS private che una rete virtuale può collegare |1000|
| Numero di query DNS che una macchina virtuale può inviare al resolver DNS di Azure, al secondo |500 <sup>2</sup> |
| Numero massimo di query DNS accodate (risposta in sospeso) per macchina virtuale |200 <sup>2 (informazioni</sup> in stato di |

<sup>1 : il</sup> nome del Se è necessario aumentare questi limiti, contattare il supporto di Azure.If you need to increase these limits, contact Azure Support.

<sup>2 Il</sup> nome del sistema Questi limiti vengono applicati a ogni singola macchina virtuale e non a livello di rete virtuale. Le query DNS che superano questi limiti vengono eliminate.