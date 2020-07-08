---
title: 'Scenario: any-to-any'
titleSuffix: Azure Virtual WAN
description: Scenari di routing-any-to-any
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567669"
---
# <a name="scenario-any-to-any"></a>Scenario: any-to-any

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In uno scenario any-to-any qualsiasi spoke può raggiungere un altro spoke. Quando esistono più hub, il routing da Hub a hub (noto anche come Interhub) è abilitato per impostazione predefinita nella rete WAN virtuale standard. 

In questo scenario, le connessioni VPN, ExpressRoute e VPN utente sono associate alla stessa tabella di route. Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architettura dello scenario

Nella **Figura 1**, tutti i reti virtuali e i rami (VPN, EXPRESSROUTE, P2S) possono essere raggiungibili tra loro. In un hub virtuale le connessioni funzionano come segue:

* Una connessione VPN connette un sito VPN a un gateway VPN.
* Una connessione di rete virtuale connette una rete virtuale a un hub virtuale. Il router dell'hub virtuale fornisce la funzionalità di transito tra reti virtuali.
* Una connessione ExpressRoute connette un circuito ExpressRoute a un gateway ExpressRoute.

Queste connessioni (per impostazione predefinita al momento della creazione) sono associate alla tabella di route predefinita, a meno che non si imposti la configurazione di routing della connessione a **Nessuna**o a una tabella di route personalizzata. Queste connessioni propagano anche le route, per impostazione predefinita alla tabella di route predefinita. Questo è ciò che consente uno scenario any-to-any in cui qualsiasi spoke (VNet, VPN, ER, P2S) può raggiungere l'uno con l'altro.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Questo scenario è abilitato per impostazione predefinita per la rete WAN virtuale standard. Se l'impostazione per Branch-to-Branch è disabilitata nella configurazione WAN, ciò non consentirà la connettività tra spoke del ramo. VPN/ExpressRoute/VPN utente sono considerati spoke del ramo nella rete WAN virtuale

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
