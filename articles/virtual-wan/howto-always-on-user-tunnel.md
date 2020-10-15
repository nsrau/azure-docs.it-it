---
title: Configurare un tunnel utente VPN Always-On
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive come configurare un tunnel utente VPN Always On per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313587"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configurare un tunnel utente VPN Always On per la rete WAN virtuale

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario creare una configurazione da punto a sito e modificare l'assegnazione dell'hub virtuale. Per istruzioni, vedere le sezioni seguenti:

* [Creare una configurazione di P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Creare hub con il gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Configurare un tunnel utente

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere un profilo, attenersi alla procedura seguente:

1. Eseguire il comando seguente:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Disconnettere la connessione e deselezionare la casella di controllo **Connetti automaticamente** .

   ![Pulizia](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
