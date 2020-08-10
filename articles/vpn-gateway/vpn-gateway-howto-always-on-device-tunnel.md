---
title: Configurare un tunnel VPN always on
titleSuffix: Azure VPN Gateway
description: Informazioni su come usare i gateway con Windows 10 Always On per creare e configurare tunnel di dispositivi permanenti in Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: e64d2223740942fe35ae8a730303bee8893c2489
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035604"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurare un tunnel di dispositivo VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurare il gateway

Configurare il gateway VPN per l'uso di IKEv2 e l'autenticazione basata su certificati usando l'articolo [configurare una connessione VPN da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Configurare il tunnel del dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere il profilo, eseguire il comando seguente:

![Pulizia](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi, vedere [problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
