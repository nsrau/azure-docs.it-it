---
title: Instradare il traffico attraverso appliance virtuali usando le impostazioni personalizzate
titleSuffix: Azure Virtual WAN
description: Questo scenario consente di instradare il traffico attraverso appliance virtuali usando un'appliance virtuale di rete diversa per il traffico associato a Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 122e76e4bde96823ff18207bc24df4a8e91afb1c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517969"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scenario: instradare il traffico attraverso appliance virtuali usando le impostazioni personalizzate

Quando si lavora con il routing dell'hub virtuale WAN virtuale di Azure, sono disponibili diverse opzioni. L'obiettivo di questo articolo è quando si vuole instradare il traffico attraverso un'appliance virtuale di rete per la comunicazione tra reti virtuali e Branch e usare un appliance virtuale di rete diversa per il traffico associato a Internet. Per ulteriori informazioni, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="design"></a>Progettazione

* **Spoke** per le reti virtuali connesse all'hub virtuale. (Ad esempio, VNet 1, VNet 2 e VNet 3 nel diagramma più avanti in questo articolo).
* **Servizio VNet** per reti virtuali in cui gli utenti hanno distribuito un appliance virtuale di rete per controllare il traffico non Internet e possibilmente con servizi comuni a cui si accede tramite spoke. (Ad esempio, VNet 4 nel diagramma più avanti in questo articolo). 
* **VNet perimetrale** per le reti virtuali in cui gli utenti hanno distribuito un'appliance virtuale di rete da usare per controllare il traffico associato a Internet. Ad esempio, VNet 5 nel diagramma più avanti in questo articolo.
* **Hub** per hub WAN virtuali gestiti da Microsoft.

Nella tabella seguente sono riepilogate le connessioni supportate in questo scenario:

| Da          | A|Spoke|Servizio VNet|Rami|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Spoke**| ->| Direttamente |Direttamente | tramite il servizio VNet |tramite VNet perimetrale |
| **Servizio VNet**| ->| Direttamente |n/d| Direttamente | |
| **Rami** | ->| tramite il servizio VNet |Direttamente| Direttamente |  |

Ognuna delle celle nella matrice di connettività indica se il flusso di connettività viene effettuato direttamente su una rete WAN virtuale o su una delle reti virtuali con un appliance virtuale di rete. 

Tenere presenti questi dettagli:
* Spoke
  * I spoke raggiungono gli altri spoke direttamente sugli hub WAN virtuali.
  * I spoke otterranno la connettività ai rami tramite una route statica che punta al servizio VNet. Non apprenderanno prefissi specifici dai rami, perché sono più specifici ed eseguono l'override del riepilogo.
  * Spokes invierà traffico Internet al perimetro di VNet tramite un peering VNet diretto.
* I rami riceveranno spoke tramite un routing statico che punta al servizio VNet. Non apprenderanno prefissi specifici dalle reti virtuali che eseguono l'override della route statica riepilogata.
* Il servizio VNet sarà simile a quello dei servizi condivisi VNet che devono essere raggiungibili da ogni rete virtuale e ogni ramo.
* Il VNet perimetrale non deve avere connettività sulla rete WAN virtuale, perché il solo traffico che supporterà riguarda i peering di rete virtuale diretti. Per semplificare la configurazione, tuttavia, usare lo stesso modello di connettività di VNet perimetrale.

Sono disponibili tre modelli di connettività distinti che si traduce in tre tabelle di route. Le associazioni alle diverse reti virtuali sono:

* Spoke
  * Tabella di route associata: **RT_V2B**
  * Propagazione alle tabelle di route: **RT_V2B** e **RT_SHARED**
* NVA reti virtuali (Service VNet and DMZ VNet):
  * Tabella di route associata: **RT_SHARED**
  * Propagazione alle tabelle di route: **RT_SHARED**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_SHARED** e **default**

Queste route statiche assicurano che il traffico da e verso la rete virtuale e il ramo attraversino l'appliance virtuale di rete nel servizio VNet (VNet 4):

| Descrizione | Tabella di route | Route statica              |
| ----------- | ----------- | ------------------------- |
| Rami    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| Spoke di NVA  | Valore predefinito     | 10.1.0.0/16-> vnet4conn  |

A questo punto è possibile usare la rete WAN virtuale per selezionare la connessione corretta a cui inviare i pacchetti. È anche necessario usare la rete WAN virtuale per selezionare l'azione corretta da eseguire quando si ricevono questi pacchetti. Usare le tabelle di route di connessione per questo, come indicato di seguito:

| Descrizione | Connessione | Route statica            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Per ulteriori informazioni, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architecture

Di seguito è riportato un diagramma dell'architettura descritta in precedenza in questo articolo.

È presente un hub, denominato **hub 1**.

* **Hub 1** è connesso direttamente a appliance virtuale di reti virtuali **VNet 4** e **VNet 5**.

* Si prevede che il traffico tra reti virtuali 1, 2 e 3 e i rami vadano via **VNet 4 NVA** 10.4.0.5.

* Si prevede che tutto il traffico associato a Internet da reti virtuali 1, 2 e 3 venga indirizzato tramite **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagramma dell'architettura di rete.":::

## <a name="workflow"></a>Flusso di lavoro

Per configurare il routing tramite appliance virtuale di sistema, ecco i passaggi da considerare:

1. Per il traffico associato a Internet da usare con VNet 5, è necessario reti virtuali 1, 2 e 3 per connettersi direttamente tramite peering di rete virtuale a VNet 5. È anche necessaria una route definita dall'utente configurata nelle reti virtuali per 0.0.0.0/0 e hop successivo 10.5.0.5. Attualmente la rete WAN virtuale non consente l'uso di un appliance virtuale di rete per l'hop successivo nell'hub virtuale per 0.0.0.0/0.

1. Nel portale di Azure passare all'hub virtuale e creare una tabella di route personalizzata denominata **RT_Shared**. Questa tabella apprende le route tramite la propagazione da tutte le reti virtuali e le connessioni a rami. È possibile visualizzare questa tabella vuota nel diagramma seguente.

   * **Route:** Non è necessario aggiungere route statiche.

   * **Associazione:** Selezionare reti virtuali 4 e 5, che significa che le connessioni di queste reti virtuali vengono associate alla tabella di route **RT_Shared**.

   * **Propagazione:** Poiché si desidera che tutti i rami e le connessioni di rete virtuale propaghino dinamicamente le route in questa tabella di route, selezionare Branch e tutte le reti virtuali.

1. Creare una tabella di route personalizzata denominata **RT_V2B** per indirizzare il traffico da reti virtuali 1, 2 e 3 ai rami.

   * **Route:** Aggiungere una voce di route statica aggregata per i rami, con hop successivo come connessione VNet 4. Configurare una route statica nella connessione di VNet 4 per i prefissi di ramo e indicare l'hop successivo come indirizzo IP specifico dell'appliance virtuale di dispositivo in VNet 4.

   * **Associazione:** Selezionare tutti reti virtuali 1, 2 e 3. Ciò implica che le connessioni VNet 1, 2 e 3 verranno associate a questa tabella di route e saranno in grado di apprendere le route (statiche e dinamiche tramite propagazione) in questa tabella di route.

   * **Propagazione:** Le connessioni propagano le route alle tabelle di route. Selezionando reti virtuali 1, 2 e 3 è possibile abilitare la propagazione delle route da reti virtuali 1, 2 e 3 a questa tabella di route. Non è necessario propagare le route da connessioni Branch a **RT_V2B**, perché il traffico di rete virtuale del ramo passa attraverso l'appliance virtuale di rete in VNet 4.
  
1. Modificare la tabella di route predefinita, **DefaultRouteTable**.

   Tutte le connessioni VPN, Azure ExpressRoute e VPN utente sono associate alla tabella di route predefinita. Tutte le connessioni VPN, ExpressRoute e VPN utente propagano le route allo stesso set di tabelle di route.

   * **Route:** Aggiungere una voce di route statica aggregata per reti virtuali 1, 2 e 3, con hop successivo come connessione VNet 4. Configurare una route statica nella connessione di VNet 4 per i prefissi aggregati VNet 1, 2 e 3 e indicare l'hop successivo come indirizzo IP specifico di appliance virtuale di dispositivo in VNet 4.

   * **Associazione:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurandosi che le connessioni a branch locali siano associate alla tabella di route predefinita.

   * **Propagazione da:** Assicurarsi che sia selezionata l'opzione per Branch (VPN/ER/P2S), assicurandosi che le connessioni locali propaghino le route alla tabella di route predefinita.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagramma dell'architettura di rete." lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
