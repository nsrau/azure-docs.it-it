---
title: 'Scenario: Isolating reti virtuali'
titleSuffix: Azure Virtual WAN
description: Scenari per l'isolamento del routing reti virtuali
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567654"
---
# <a name="scenario-isolating-vnets"></a>Scenario: Isolating reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è impedire a reti virtuali di raggiungere altri. Questa operazione è nota come Isolating reti virtuali. Il carico di lavoro all'interno del VNet rimane isolato e non è in grado di comunicare con altri reti virtuali, come in uno scenario any-to-any. Tuttavia, reti virtuali sono necessari per raggiungere tutti i rami (VPN, ER e VPN utente). In questo scenario, tutte le connessioni VPN, ExpressRoute e VPN utente sono associate allo stesso e a una tabella di route. Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare questo scenario, prendere in considerazione i passaggi seguenti:

1. Creare una tabella di route personalizzata. Nell'esempio, la tabella di route è **RT_VNet**. Per creare una tabella di route, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md). Per ulteriori informazioni sulle tabelle di route, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).
2. Quando si crea la tabella di route **RT_VNet** , configurare le impostazioni seguenti:

   * **Associazione**: selezionare il reti virtuali che si vuole isolare.
   * **Propagazione**: selezionare l'opzione per i rami. le connessioni che implicano il ramo (VPN/er/P2S) propagheranno le route a questa tabella di route.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Reti virtuali isolato":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
