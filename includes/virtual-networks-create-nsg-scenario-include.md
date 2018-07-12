---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38760361"
---
## <a name="scenario"></a>Scenario
Per illustrare meglio come creare un gruppo di sicurezza di rete, in questo documento verrà usato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In questo scenario si creerà un gruppo di sicurezza di rete per ogni subnet nella rete virtuale **TestVNet**, come illustrato di seguito: 

* **NSG-FrontEnd**. Il gruppo di sicurezza di rete front-end verrà applicato alla subnet *FrontEnd* e contiene due regole:    
  * **regola-rdp**. Consente il traffico RDP verso la subnet *FrontEnd*.
  * **regola-web**. Consente il traffico HTTP verso la subnet *FrontEnd*.
* **Back-end di NSG**. Il gruppo di sicurezza di rete back-end verrà applicato alla subnet *BackEnd* e contiene due regole:    
  * **regola sql**. Consente il traffico SQL solo dalla subnet *FrontEnd*.
  * **regola-web**. Nega tutto il traffico associato ad internet proveniente dalla subnet *BackEnd*.

La combinazione di queste regole crea uno scenario simile alla rete perimetrale, dove la subnet di back-end può solo ricevere traffico in ingresso per SQL dalla subnet front-end e non dispone dell'accesso a Internet, mentre la subnet front-end può comunicare con Internet e ricevere solo le richieste HTTP in ingresso.

