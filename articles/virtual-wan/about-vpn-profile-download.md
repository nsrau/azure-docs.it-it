---
title: 'Rete WAN virtuale di Azure: profili client VPN utente'
description: Consente di usare il file del profilo client
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 116ec518f7788e620fc6bd177e535c8f6af99d10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267772"
---
# <a name="working-with-user-vpn-client-profiles"></a>Uso dei profili client VPN utente

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN utente.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla VPN utente WAN virtuale, vedere [creare una connessione VPN utente](virtual-wan-point-to-site-portal.md).
