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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179994"
---
1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Aggiungere la subnet del gateway")
  
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Il valore GatewaySubnet è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

   ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Aggiunta della subnet del gateway")
  
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet.
