---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329580"
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
| Numero di query DNS che una macchina virtuale può inviare al resolver DNS di Azure al secondo |1000 <sup>1</sup> |
| Numero massimo di query DNS in coda (risposta in sospeso) per ogni macchina virtuale |200 <sup>1</sup> |

<sup>1</sup>Questi limiti vengono applicati a ogni singola macchina virtuale e non a livello di rete virtuale. Le query DNS che superano questi limiti vengono eliminate.
