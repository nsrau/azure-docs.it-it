---
title: 'Scenario: instradare il traffico attraverso appliance virtuali usando le impostazioni personalizzate'
titleSuffix: Azure Virtual WAN
description: Questo scenario consente di instradare il traffico attraverso appliance virtuali usando un'appliance virtuale di rete diversa per il traffico associato a Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567909"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: instradare il traffico attraverso appliance virtuali-Custom (anteprima)

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario di appliance virtuale di rete, l'obiettivo è quello di instradare il traffico attraverso un'appliance virtuale di rete per VNet <-> Branch e usare un appliance virtuale di rete diverso per il traffico associato a Internet. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architettura dello scenario

Nella **Figura 1**è presente un hub, **hub 1**.

* **Hub 1** è connesso direttamente a appliance virtuale di reti virtuali **VNET 4** e **VNET 5**.

* Si prevede che il traffico tra reti virtuali 1, 2 e 3 e i rami (VPN/ER/P2S) vadano via **VNET 4 NVA** 10.4.0.5.

* Si prevede che tutto il traffico associato a Internet da reti virtuali 1, 2 e 3 venga indirizzato tramite **VNET 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare il routing tramite appliance virtuale di sistema, ecco i passaggi da considerare:

1. Affinché il traffico associato a Internet vada via VNET5, è necessario reti virtuali 1, 2 e 3 per connettersi direttamente tramite il peering VNet a VNET 5. È necessario anche un UDR configurato in reti virtuali per 0.0.0.0/0 e 10.5.0.5 hop successivo. Attualmente la rete WAN virtuale non consente l'uso di un appliance virtuale di rete per l'hop successivo nell'hub virtuale per 0.0.0.0/0.

1. Nella portale di Azure passare all'hub virtuale e creare una tabella di route personalizzata **RT_Shared** che apprenderà le route tramite propagazione da tutte le connessioni reti virtuali e Branch. Nella **Figura 2**viene illustrato come una tabella di route personalizzata vuota **RT_Shared**.

   * **Route:** Non è necessario aggiungere route statiche.

   * **Associazione:** Selezionare reti virtuali 4 e 5 per indicare che le connessioni reti virtuali 4 e 5 vengono associate alla tabella di route **RT_Shared**.

   * **Propagazione:** Poiché si desidera che tutti i rami e le connessioni VNet propaghino dinamicamente le route in questa tabella di route, selezionare Branch e tutti i reti virtuali.

1. Creare una tabella di route personalizzata **RT_V2B** per indirizzare il traffico da reti virtuali 1, 2 e 3 ai rami.

   * **Route:** Aggiungere una voce di route statica aggregata per Branch (VPN/ER/P2S) (10.2.0.0/16 nella **Figura 2**) con hop successivo come connessione VNET 4. È anche necessario configurare una route statica nella connessione di VNET 4 per i prefissi di ramo e indicare l'hop successivo come IP specifico dell'appliance virtuale di dispositivo in VNET 4.

   * **Associazione:** Selezionare tutti reti virtuali 1, 2 e 3. Ciò implica che le connessioni VNet 1, 2 e 3 verranno associate a questa tabella di route e saranno in grado di apprendere le route (statiche e dinamiche tramite propagazione) in questa tabella di route.

   * **Propagazione:** Le connessioni propagano le route alle tabelle di route. Selezionando reti virtuali 1, 2 e 3 si Abilita la propagazione delle route da reti virtuali 1, 2 e 3 a questa tabella di route. Non è necessario propagare le route dalle connessioni Branch a RT_V2B, perché il traffico VNet del ramo passa attraverso l'appliance virtuale di VNET4.
  
1. Modificare la tabella di route predefinita **DefaultRouteTable**.

   Tutte le connessioni VPN, ExpressRoute e VPN utente sono associate alla tabella di route predefinita. Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route.

   * **Route:** Aggiungere una voce di route statica aggregata per reti virtuali 1, 2 e 3 (10.1.0.0/16 nella **Figura 2**) con hop successivo come connessione VNET 4. È anche necessario configurare una route statica nella connessione di VNET 4 per i prefissi aggregati VNET 1, 2 e 3 e indicare l'hop successivo come IP specifico dell'appliance virtuale di dispositivo in VNET 4.

   * **Associazione:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurandosi che le connessioni a rami locali siano associate al *defaultroutetable*.

   * **Propagazione da:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurando che le connessioni locali propaghino le route al *defaultroutetable*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 2":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
