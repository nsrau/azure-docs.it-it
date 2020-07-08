---
title: "Scenario: indirizzare il traffico attraverso un'appliance virtuale di dispositivo"
titleSuffix: Azure Virtual WAN
description: Indirizzare il traffico attraverso un'appliance virtuale di rete
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0716ca8f0457ca801098c97dd7a5e68751822d4d
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848092"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: indirizzare il traffico attraverso un'appliance virtuale di dispositivo

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario di appliance virtuale di rete, l'obiettivo è instradare il traffico attraverso un'appliance virtuale di rete (appliance virtuale di rete) per il ramo a VNet e VNet al ramo. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architettura dello scenario

Nella **Figura 1**sono presenti due hub: **Hub 1** e **Hub 2**.

* **Hub 1** e **Hub 2** sono connessi direttamente a appliance virtuale di reti virtuali **VNET 2** e **VNET 4**.

* Il peering di **VNET 5** e **VNET 6** è **VNET 2**.

* **VNET 7** e **VNET 8** sono peering con **VNET 4**.

* **Reti virtuali 5, 6, 7, 8** sono spoke indiretti, non direttamente connessi a un hub virtuale.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare il routing tramite appliance virtuale di sistema, ecco i passaggi da considerare:

1. Identificare la connessione VNet per l'appliance virtuale di dispositivo. Nella **Figura 1**sono **VNET 2 Connection (Eastusconn)** e **VNET 4 Connection (weconn)**.

   Verificare la configurazione di UdR:
   * Da VNET 5 a 6 a VNET 2 appliance virtuale di dispositivo virtuale
   * Da VNET 7 e 8 a VNET 4 Appliance IP 
   
   Non è necessario connettere direttamente VNET 5, 6, 7 e 8 agli hub virtuali. Assicurarsi che gruppi in reti virtuali 5, 6, 7, 8 consentano il traffico per Branch (VPN/ER/P2S) o reti virtuali connesso alla rispettiva reti virtuali remota. Ad esempio, VNET 5, 6 deve garantire che gruppi consenta il traffico per i prefissi degli indirizzi locali e reti virtuali 7, 8 connessi all'Hub remoto 2. 

2. Aggiungere una voce di route statica aggregata per reti virtuali 2, 5, 6 alla tabella di route predefinita dell'hub 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Esempio":::

3. Configurare una route statica per reti virtuali 5, 6 nella connessione di rete virtuale di VNET 2. Per configurare la configurazione del routing per una connessione di rete virtuale, vedere [routing dell'hub virtuale](how-to-virtual-hub-routing.md#routing-configuration).

4. Aggiungere una voce di route statica aggregata per reti virtuali 4, 7, 8 alla tabella di route predefinita dell'hub 1.

5. Ripetere i passaggi 2, 3 e 4 per la tabella di route predefinita dell'hub 2.

Questo comporterà la modifica della configurazione di routing, come illustrato nella figura seguente.

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Risultato":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
