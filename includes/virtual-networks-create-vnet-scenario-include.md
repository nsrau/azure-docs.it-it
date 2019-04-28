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
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597016"
---
## <a name="scenario"></a>Scenario

Per illustrare come creare una rete virtuale e una subnet, in questo documento verrà usato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In questo scenario verrà creata una rete virtuale denominata **TestVNet** con blocco CIDR riservato **192.168.0.0./16**. La rete virtuale contiene le subnet seguenti: 

* **FrontEnd**, che usa **192.168.1.0/24** come blocco CIDR.
* **BackEnd**, che usa **192.168.2.0/24** come blocco CIDR.