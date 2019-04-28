---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516951"
---
Azure Load Balancer è un servizio di bilanciamento del carico di livello 4 (TCP, UDP). Il servizio di bilanciamento del carico offre disponibilità elevata distribuendo il traffico in ingresso tra istanze del servizio integre in servizi cloud o macchine virtuali in un set di bilanciamento del carico . Azure Load Balancer può anche presentare tali servizi su più porte, più indirizzi IP o entrambi.

È possibile configurare un servizio bilanciamento del carico per:

* Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali (VMs). Si fa riferimento a un servizio di bilanciamento del carico in questo scenario come [bilanciamento del carico con connessione Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Bilanciare il carico del traffico tra macchine virtuali in una rete virtuale, tra macchine virtuali nei servizi cloud o tra computer locali e macchine virtuali in una rete virtuale cross-premise. Si fa riferimento a un servizio di bilanciamento del carico in questo scenario come un [Bilanciamento del carico interno](../articles/load-balancer/load-balancer-internal-overview.md).
* Inoltrare il traffico esterno a una specifica istanza di macchina virtuale