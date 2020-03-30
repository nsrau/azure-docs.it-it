---
title: 'Architettura: Architettura della rete di transito globale'
titleSuffix: Azure Virtual WAN
description: Informazioni sull'architettura della rete di transito globale per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064972"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architettura di rete di transito globale e WAN virtuale

Le aziende moderne richiedono una connettività onnipresente tra applicazioni, dati e utenti iperdistribuiti nel cloud e in locale. L'architettura di rete di transito globale viene adottata dalle aziende per consolidare, connettere e controllare l'impronta AZIENDALE aziendale moderna e globale incentrata sul cloud.

L'architettura della rete di transito globale si basa su un modello di connettività hub-and-spoke classico in cui l'hub della rete ospitata nel cloud consente la connettività transitiva tra endpoint che possono essere distribuiti tra diversi tipi di "spoke".

In questo modello, un raggio può essere:
* Rete virtuale (VNet)
* Sito di succursale fisico
* Utente remoto
* Internet

![hub e ha parlato](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: rete hub-and-spoke di transito globale**

Nella Figura 1 viene illustrata la visualizzazione logica della rete di transito globale in cui gli utenti distribuiti geograficamente, i siti fisici e le reti virtuali sono interconnessi tramite un hub di rete ospitato nel cloud. Questa architettura consente la connettività di transito virtuale con un solo hop tra gli endpoint di rete.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Rete di transito globale con WAN virtuale

La rete WAN virtuale di Azure è un servizio di cloud networking gestito da Microsoft.Azure Virtual WAN is a Microsoft-managed cloud networking service. Tutti i componenti di rete di cui è composto questo servizio sono ospitati e gestiti da Microsoft. Per ulteriori informazioni sulla rete WAN virtuale, vedere l'articolo [Panoramica della rete WAN virtuale.](virtual-wan-about.md)

La rete WAN virtuale di Azure consente un'architettura di rete di transito globale consentendo una connettività onnipresente e any-to-any tra set di carichi di lavoro cloud distribuiti a livello globale in reti virtuali, siti di succursale, applicazioni SaaS e PaaS e utenti.

![Rete WAN virtuale di Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: rete di transito globale e WAN virtuale**

Nell'architettura della rete WAN virtuale di Azure viene eseguito il provisioning degli hub WAN virtuali nelle aree di Azure, a cui è possibile scegliere di connettere le filiali, le reti virtuali e gli utenti remoti. I siti di succursale fisici sono connessi all'hub tramite Premium ExpressRoute o VPN da sito a sito, le reti virtuali sono connesse all'hub tramite connessioni della rete virtuale e gli utenti remoti possono connettersi direttamente all'hub tramite VPN utente (VPN da punto a sito). La rete WAN virtuale supporta anche una connessione a rete virtuale tra aree in cui una rete virtuale in un'area può essere connessa a un hub WAN virtuale in un'area diversa.

È possibile stabilire una rete WAN virtuale creando un singolo hub WAN virtuale nell'area con il maggior numero di spoke (filiali, reti virtuali, utenti) e quindi connettendo gli spoke presenti in altre aree all'hub. Questa è una buona opzione quando un'impronta aziendale è per lo più in un'area con alcuni raggi remoti.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Connettività hub-hub

Un footprint cloud aziendale può estendersi su più aree cloud ed è ottimale (in base alla latenza) per accedere al cloud da un'area più vicina al sito fisico e agli utenti. Uno dei principi fondamentali dell'architettura della rete di transito globale consiste nell'abilitare la connettività tra aree tra tutti gli endpoint di rete cloud e locali. Ciò significa che il traffico proveniente da un ramo connesso al cloud in un'area può raggiungere un'altra filiale o una rete virtuale in un'area diversa usando la connettività hub-hub abilitata da Rete globale di [Azure.](https://azure.microsoft.com/global-infrastructure/global-network/)

![regione](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: Connettività tra aree WAN virtuali**

Quando più hub sono abilitati in una singola rete WAN virtuale, gli hub vengono automaticamente interconnessi tramite collegamenti hub-hub, consentendo in tal modo la connettività globale tra filiali e reti virtuali distribuite tra più aree. 

Inoltre, gli hub che fanno tutti parte della stessa rete WAN virtuale possono essere associati a criteri di sicurezza e accesso regionali diversi. Per altre informazioni, vedere [Sicurezza e controllo dei criteri](#security) più avanti in questo articolo.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Connettività any-to-any

L'architettura della rete di transito globale consente una connettività any-to-any tramite hub WAN virtuali. Questa architettura elimina o riduce la necessità di connettività mesh completa o parziale tra i raggi, che sono più complessi da creare e gestire. Inoltre, il controllo del routing nelle reti hub-and-spoke e mesh è più facile da configurare e gestire.

La connettività any-to-any (nel contesto di un'architettura globale) consente a un'azienda con utenti, filiali, data center, reti virtuali e applicazioni distribuite a livello globale di connettersi tra loro tramite gli hub di transito. La rete WAN virtuale di Azure funge da sistema di transito globale.

![qualsiasi a qualsiasi](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: Percorsi del traffico WAN virtuale**

La rete WAN virtuale di Azure supporta i percorsi di connettività del transito globale seguenti. Le lettere tra parentesi vengono mappate alla figura 4.

* Da ramo a rete virtuale (a)
* Da ramo a ramo (b)
  * Copertura globale ExpressRoute e WAN virtuale
* Da utente a rete virtuale remoto (c)
* Remoto da utente a ramo (d)
* Da VNet a VNet (e)
* Da ramo a hub-hub-hub-da-ramo a ramo (f)
* Da ramo a hub-hub-hub-da-vNet (g)
* Da VNet a hub-hub-hub-a-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Da ramo a rete (a) e da ramo a area virtuale (g)

Da succursale a VNet è il percorso principale supportato dalla rete WAN virtuale di Azure.Branch-to-VNet is the primary path supported by Azure Virtual WAN. Questo percorso consente di connettere le filiali ai carichi di lavoro aziendali di Azure IAAS distribuiti nelle reti virtuali di Azure.This path allows you to connect branches to Azure IAAS enterprise workloads that are deployed in Azure VNets. Le filiali possono essere connesse alla rete WAN virtuale tramite ExpressRoute o VPN da sito a sito. Il traffico transita verso reti virtuali connesse agli hub WAN virtuali tramite Connessioni di rete virtuale. Il [transito esplicito del gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) non è necessario per la rete WAN virtuale perché la rete WAN virtuale abilita automaticamente il transito del gateway al sito di succursale. Vedere l'articolo [Virtual WAN Partners](virtual-wan-configure-automation-providers.md) su come connettere un CPE SD-WAN alla rete WAN virtuale.

### <a name="expressroute-global-reach-and-virtual-wan"></a>Copertura globale ExpressRoute e WAN virtuale

ExpressRoute is a private and resilient way to connect your on-premises networks to the Microsoft Cloud. La rete WAN virtuale supporta le connessioni ai circuiti Express Route. La connessione di un sito di succursale alla rete WAN virtuale con Express Route richiede 1) Circuito Premium Circuit 2) per trovarsi in una posizione abilitata per la copertura globale.

ExpressRoute Global Reach is an add-on feature for ExpressRoute. Con Global Reach è possibile collegare i circuiti ExpressRoute per creare una rete privata tra le reti locali. I rami connessi alla rete WAN virtuale di Azure tramite ExpressRoute richiedono la copertura globale ExpressRoute per comunicare tra loro.

In questo modello, ogni ramo connesso all'hub WAN virtuale tramite ExpressRoute può connettersi alle reti virtuali usando il percorso da ramo a vNet. Il traffico da succursale a ramo non transiterà nell'hub perché la copertura globale ExpressRoute consente un percorso più ottimale sulla rete WAN di Azure.Branch-to-branch traffic don't transit the hub because ExpressRoute Global Reach enables a more optimal path over Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Da ramo a ramo (b) e tra aree da ramo a ramo (f)

Le filiali possono essere connesse a un hub WAN virtuale di Azure usando circuiti ExpressRoute e/o connessioni VPN da sito a sito. È possibile connettere i rami all'hub WAN virtuale che si trova nell'area più vicina alla succursale.

Questa opzione consente alle aziende di sfruttare la backbone di Azure per connettere i rami. Tuttavia, anche se questa funzionalità è disponibile, è consigliabile valutare i vantaggi della connessione di rami tramite la rete WAN virtuale di Azure rispetto all'uso di una rete WAN privata.  

### <a name="remote-user-to-vnet-c"></a>Da utente a rete virtuale remoto (c)

È possibile abilitare l'accesso remoto diretto e sicuro ad Azure usando la connessione da punto a sito da un client utente remoto a una rete WAN virtuale. Gli utenti remoti aziendali non devono più aggiungere un tornante al cloud utilizzando una VPN aziendale.

### <a name="remote-user-to-branch-d"></a>Remoto da utente a ramo (d)

Il percorso da utente a succursale remoto consente agli utenti remoti che utilizzano una connessione da punto a sito per accedere ad Azure carichi di lavoro e applicazioni locali transitando nel cloud. Questo percorso offre agli utenti remoti la flessibilità necessaria per accedere ai carichi di lavoro distribuiti sia in Azure che in locale. Le aziende possono abilitare il servizio di accesso remoto sicuro basato su cloud centrale nella rete WAN virtuale di Azure.Enterprises can enable central cloud-based secure remote access service in Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Transito da VNet a VNet (e) e da VNet a VNet tra aree (h)

Il transito da VNet a VNet consente alle reti virtuali di connettersi tra loro per interconnettere applicazioni multilivello implementate tra più reti virtuali. Facoltativamente, è possibile connettere le reti virtuali tra loro tramite peering di rete virtuale e questo può essere adatto per alcuni scenari in cui il transito tramite l'hub VWAN non è necessario.

## <a name="security-and-policy-control"></a><a name="security"></a>Sicurezza e controllo dei criteri

Gli hub della rete WAN virtuale di Azure interconnettono tutti gli endpoint di rete nella rete ibrida e potenzialmente vedono tutto il traffico di rete di transito. Gli hub WAN virtuali possono essere convertiti in hub virtuali protetti distribuendo Firewall di Azure all'interno di hub VWAN per abilitare la sicurezza basata su cloud, l'accesso e il controllo dei criteri. L'orchestrazione dei firewall di Azure negli hub WAN virtuale può essere eseguita da Gestione firewall di Azure.Orchestration of Azure Firewalls in virtual WAN hubs can be performed by Azure Firewall Manager.

[Gestione firewall](https://go.microsoft.com/fwlink/?linkid=2107683) di Azure offre le funzionalità per gestire e scalare la sicurezza per le reti di transito globali. Gestione firewall di Azure offre la possibilità di gestire centralmente il routing, la gestione dei criteri globali e i servizi avanzati di sicurezza Internet tramite terze parti insieme a Firewall di Azure.Azure Firewall Manager provides ability to centrally manage routing, global policy management, advanced Internet security services via third-party with the Azure Firewall.

![hub virtuale protetto con Firewall di Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: Hub virtuale protetto con Firewall di AzureFigure 5: Secured virtual hub with Azure Firewall**

Firewall di Azure nella rete WAN virtuale supporta i percorsi di connettività di transito protetta globali seguenti. Le lettere tra parentesi vengono mappate alla figura 5.

* Transito sicuro da VNet a VNet (e)
* Servizio di sicurezza da VNet a Internet o da terze parti (i)
* Servizio di sicurezza da una filiale a Internet o da terze parti (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Transito protetto da VNet a VNet (e)

Il transito protetto da VNet a VNet consente alle reti virtuali di connettersi tra loro tramite il firewall di Azure nell'hub WAN virtuale.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Servizio di sicurezza da VNet a Internet o da terze parti (i)

Il transito protetto da rete a Internet o da terze parti consente alle reti virtuali di connettersi a Internet o a un servizio di sicurezza di terze parti supportato tramite il firewall di Azure nell'hub WAN virtuale.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Servizio di sicurezza da una filiale a Internet o da terze parti (j)
Il transito sicuro da succursale a Internet o da terze parti consente alle filiali di connettersi a Internet o a un servizio di sicurezza di terze parti supportato tramite il firewall di Azure nell'hub WAN virtuale.

## <a name="next-steps"></a>Passaggi successivi

Creare una connessione usando la rete WAN virtuale e distribuire Firewall di Azure negli hub VWAN.

* [Connessioni da sito a sito tramite WAN virtuale](virtual-wan-site-to-site-portal.md)
* [Connessioni ExpressRoute con WAN virtuale](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager to Deploy Azure FW in VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
