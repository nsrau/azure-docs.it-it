---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170139"
---
1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Aggiungere la subnet del gateway")
  
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Il valore GatewaySubnet è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

   ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Aggiunta della subnet del gateway")
  
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet.
