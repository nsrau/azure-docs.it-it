---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Informazioni su come configurare OpenVPN per il gateway VPN di Azure per i client del sistema operativo Windows, Linux e Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435780"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurare i client OpenVPN per il gateway VPN di Azure

Questo articolo consente di configurare i client del ** &reg; protocollo OpenVPN** .

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si vuole che i client VPN siano in grado di accedere alle risorse in un altro VNet, seguire le istruzioni riportate nell'articolo [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per configurare una connessione da VNet a VNet. Assicurarsi di abilitare BGP sui gateway e le connessioni, altrimenti il flusso del traffico non sarà possibile.

**"OpenVPN" è un marchio di OpenVPN Inc.**
