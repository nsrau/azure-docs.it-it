---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 0f7187300ec96ce417866c4fb8fa02783c1da63a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86515876"
---
**Zone DNS pubblico**

| Risorsa | Limite |
| --- | --- |
| Zone DNS pubblico per ogni sottoscrizione |250 <sup>1</sup> |
| Set di record per ogni zona DNS pubblico |10.000 <sup>1</sup> |
| Record per ogni set di record nella zona DNS pubblico |20 |
| Numero di record alias per singola risorsa di Azure |20|

<sup>1</sup> Se è necessario aumentare questi limiti, contattare il supporto di Azure.

**Zone DNS privato**

| Risorsa | Limite |
| --- | --- |
| Zone DNS privato per ogni sottoscrizione |1000|
| Set di record per ogni zona DNS privato |25000|
| Record per ogni set di record nella zona DNS privato |20|
| Collegamenti alla rete virtuale per ogni zona DNS privato |1000|
| Collegamenti alle reti virtuali per zone DNS privato con registrazione automatica abilitata |100|
| Numero di zone DNS privato a cui una rete virtuale può collegarsi con la registrazione automatica abilitata |1|
| Numero di zone DNS privato a cui una rete virtuale può collegarsi |1000|
| Numero di query DNS che una macchina virtuale può inviare al resolver DNS di Azure al secondo |500 <sup>1</sup> |
| Numero massimo di query DNS in coda (risposta in sospeso) per ogni macchina virtuale |200 <sup>1</sup> |

<sup>1</sup>Questi limiti vengono applicati a ogni singola macchina virtuale e non a livello di rete virtuale. Le query DNS che superano questi limiti vengono eliminate.
