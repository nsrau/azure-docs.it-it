---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina Subnet.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

  ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Il valore GatewaySubnet è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione.

  ![Aggiunta della subnet del gateway](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Aggiunta della subnet del gateway")
5. Fare clic su **OK** nella parte inferiore della pagina per creare la subnet.