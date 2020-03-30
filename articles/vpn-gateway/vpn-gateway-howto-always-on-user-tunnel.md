---
title: Configurare un tunnel utente VPN Always-On
titleSuffix: Azure VPN Gateway
description: In questo articolo viene descritto come configurare un tunnel utente VPN Always On per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502268"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurare un tunnel utente VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurare il gateway

 Utilizzare le istruzioni nell'articolo [Configurare una connessione VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) da punto a sito per configurare il gateway VPN per l'utilizzo di IKEv2 e dell'autenticazione basata su certificati.

## <a name="configure-a-user-tunnel"></a>Configurare un tunnel utente

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere un profilo, attenersi alla seguente procedura:

1. Eseguire il comando seguente:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Scollegare la connessione e deselezionare la casella di controllo **Connetti automaticamente.**

   ![Pulizia](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per risolvere eventuali problemi di connessione che potrebbero verificarsi, vedere Problemi di connessione da punto ad sito di [Azure.](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
