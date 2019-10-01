---
title: Architettura della rete di transito globale WAN virtuale di Azure | Microsoft Docs
description: Informazioni sull'architettura di rete di transito globale per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695260"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architettura di rete di transito globale e rete WAN virtuale

L'architettura di rete di transito globale è stata adottata dalle aziende per consolidare, connettere e controllare il footprint IT moderno incentrato sul cloud. In una moderna azienda incentrata sul cloud, il traffico di rete non deve essere revisionato in HQ. L'architettura di rete di transito globale si basa su concetti di rete noti e su nuovi concetti unici per le architetture cloud e basate su cloud.

![architettura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figura 1: Rete di transito globale con WAN virtuale @ no__t-0

Le aziende moderne richiedono una connettività onnipresente tra le applicazioni, i dati e gli utenti Hyper-distribuiti nel cloud e in locale. La rete WAN virtuale di Azure consente un'architettura di rete di transito globale abilitando connettività universale, any-to-any tra set distribuiti a livello globale di reti virtuali, siti, applicazioni e utenti. La rete WAN virtuale di Azure è un servizio gestito da Microsoft. Tutti i componenti di rete di cui questo servizio è composto sono ospitati e gestiti da Microsoft. Per altre informazioni sulla rete WAN virtuale, vedere l'articolo [Panoramica della rete WAN virtuale](virtual-wan-about.md) .

Nell'architettura WAN virtuale di Azure, le aree di Azure servono come hub a cui è possibile scegliere di connettere i rami. Una volta connessi i rami, è possibile sfruttare la backbone di Azure per stabilire la connettività da ramo a VNet e, facoltativamente, da ramo a ramo.

È possibile stabilire una rete WAN virtuale creando un singolo hub WAN virtuale nell'area con il maggior numero di spoke (Branch, reti virtuali, utenti) e quindi connettendo i spoke che si trovano in altre aree all'hub. In alternativa, se i spoke sono distribuiti geograficamente, è anche possibile creare un'istanza degli hub regionali e interconnettere gli hub. Gli hub fanno parte della stessa rete WAN virtuale, ma possono essere associati a criteri regionali diversi.

## <a name="hub"></a>Transito Hub e spoke

L'architettura di rete di transito globale è basata su un modello di connettività hub-spoke classico in cui la rete ospitata nel cloud ' hub ' consente la connettività transitiva tra gli endpoint che possono essere distribuiti tra tipi diversi di "spoke".
  
In questo modello, un spoke può essere:

* Rete virtuale (reti virtuali)
* Sito del ramo fisico
* Utente remoto
* Internet

![diagramma di transito globale Hub e spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figura 2: Hub-and-spoke @ no__t-0

Nella figura 2 è illustrata la visualizzazione logica della rete globale in cui utenti distribuiti geograficamente, siti fisici e reti virtuali vengono interconnessi tramite un hub di rete ospitato nel cloud. Questa architettura consente la connettività di transito a un hop logico tra gli endpoint di rete. I spoke sono connessi all'hub da vari servizi di rete di Azure, ad esempio ExpressRoute o VPN da sito a sito, per i rami fisici, le connessioni VNet per reti virtuali e la VPN da punto a sito per gli utenti remoti.

## <a name="crossregion"></a>Connettività tra aree

Per un'azienda, un'impronta cloud segue in genere il footprint fisico. La maggior parte delle aziende accede al cloud da un'area più vicina al sito fisico e agli utenti. Una delle principali principali dell'architettura di rete globale è l'abilitazione della connettività tra aree tra le entità di rete e gli endpoint. Un footprint cloud può estendersi su più aree. Questo significa che il traffico da un ramo connesso al cloud in un'area può raggiungere un altro ramo o una VNet in un'area diversa usando la connettività da Hub a hub, che attualmente si trova in questo percorso.

## <a name="any"></a>Connettività any-to-any

L'architettura di rete di transito globale consente la *connettività any-to-any* tramite un hub di rete centrale. Questa architettura Elimina o riduce la necessità di modelli di connettività a mesh completo o a mesh parziali più complessi da compilare e gestire. Inoltre, il controllo di routing nelle reti hub e spoke e rete mesh è più semplice da configurare e gestire.

La connettività any-to-any, nel contesto di un'architettura globale, consente a un'organizzazione con utenti, rami, Data Center, reti virtuali e applicazioni distribuite a livello globale di connettersi tra loro tramite l'hub di transito. L'hub di transito funge da sistema di transito globale.

![percorsi di traffico](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figura 3: Percorsi di traffico della rete WAN virtuale @ no__t-0

La rete WAN virtuale di Azure supporta i seguenti percorsi di connettività di transito globali. Le lettere tra parentesi vengono mappate alla figura 3.

* Branch-to-VNet (a)  
* Ramo a ramo (b)
* Utente remoto-VNet (c)
* Remote User-to-Branch (d)
* Da VNet a VNet mediante il peering VNet (e)
* Copertura globale di ExpressRoute 

### <a name="branchvnet"></a>Da ramo a VNet

Branch-to-VNet è il percorso primario supportato dalla rete WAN virtuale di Azure. Questo percorso consente di connettere i rami ai carichi di lavoro aziendali di Azure IAAS distribuiti in Azure reti virtuali. È possibile connettere i rami alla rete WAN virtuale tramite ExpressRoute o VPN da sito a sito. Il traffico viene transitato verso reti virtuali connesse agli hub WAN virtuali tramite connessioni VNet. Il [transito del gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) non è necessario per la rete WAN virtuale perché la rete WAN virtuale Abilita automaticamente il transito dei gateway nei siti dei rami.

### <a name="branchbranch"></a>Da ramo a ramo

I rami possono essere connessi a un hub WAN virtuale di Azure usando circuiti ExpressRoute e/o connessioni VPN da sito a sito. È possibile connettere i rami all'hub WAN virtuale che si trova nell'area più vicina al ramo.

Questa opzione consente alle aziende di sfruttare la backbone di Azure per connettere i rami. Tuttavia, anche se questa funzionalità è disponibile, è necessario valutare i vantaggi della connessione dei rami sulla rete WAN virtuale di Azure rispetto all'uso di una rete WAN privata.

### <a name="usertovnet"></a>Da utente remoto a VNet

È possibile abilitare l'accesso remoto diretto e sicuro ad Azure tramite connessioni da punto a sito da un client utente remoto a una rete WAN virtuale. Gli utenti remoti aziendali non devono più eseguire il tornamento al cloud usando una VPN aziendale.

### <a name="usertobranch"></a>Da utente a ramo remoto

Il percorso remoto da utente a ramo consente agli utenti remoti che usano una connessione da punto a sito ad Azure di accedere ai carichi di lavoro e alle applicazioni locali passando attraverso il cloud. Questo percorso offre agli utenti remoti la flessibilità necessaria per accedere ai carichi di lavoro distribuiti in Azure e in locale. Le aziende possono abilitare il servizio di accesso remoto sicuro basato sul cloud in una rete WAN virtuale di Azure.

### <a name="vnetvnet"></a>Transito da VNet a VNet con peering VNet

Per connettere reti virtuali l'uno all'altro per supportare applicazioni multilivello implementate in più reti virtuali, usare il peering VNet. Uno scenario di transito da VNet a VNet tramite la rete WAN virtuale di Azure non è attualmente supportato, ma si trova nella roadmap di Azure. La connessione di reti virtuali tramite il peering VNet è la soluzione consigliata per reti virtuali che devono essere connesse tra loro. 

### <a name="globalreach"></a>Copertura globale ExpressRoute

ExpressRoute è un modo privato e resiliente per connettere le reti locali al Microsoft Cloud. ExpressRoute Copertura globale è una funzionalità del componente aggiuntivo per ExpressRoute. Con Copertura globale è possibile collegare circuiti ExpressRoute insieme per creare una rete privata tra le reti locali. I rami connessi alla rete WAN virtuale di Azure con ExpressRoute richiedono che il ExpressRoute Copertura globale per comunicare tra loro.

In questo modello ogni ramo connesso all'hub WAN virtuale usando ExpressRoute può connettersi a reti virtuali usando il percorso da ramo a VNet. Il traffico da ramo a ramo non transiterà nell'hub perché ExpressRoute Copertura globale Abilita un percorso più ottimale sulla rete WAN di Azure.

## <a name="security"></a>Sicurezza e controllo dei criteri

L'hub di rete virtuale si interconnette e potenzialmente rileva tutto il traffico di transito. Può essere il posto di ospitare funzioni e servizi di rete centrale, ad esempio routing cloud, criteri di rete e sicurezza e controllo di accesso a Internet.

## <a name="next-steps"></a>Passaggi successivi

Creare una connessione utilizzando la rete WAN virtuale.

* [Connessioni da sito a sito tramite la rete WAN virtuale](virtual-wan-site-to-site-portal.md)
* [Connessioni ExpressRoute tramite la rete WAN virtuale](virtual-wan-expressroute-portal.md)
