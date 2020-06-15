---
title: 'Gateway VPN di Azure: Informazioni sui profili client VPN da punto a sito'
description: Consente di usare il file del profilo client
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650643"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informazioni sui profili client VPN da punto a sito

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [Configurare i client OpenVPN per il gateway VPN di Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se nel gateway VPN è selezionata l'autenticazione di Azure AD, questa cartella non è presente nel file ZIP. Passare invece alla cartella AzureVPN e individuare azurevpnconfig.xml.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle connessioni da punto a sito, vedere [Informazioni sulla VPN da punto a sito](point-to-site-about.md).
