---
title: 'Scenario: isolamento personalizzato per reti virtuali'
titleSuffix: Azure Virtual WAN
description: "Scenari per il routing: impedire che i reti virtuali selezionati siano in grado di raggiungere l'altro"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567660"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: isolamento personalizzato per reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In uno scenario di isolamento personalizzato per reti virtuali, l'obiettivo è impedire che un set specifico di reti virtuali riesca a raggiungere un altro set specifico di reti virtuali. Tuttavia, reti virtuali sono necessari per raggiungere tutti i rami (VPN/ER/VPN utente).

In questo scenario, le connessioni VPN, ExpressRoute e VPN utente (collettivamente denominate Branch) sono associate alla stessa tabella di route (tabella di route predefinita). Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Flusso di lavoro dello scenario

Nella **Figura 1**sono presenti connessioni VNet blu e rosse.

* Il reti virtuali connesso a blu può raggiungere l'altro, oltre a raggiungere tutte le connessioni di Branch (VPN/ER/P2S).
* Il reti virtuali rosso può raggiungere l'altro, oltre a raggiungere tutte le connessioni di Branch (VPN/ER/P2S).

Quando si configura il routing, tenere presente i passaggi seguenti.

1. Creare due tabelle di route personalizzate nell'portale di Azure, **RT_BLUE** e **RT_RED**.
2. Per **RT_BLUE**della tabella di route, in:
   * **Associazione**: selezionare tutti reti virtuali blu
   * **Propagazione**: per i Branch selezionare l'opzione per i rami, le connessioni che implicano il ramo (VPN/er/P2S) propagheranno le route a questa tabella di route.
3. Ripetere gli stessi passaggi per **RT_RED** tabella di route per reti virtuali e rami rossi (VPN/er/P2S).

Questo comporterà la modifica della configurazione di routing, come illustrato nella figura seguente.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Figura 1":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
