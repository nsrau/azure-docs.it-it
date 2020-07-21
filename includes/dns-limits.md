---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 0f7187300ec96ce417866c4fb8fa02783c1da63a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515876"
---
**Zone DNS pubbliche**

| Risorsa | Limite |
| --- | --- |
| Zone DNS pubblico per sottoscrizione |250 <sup>1</sup> |
| Set di record per zona DNS pubblica |10.000 <sup>1</sup> |
| Record per set di record nella zona DNS pubblica |20 |
| Numero di record alias per una singola risorsa di Azure |20|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto di Azure.

**Zone DNS privato**

| Risorsa | Limite |
| --- | --- |
| Zone DNS privato per sottoscrizione |1000|
| Set di record per zona DNS privata |25000|
| Record per set di record per le zone DNS private |20|
| Collegamenti di rete virtuale per zona DNS privata |1000|
| Collegamenti alle reti virtuali per zone DNS private con registrazione automatica abilitata |100|
| Numero di zone DNS private a cui può essere collegata una rete virtuale con registrazione automatica abilitata |1|
| Numero di zone DNS private che possono essere collegate a una rete virtuale |1000|
| Numero di query DNS che una macchina virtuale può inviare al resolver DNS di Azure al secondo |500 <sup>1</sup> |
| Numero massimo di query DNS in coda (risposta in sospeso) per macchina virtuale |200 <sup>1</sup> |

<sup>1</sup> Questi limiti vengono applicati a ogni singola macchina virtuale e non a livello di rete virtuale. Le query DNS che superano questi limiti vengono eliminate.
