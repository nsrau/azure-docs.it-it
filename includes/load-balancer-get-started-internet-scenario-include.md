---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122181"
---
In questo scenario verranno eseguite le seguenti attività:

* Creare un servizio di bilanciamento del carico che riceve il traffico di rete sulla porta 80 e invia il traffico con carico bilanciato alle macchine virtuali "web1" e "web2"
* Creare regole NAT per l'accesso desktop remoto/SSH per le macchine virtuali dietro il servizio di bilanciamento del carico
* Creare probe di integrità

![Scenario del bilanciamento del carico](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
