---
title: 'Scenario: instradare il traffico attraverso appliance virtuali usando le impostazioni personalizzate'
titleSuffix: Azure Virtual WAN
description: Questo scenario consente di instradare il traffico attraverso appliance virtuali usando un'appliance virtuale di rete diversa per il traffico associato a Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 5546fc63b01d1da6b4033e071ac071574ab9699a
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987205"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: instradare il traffico attraverso appliance virtuali-Custom (anteprima)

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario di appliance virtuale di rete, l'obiettivo è quello di instradare il traffico attraverso un'appliance virtuale di rete per la comunicazione tra reti virtuali e rami e usare un'appliance virtuale di rete diversa per il traffico associato a Internet. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

In questo scenario verrà usata la convenzione di denominazione:

* "Service VNet" per le reti virtuali in cui gli utenti hanno distribuito un appliance virtuale di rete (VNet 4 nella **Figura 1**) per controllare il traffico non Internet.
* "DMZ VNet" per le reti virtuali in cui gli utenti hanno distribuito un'appliance virtuale di rete da usare per controllare il traffico associato a Internet (VNet 5 nella **Figura 1**).
* "NVA spokes" per le reti virtuali connesse a un appliance virtuale di rete VNet (VNet 1, VNet 2 e VNet 3 nella **Figura 1**).
* "Hub" per hub WAN virtuali gestiti da Microsoft.

La seguente matrice di connettività riepiloga i flussi supportati in questo scenario:

**Matrice di connettività**

| Da          | Con:|*Spoke di NVA*|*Servizio VNet*|*VNet DMZ*|*Rami statici*|
|---|---|---|---|---|---|
| **Spoke di NVA**| &#8594;|      X |            X |   Peering |    Statico    |
| **Servizio VNet**| &#8594;|    X |            X |      X    |      X       |
| **VNet DMZ** | &#8594;|       X |            X |      X    |      X       |
| **Rami** | &#8594;|  Statico |            X |      X    |      X       |

Ognuna delle celle nella matrice di connettività descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga) apprende un prefisso di destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo) per un flusso di traffico specifico. Di seguito vengono descritte le diverse righe:

* Spoke di NVA:
  * I spoke raggiungono gli altri spoke direttamente sugli hub WAN virtuali.
  * I spoke otterranno la connettività ai rami tramite una route statica che punta al servizio VNet. Non devono apprendere prefissi specifici dai rami (in caso contrario, sono più specifici ed eseguono l'override del riepilogo).
  * I spoke invieranno il traffico Internet alla rete perimetrale VNet tramite un peering VNet diretto.
* Rami
  * I rami riceveranno spoke tramite un routing statico che punta al servizio VNet. Non devono apprendere prefissi specifici da reti virtuali che eseguono l'override della route statica riepilogata.
* Il servizio VNet sarà simile a quello dei servizi condivisi VNet che devono essere raggiungibili da ogni VNet e ogni ramo.
* Il VNet della rete perimetrale non è in realtà necessario avere connettività sulla rete WAN virtuale, poiché l'unico traffico supportato sarà il peering diretto di VNet. Tuttavia, si userà lo stesso modello di connettività per la rete perimetrale VNet per semplificare la configurazione.

Quindi, la nostra matrice di connettività offre tre modelli di connettività distinti, che si traduce in tre tabelle di route. Le associazioni a reti virtuali diversi saranno le seguenti:

* Spoke di NVA:
  * Tabella di route associata: **RT_V2B**
  * Propagazione alle tabelle di route: **RT_V2B** e **RT_SHARED**
* Appliance virtuale di reti virtuali (interna e Internet):
  * Tabella di route associata: **RT_SHARED**
  * Propagazione alle tabelle di route: **RT_SHARED**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_SHARED** e **default**

Queste route statiche sono necessarie per assicurarsi che il traffico da VNet a ramo e da ramo a VNet passi attraverso l'appliance virtuale di sicurezza nel servizio VNet (VNet 4):

| Descrizione | Tabella di route | Route statica              |
| ----------- | ----------- | ------------------------- |
| Rami    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| Spoke di NVA  | Impostazione predefinita     | 10.1.0.0/16-> vnet4conn  |

A questo punto, la rete WAN virtuale sa a quale connessione inviare i pacchetti, ma la connessione deve sapere cosa fare quando ricevono i pacchetti: qui vengono usate le tabelle della route di connessione.

| Descrizione | Connessione | Route statica            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

A questo punto, tutto dovrebbe essere sul posto.

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architettura

Nella **Figura 1**è presente un hub, **hub 1**.

* **Hub 1** è connesso direttamente a appliance virtuale di reti virtuali **VNet 4** e **VNet 5**.

* Si prevede che il traffico tra reti virtuali 1, 2 e 3 e i rami (VPN/ER/P2S) vadano via **VNet 4 NVA** 10.4.0.5.

* Si prevede che tutto il traffico associato a Internet da reti virtuali 1, 2 e 3 venga indirizzato tramite **VNet 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

Per configurare il routing tramite appliance virtuale di sistema, ecco i passaggi da considerare:

1. Affinché il traffico associato a Internet vada via VNet 5, è necessario reti virtuali 1, 2 e 3 per connettersi direttamente tramite il peering VNet a VNet 5. È necessario anche un UDR configurato in reti virtuali per 0.0.0.0/0 e 10.5.0.5 hop successivo. Attualmente la rete WAN virtuale non consente l'uso di un appliance virtuale di rete per l'hop successivo nell'hub virtuale per 0.0.0.0/0.

1. Nella portale di Azure passare all'hub virtuale e creare una tabella di route personalizzata **RT_Shared** che apprenderà le route tramite propagazione da tutte le connessioni reti virtuali e Branch. Nella **Figura 2**viene illustrato come una tabella di route personalizzata vuota **RT_Shared**.

   * **Route:** Non è necessario aggiungere route statiche.

   * **Associazione:** Selezionare reti virtuali 4 e 5 per indicare che le connessioni reti virtuali 4 e 5 vengono associate alla tabella di route **RT_Shared**.

   * **Propagazione:** Poiché si desidera che tutti i rami e le connessioni VNet propaghino dinamicamente le route in questa tabella di route, selezionare Branch e tutti i reti virtuali.

1. Creare una tabella di route personalizzata **RT_V2B** per indirizzare il traffico da reti virtuali 1, 2 e 3 ai rami.

   * **Route:** Aggiungere una voce di route statica aggregata per Branch (VPN/ER/P2S) (10.2.0.0/16 nella **Figura 2**) con hop successivo come connessione VNet 4. È anche necessario configurare una route statica nella connessione di VNet 4 per i prefissi di ramo e indicare l'hop successivo come IP specifico dell'appliance virtuale di dispositivo in VNet 4.

   * **Associazione:** Selezionare tutti reti virtuali 1, 2 e 3. Ciò implica che le connessioni VNet 1, 2 e 3 verranno associate a questa tabella di route e saranno in grado di apprendere le route (statiche e dinamiche tramite propagazione) in questa tabella di route.

   * **Propagazione:** Le connessioni propagano le route alle tabelle di route. Selezionando reti virtuali 1, 2 e 3 si Abilita la propagazione delle route da reti virtuali 1, 2 e 3 a questa tabella di route. Non è necessario propagare le route dalle connessioni Branch a RT_V2B, perché il traffico VNet del ramo passa attraverso l'appliance virtuale di VNet 4.
  
1. Modificare la tabella di route predefinita **DefaultRouteTable**.

   Tutte le connessioni VPN, ExpressRoute e VPN utente sono associate alla tabella di route predefinita. Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route.

   * **Route:** Aggiungere una voce di route statica aggregata per reti virtuali 1, 2 e 3 (10.1.0.0/16 nella **Figura 2**) con hop successivo come connessione VNet 4. È anche necessario configurare una route statica nella connessione di VNet 4 per i prefissi aggregati VNet 1, 2 e 3 e indicare l'hop successivo come IP specifico dell'appliance virtuale di dispositivo in VNet 4.

   * **Associazione:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurandosi che le connessioni a rami locali siano associate al *defaultroutetable*.

   * **Propagazione da:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurando che le connessioni locali propaghino le route al *defaultroutetable*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 2" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
