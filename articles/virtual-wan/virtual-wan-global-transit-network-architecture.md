---
title: Architettura di rete di transito globale rete WAN virtuale Azure | Microsoft Docs
description: Informazioni sull'architettura di rete di transito globale per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965974"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architettura di rete di transito globale e una rete WAN virtuale

Architettura di rete di transito globale viene adottato da alle aziende di consolidamento, connettere e controllare l'azienda moderna incentrate su cloud footprint IT. In un'organizzazione incentrata sul cloud moderna, il traffico di rete non dovrà essere backhauled alla sede centrale. Architettura di rete di transito globale si basa su concetti di rete comuni e nuovi concetti univoci per architetture basate su cloud e cloud.

![architettura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figura 1: Rete di transito globale con rete WAN virtuale**

Le aziende moderne richiedono la connettività universale tra hyper applicazioni distribuite a livello dei dati e gli utenti nel cloud e locali. Rete WAN virtuale Azure consente a un'architettura di rete di transito globale abilitando la connettività universale, any-to-any tra set distribuito a livello globale di reti virtuali, siti, applicazioni e utenti. Rete WAN virtuale Azure è un servizio gestito da Microsoft. Tutti i componenti di rete che questo servizio è composto ospitati e gestiti da Microsoft. Per altre informazioni sulla rete WAN virtuale, vedere la [Panoramica della rete WAN virtuale](virtual-wan-about.md) articolo.

Nell'architettura di Azure rete WAN virtuale, le aree di Azure è fungere da hub a cui è possibile scegliere di connettere i rami. Una volta connessi i rami, è possibile sfruttare il backbone di Azure per stabilire branch-to-VNet e, facoltativamente, la connettività al ramo di ramo.

Creazione di un singolo hub rete WAN virtuale nell'area che ha il maggior numero di spoke (rami, le reti virtuali, gli utenti) e quindi collegando gli spoke che si trovano in altre aree all'hub, è possibile stabilire una rete WAN virtuale. In alternativa, se gli spoke sono distribuiti geograficamente, è possibile anche creare un'istanza di hub a livello di area e gli hub di interconnessione. Gli hub fanno tutte parte della rete WAN virtuale stessa, ma possono essere associate a diversi criteri a livello di area.

## <a name="hub"></a>Transito hub e spoke

L'architettura di rete di transito globale si basa su un modello classico connettività hub-spoke in cui la rete ospitato nel cloud "hub" Abilita la connettività transitiva tra gli endpoint che può essere distribuita in diversi tipi di 'spoke'.
  
In questo modello può essere uno spoke:

* Rete virtuale (Vnet)
* Sito fisico branch
* Utente remoto
* Internet

![diagramma di transito globale dell'hub e spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figura 2: Hub e spoke**

Figura 2 mostra la visualizzazione logica della rete globale in cui utenti distribuiti geograficamente, siti fisici e le reti virtuali sono collegate tra loro tramite un hub di rete ospitato nel cloud. Questa architettura abilita la connettività di transito un hop logico tra gli endpoint di rete. Gli spoke sono connessi all'hub da vari servizi rete Azure, ad esempio VPN da sito o ExpressRoute da sito a rami fisico, VNet peering reti virtuali e VPN da punto a sito per gli utenti remoti.

## <a name="crossregion"></a>Connettività in più aree

Per un'azienda, un footprint cloud segue in genere l'impronta fisica. La maggior parte delle aziende accedere a cloud da un'area più vicina ai propri fisico del sito e gli utenti. Una delle entità chiave dell'architettura di rete globale è per abilitare la connettività tra più aree tra le entità di rete ed endpoint. Un footprint cloud può estendersi su più aree. Ciò significa che il traffico proveniente da un ramo che è connesso al cloud in un'area possa raggiungere un altro ramo o una rete virtuale in un'altra area usando la connettività hub nell'hub che è attualmente in anteprima.

## <a name="any"></a>Connettività Any-to-any

Architettura di rete di transito globale consente *connettività any-to-any* tramite un hub centrale di rete. Questa architettura si elimina o riduce la necessità di maglia completa o modelli di connettività di rete mesh parziale che sono più complessi per compilare e gestire. Inoltre, un controllo di routing nell'hub e spoke e le reti mesh è più facile da configurare e gestire.

Connettività Any-to-any, nel contesto di un'architettura globale, consente a un'azienda con utenti distribuiti a livello globale, i rami, i Data Center, le reti virtuali e applicazioni di connettersi tra loro tramite l'hub di transito. L'hub di transito funge da sistema di transito globale.

![percorsi del traffico](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figura 3: Percorsi di traffico di rete WAN virtuale**

Rete WAN virtuale Azure supporta i seguenti percorsi di connettività di transito globale. Figura 3 mappare le lettere tra parentesi.

* Ramo-to-VNet (a)  
* Ramo al ramo (b)
* Utente-a-rete virtuale remota (c)
* Utente-a-ramo remoto (d)
* VNet-VNet tramite peering di rete virtuale (e)
* Copertura globale di ExpressRoute 

### <a name="branchvnet"></a>Ramo-to-VNet

Ramo-to-VNet è il percorso principale supportato da Azure rete WAN virtuale. Questo percorso consente di connettersi i rami a carichi di lavoro aziendali IAAS di Azure distribuite in reti virtuali di Azure. I rami possono essere connesse alla rete WAN virtuale tramite ExpressRoute o VPN site-to-site. Transiti di traffico a reti virtuali connesse a hub di rete WAN virtuale tramite connessioni di rete virtuale.

### <a name="branchbranch"></a>Branch-a-branch

I rami possono essere connesse a un hub WAN virtuale di Azure usando i circuiti ExpressRoute e/o le connessioni VPN site-to-site. I branch è possibile connettersi all'hub di rete WAN virtuale presente nell'area più vicina al ramo.

Questa opzione consente alle grandi imprese di sfruttare il backbone di Azure per connettere i rami. Tuttavia, anche se questa funzionalità è disponibile, è opportuno valutare i vantaggi di connessione rami su WAN virtuale di Azure o usando una rete WAN privata.

### <a name="usertovnet"></a>Utente remoto-to-VNet

È possibile abilitare accesso remoto diretto e protetto in Azure usando connessioni da punto a sito da un client utente remoto a una rete WAN virtuale. Gli utenti remoti Enterprise non è più necessario hairpin al cloud usando una VPN azienda.

### <a name="usertobranch"></a>Utente al ramo remoto

Il percorso utente al ramo remoto consente agli utenti remoti che usano una connessione da punto a sito per i carichi di lavoro di accesso di Azure in locale e le applicazioni da in transito tramite il cloud. Questo percorso flessibilità agli utenti remoti per i carichi di lavoro di accesso che sono distribuiti in Azure sia in locale. Le aziende è possono abilitare il servizio centrale di accesso remoto sicuro e basato sul cloud in Azure rete WAN virtuale.

### <a name="vnetvnet"></a>VNet-to-VNet transito tramite il peering reti virtuali

Per connettere le reti virtuali tra loro per supportare le applicazioni multilivello che vengono implementate in più reti virtuali, usare il peering reti virtuali. Uno scenario di transito VNet a VNet tramite Azure rete WAN virtuale non è attualmente supportato, ma la roadmap di Azure. La connessione di reti virtuali tramite peering reti virtuali è la soluzione consigliata per le reti virtuali che devono essere connesse tra loro. [Il transito gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (nel contesto di peering reti virtuali) non è necessaria per la rete WAN virtuale perché rete WAN virtuale abilita automaticamente il transito gateway.

### <a name="globalreach"></a>Copertura globale di ExpressRoute

ExpressRoute è un modo privato e resiliente per connettere le reti locali nel cloud di Microsoft. Raggiungere globale ExpressRoute è una funzionalità aggiuntiva per ExpressRoute. Con raggiungere globale, è possibile collegare i circuiti ExpressRoute tra loro per rendere una rete privata tra le reti locali. Rami connesse a Azure rete WAN virtuale tramite ExpressRoute richiedono di ExpressRoute globale raggiungere comunicare tra loro.

In questo modello, ogni ramo che è connesso all'hub di rete WAN virtuale tramite ExpressRoute può connettersi alle reti virtuali tramite il percorso del ramo-to-VNet. Il traffico al ramo di ramo non transito nell'hub perché raggiungere globale ExpressRoute consente un percorso più ottimale su WAN di Azure.

## <a name="security"></a>Sicurezza e controllo dei criteri

L'hub di rete virtuale interconnessioni e potenzialmente vede tutto il traffico di transito. Può essere la posizione in cui le funzioni di rete centrale di host e i servizi come tale cloud routing, criteri di rete e sicurezza e controllo di accesso Internet.

## <a name="next-steps"></a>Passaggi successivi

Creare una connessione tramite rete WAN virtuale.

* [Connessioni Site-to-site tramite rete WAN virtuale](virtual-wan-site-to-site-portal.md)
* [Connessioni da punto a sito tramite rete WAN virtuale](virtual-wan-point-to-site-portal.md)
* [Connessioni ExpressRoute tramite rete WAN virtuale](virtual-wan-expressroute-portal.md)
