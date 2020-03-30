---
title: Come configurare i client OpenVPN per il Gateway VPN di Azure | Microsoft Docs
description: Procedura per configurare i client OpenVPN per il Gateway VPN di Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066185"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Configurare i client OpenVPN per il gateway VPN di AzureConfigure OpenVPN clients for Azure VPN Gateway

Questo articolo consente di configurare i client **del protocollo &reg; OpenVPN.**

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver completato i passaggi per configurare OpenVPN per il gateway VPN. Per informazioni dettagliate, vedere [configurare OpenVPN per il Gateway VPN di Azure](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si desidera che i client VPN siano in grado di accedere alle risorse in un'altra rete virtuale, seguire le istruzioni nell'articolo [da disco rigido a rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per configurare una connessione da vnet a vnet. Assicurarsi di abilitare BGP sui gateway e le connessioni, altrimenti il flusso del traffico non sarà possibile.

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**
