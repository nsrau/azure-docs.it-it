---
title: 'Rete WAN virtuale di Azure: profili client VPN utente'
description: Questo consente di usare il file del profilo client
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066225"
---
# <a name="working-with-user-vpn-client-profiles"></a>Uso dei profili client VPN utente

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN utente.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato per includere la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla VPN utente WAN virtuale, vedere [creare una connessione VPN utente](virtual-wan-point-to-site-portal.md).
