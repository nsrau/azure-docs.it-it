---
title: 'Gateway VPN di Azure: informazioni sui profili client VPN P2SAzure VPN Gateway: About P2S VPN client profiles'
description: Ciò consente di lavorare con il file del profilo client
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528507"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informazioni sui profili client VPN P2S

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo ti aiuterà a ottenere e comprendere le informazioni necessarie per un profilo client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato per includere la chiave e il certificato. Per altre informazioni, vedere [Configurare i client OpenVPN per](vpn-gateway-howto-openvpn-clients.md#windows)il gateway VPN di Azure.For more information, see Configure OpenVPN clients for Azure VPN Gateway . Questa cartella non sarà presente nel file zip se nel gateway VPN è selezionata l'autenticazione di Azure AD. Al contrario, il file azurevpnconfig.xml si troverà nella cartella AzureVPN.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul punto a sito, vedere [Informazioni sul punto a sito](point-to-site-about.md).
