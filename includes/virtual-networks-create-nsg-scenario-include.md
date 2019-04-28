---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309964"
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
