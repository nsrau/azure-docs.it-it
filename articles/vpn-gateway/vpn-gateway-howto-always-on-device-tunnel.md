---
title: Configurare un tunnel VPN Always-On
titleSuffix: Azure VPN Gateway
description: Procedura per configurare il tunnel VPN Always On per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371747"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurare un tunnel di dispositivo VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurare il gateway

Configurare il gateway VPN per l'utilizzo di IKEv2 e l'autenticazione basata su certificati utilizzando l'articolo [Configurare una connessione VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) da punto a sito.

## <a name="configure-the-device-tunnel"></a>Configurare il tunnel del dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere il profilo, eseguire il comando seguente:

![Pulizia](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi, vedere Problemi di [connessione da punto ad azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
