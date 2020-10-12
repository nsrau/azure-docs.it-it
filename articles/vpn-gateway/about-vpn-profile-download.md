---
title: 'Gateway VPN di Azure: Informazioni sui profili client VPN da punto a sito'
description: Usare questo articolo per trovare le informazioni necessarie per un profilo client VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985927"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informazioni sui profili client VPN da punto a sito

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [Configurare i client OpenVPN per il gateway VPN di Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se nel gateway VPN è selezionata l'autenticazione di Azure AD, questa cartella non è presente nel file ZIP. Passare invece alla cartella AzureVPN e individuare azurevpnconfig.xml.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle connessioni da punto a sito, vedere [Informazioni sulla VPN da punto a sito](point-to-site-about.md).
