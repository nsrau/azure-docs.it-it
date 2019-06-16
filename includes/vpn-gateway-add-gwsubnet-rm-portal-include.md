---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150291"
---
1. Nel [portale di Azure](http://portal.azure.com) selezionare la rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.

2. Nella sezione **Impostazioni** della pagina della rete virtuale selezionare **Subnet** per espandere la pagina **corrispondente**.

3. Nella pagina **Subnet** selezionare **Subnet del gateway** per aprire la pagina **Aggiungi subnet**.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Aggiungere la subnet del gateway")

4. Il **nome** della subnet verrà compilato automaticamente con il valore *GatewaySubnet*. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** inseriti automaticamente in modo che corrispondano ai requisiti della configurazione e quindi selezionare **OK** per creare la subnet.

   ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Aggiunta della subnet")