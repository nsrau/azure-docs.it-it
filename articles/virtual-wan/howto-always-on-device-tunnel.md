---
title: Configurare un tunnel VPN always on
titleSuffix: Azure Virtual WAN
description: Procedura per configurare Always On tunnel del dispositivo VPN per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 7adeb9682336b19cc87d8c73a7f9ad8bda5e7828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564071"
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

![Pulizia](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).