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
ms.openlocfilehash: a4101f3bfe83859eea525370b5eebcaa6e193a2d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168335"
---
1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

  ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Aggiungere la subnet del gateway")
  
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Il valore GatewaySubnet è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

  ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Aggiunta della subnet del gateway")
  
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet.
