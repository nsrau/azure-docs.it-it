---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780267"
---
La subnet del gateway contiene gli indirizzi IP riservati usati dai servizi del gateway di rete virtuale. Creare una subnet del gateway.

1. Nel portale passare alla rete virtuale per cui si vuole creare un gateway di rete virtuale.
2. Nella pagina della rete virtuale fare clic su **Subnet** per espandere la pagina **VNet1 - Subnet**.
3. Fare clic su **+Subnet del gateway** in alto per aprire la pagina **Aggiungi subnet**.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore "GatewaySubnet". Modificare l'intervallo di indirizzi riempito automaticamente **(blocco CIDR)** in modo che corrisponda al valore seguente:

   **Intervallo di indirizzi (blocco CIDR)** : 10.1.255.0/27

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Aggiungere la subnet del gateway")
5. Lasciare le altre impostazioni predefinite di **None** o **0 selezionate**. Quindi, fare clic su **OK** per creare la subnet del gateway.