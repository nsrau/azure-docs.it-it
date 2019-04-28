---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516964"
---
In questo scenario verranno eseguite le seguenti attività:

* Creare un servizio di bilanciamento del carico che riceve il traffico di rete sulla porta 80 e invia il traffico con carico bilanciato alle macchine virtuali "web1" e "web2"
* Creare regole NAT per l'accesso desktop remoto/SSH per le macchine virtuali dietro il servizio di bilanciamento del carico
* Creare probe di integrità

![Scenario del bilanciamento del carico](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)