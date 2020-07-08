---
title: Configurare un tunnel utente VPN always on
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive come configurare un tunnel utente VPN Always On per la rete WAN virtuale
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564055"
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
