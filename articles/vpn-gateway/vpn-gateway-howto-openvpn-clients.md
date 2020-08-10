---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Informazioni su come configurare OpenVPN per il gateway VPN di Azure per i client del sistema operativo Windows, Linux e Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036862"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurare i client OpenVPN per il gateway VPN di Azure

Questo articolo consente di configurare i client del ** &reg; protocollo OpenVPN** .

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si vuole che i client VPN siano in grado di accedere alle risorse in un altro VNet, seguire le istruzioni riportate nell'articolo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per configurare una connessione da VNet a VNet. Assicurarsi di abilitare BGP sui gateway e le connessioni, altrimenti il flusso del traffico non sarà possibile.

**"OpenVPN" è un marchio di OpenVPN Inc.**
