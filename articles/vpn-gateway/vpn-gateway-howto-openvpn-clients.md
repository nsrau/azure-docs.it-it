---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Procedura per configurare i client OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984094"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurare i client OpenVPN per il gateway VPN di Azure

Questo articolo consente di configurare i client del ** &reg; protocollo OpenVPN** .

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si vuole che i client VPN siano in grado di accedere alle risorse in un altro VNet, seguire le istruzioni riportate nell'articolo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per configurare una connessione da VNet a VNet. Assicurarsi di abilitare BGP sui gateway e le connessioni, altrimenti il flusso del traffico non sarà possibile.

**"OpenVPN" è un marchio di OpenVPN Inc.**
