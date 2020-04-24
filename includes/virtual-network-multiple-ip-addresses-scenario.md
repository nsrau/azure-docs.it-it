---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180069"
---
## <a name="scenario"></a>Scenario
Una macchina virtuale con una singola scheda di interfaccia di rete viene creata e collegata a una rete virtuale. La macchina virtuale richiede tre diversi indirizzi IP *privati* e due indirizzi IP *pubblici*. Gli indirizzi IP vengono assegnati alle configurazioni IP seguenti:

* **IPConfig-1:** assegna un indirizzo IP privato *statico* e un indirizzo IP pubblico *statico*.
* **IPConfig-2:** assegna un indirizzo IP privato *statico* e un indirizzo IP pubblico *statico*.
* **IPConfig-3:** assegna un indirizzo IP privato *statico* e nessun indirizzo IP pubblico.
  
    ![Più indirizzi IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Le configurazioni IP vengono associate alla scheda di interfaccia di rete al momento della creazione della stessa, mentre la scheda di interfaccia di rete viene collegata alla macchina virtuale al momento della creazione della macchina virtuale. I tipi di indirizzi IP usati per lo scenario sono a scopo illustrativo. È possibile assegnare qualsiasi tipo di assegnazione e indirizzo IP desiderato.

> [!NOTE]
> Sebbene la procedura illustrata in questo articolo assegni tutte le configurazioni IP a una singola scheda di interfaccia di rete, è possibile anche assegnare più configurazioni IP a una scheda di interfaccia di rete in una macchina virtuale con più schede di interfaccia di rete. Per informazioni su come creare una VM con più schede di rete, vedere l'articolo [creare una VM con più schede](../articles/virtual-machines/windows/multiple-nics.md) di rete.