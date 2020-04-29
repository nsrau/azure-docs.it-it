---
title: 'Gateway VPN di Azure: informazioni sui profili client VPN P2S'
description: Questo consente di usare il file del profilo client
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528507"
---
# <a name="about-p2s-vpn-client-profiles"></a>Informazioni sui profili client VPN P2S

Il file del profilo scaricato contiene le informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato per includere la chiave e il certificato. Per altre informazioni, vedere [configurare i client OpenVPN per il gateway VPN di Azure](vpn-gateway-howto-openvpn-clients.md#windows). Questa cartella non sarà presente nel file zip se è stata selezionata Azure AD autenticazione nel gateway VPN. Il azurevpnconfig. XML si troverà invece nella cartella AzureVPN

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su da punto a sito, vedere [informazioni su da punto a sito](point-to-site-about.md).
