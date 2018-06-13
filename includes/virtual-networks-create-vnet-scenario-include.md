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
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805056"
---
## <a name="scenario"></a>Scenario

Per illustrare come creare una rete virtuale e una subnet, in questo documento verrà usato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In questo scenario verrà creata una rete virtuale denominata **TestVNet** con blocco CIDR riservato **192.168.0.0./16**. La rete virtuale contiene le subnet seguenti: 

* **FrontEnd**, che usa **192.168.1.0/24** come blocco CIDR.
* **BackEnd**, che usa **192.168.2.0/24** come blocco CIDR.

