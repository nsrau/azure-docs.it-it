---
title: Configurare un tunnel utente VPN always on
titleSuffix: Azure VPN Gateway
description: Questo articolo descrive come configurare un tunnel utente VPN Always On per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: bf9dbd0cef19ad54ba6c3b58f2b9b3071b98bd93
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370986"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurare un tunnel utente VPN Always On

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurare il gateway

 Usare le istruzioni riportate nell'articolo [configurare una connessione VPN da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per configurare il gateway VPN per l'uso di IKEv2 e l'autenticazione basata sui certificati.

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere un profilo, attenersi alla procedura seguente:

1. Eseguire il comando seguente:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Disconnettere la connessione e deselezionare la casella di controllo **Connetti automaticamente** .

   ![Pulizia](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi di connessione che possono verificarsi, vedere [problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
