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
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116844"
---
## <a name="scenario"></a>Scenario

Per illustrare come creare una rete virtuale e una subnet, in questo documento verrà usato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In questo scenario verrà creata una rete virtuale denominata **TestVNet** con blocco CIDR riservato **192.168.0.0./16**. La rete virtuale contiene le subnet seguenti: 

* **FrontEnd**, che usa **192.168.1.0/24** come blocco CIDR.
* **BackEnd**, che usa **192.168.2.0/24** come blocco CIDR.

