---
title: 'Rete WAN virtuale di Azure: profili client VPN utente'
description: Consente di usare il file del profilo client
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753135"
---
# <a name="working-with-user-vpn-client-profiles"></a>Uso dei profili client VPN utente

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN utente.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla VPN utente WAN virtuale, vedere [creare una connessione VPN utente](virtual-wan-point-to-site-portal.md).
