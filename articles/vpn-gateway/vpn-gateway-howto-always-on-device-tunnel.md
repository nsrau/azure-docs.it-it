---
title: Configurare un tunnel VPN always on
titleSuffix: Azure VPN Gateway
description: Procedura per configurare Always On tunnel VPN per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6db48928ebac115c42c643d669f6541a3654a53a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983122"
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
