---
title: 'Creare un gateway VPN basato su Route: portale'
titleSuffix: Azure VPN Gateway
description: Creare un gateway VPN basato su route usando il portale di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: a44bdf654842d964a02f6aebe60e3632aabcd64d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151679"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Creare un gateway VPN basato su route usando il portale di Azure

Questo articolo illustra come creare rapidamente un gateway VPN di Azure basato su route usando il portale di Azure.  Il gateway VPN viene usato quando si crea una connessione VPN alla rete locale. È anche possibile usare il gateway VPN per connettere reti virtuali. 

Seguendo i passaggi in questo articolo si creeranno una rete virtuale, una subnet, una subnet del gateway e un gateway VPN (gateway di rete virtuale) basato su route. Dopo aver creato il gateway, è possibile configurare le connessioni. Questi passaggi richiedono una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="vnet"></a>Creare una rete virtuale

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwvalues"></a>Configurare e creare il gateway

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dell'SKU gateway selezionato.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Lo SKU del gateway Basic non supporta l'autenticazione IKEv2 o RADIUS. Se si prevede che i client Mac si connettano alla rete virtuale, non usare lo SKU Basic.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="viewgw"></a>Visualizzare il gateway VPN

1. Dopo la creazione del gateway, passare a VNet1 nel portale. Il gateway VPN viene visualizzato nella pagina Panoramica come dispositivo connesso.

   ![Dispositivi connessi](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Dispositivi connessi")

2. Nell'elenco di dispositivi fare clic su **VNet1GW** per visualizzare altre informazioni.

   ![Visualizzare il gateway VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Visualizzare il gateway VPN")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il gateway, è possibile creare una connessione tra la propria rete virtuale e un'altra rete virtuale oppure tra la propria rete virtuale e una posizione locale.

> [!div class="nextstepaction"]
> [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Creare una connessione da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Creare una connessione a un'altra rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
