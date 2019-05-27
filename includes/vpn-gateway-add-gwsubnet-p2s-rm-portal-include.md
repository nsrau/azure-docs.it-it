---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157277"
---
1. Nel [portale](http://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina **Subnet**.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** per aprire la pagina **Aggiungi subnet**. 

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione. Non configurare la tabella di route o gli endpoint di servizio.

   ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Aggiunta della subnet")
5. Fare clic su **OK** in basso nella pagina per creare la subnet.
