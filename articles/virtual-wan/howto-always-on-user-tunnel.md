---
title: Configurare un tunnel utente VPN always on
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive come configurare un tunnel utente VPN Always On per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016352"
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
