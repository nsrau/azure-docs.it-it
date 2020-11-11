---
title: Configurare i client OpenVPN per la rete WAN virtuale di Azure
description: Procedura per configurare i client OpenVPN per la rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491002"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurare un client OpenVPN per la rete WAN virtuale di Azure

Questo articolo consente di configurare i client del **&reg; protocollo OpenVPN** . È anche possibile usare il client VPN di Azure per Windows 10 per connettersi tramite il protocollo OpenVPN. Altre istruzioni sono disponibili [qui](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Prima di iniziare

Creare una configurazione VPN utente (da punto a sito). Assicurarsi di selezionare "OpenVPN" per il tipo di tunnel. Per i passaggi, vedere [creare una configurazione P2S per la rete WAN virtuale di Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla VPN utente (da punto a sito), vedere [creare connessioni VPN utente](virtual-wan-point-to-site-portal.md).

**"OpenVPN" è un marchio di OpenVPN Inc.**
