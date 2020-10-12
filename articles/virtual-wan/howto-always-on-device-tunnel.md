---
title: Configurare un tunnel VPN Always-On
titleSuffix: Azure Virtual WAN
description: Procedura per configurare Always On tunnel del dispositivo VPN per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983686"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configurare un tunnel del dispositivo VPN Always On per la rete WAN virtuale

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario creare una configurazione da punto a sito e modificare l'assegnazione dell'hub virtuale. Per istruzioni, vedere le sezioni seguenti:

* [Creare una configurazione di P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Creare hub con il gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Configurare il tunnel del dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Per rimuovere un profilo

Per rimuovere il profilo, eseguire il comando seguente:

![Screenshot mostra una finestra di PowerShell che esegue il comando Remove-VpnConnection-Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).