---
title: Architettura della rete di transito globale WAN virtuale di Azure | Microsoft Docs
description: Informazioni sull'architettura di rete di transito globale per la rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 9b736e897278c4656df83c30388efc812ac6193d
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607374"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architettura di rete di transito globale e rete WAN virtuale

Le aziende moderne richiedono una connettività onnipresente tra le applicazioni, i dati e gli utenti Hyper-distribuiti nel cloud e in locale. L'architettura di rete di transito globale è stata adottata dalle aziende per consolidare, connettere e controllare il footprint aziendale moderno e globale incentrato sul cloud.

L'architettura di rete di transito globale è basata su un modello di connettività hub-spoke classico in cui la rete ospitata nel cloud ' hub ' consente la connettività transitiva tra gli endpoint che possono essere distribuiti tra tipi diversi di "spoke".

In questo modello, un spoke può essere:
* Rete virtuale (reti virtuali)
* Sito del ramo fisico
* Utente remoto
* Internet

![Hub e spoke](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: rete Hub di transito globale e spoke**

Nella figura 1 viene illustrata la visualizzazione logica della rete di transito globale in cui utenti distribuiti geograficamente, siti fisici e reti virtuali vengono interconnessi tramite un hub di rete ospitato nel cloud. Questa architettura consente la connettività di transito a un hop logico tra gli endpoint di rete.

## <a name="globalnetworktransit"></a>Rete di transito globale con rete WAN virtuale

WAN virtuale di Azure è un servizio di rete cloud gestito da Microsoft. Tutti i componenti di rete di cui questo servizio è composto sono ospitati e gestiti da Microsoft. Per altre informazioni sulla rete WAN virtuale, vedere l'articolo [Panoramica della rete WAN virtuale](virtual-wan-about.md) .

La rete WAN virtuale di Azure consente un'architettura di rete di transito globale abilitando connettività universale e any-to-any tra set distribuiti a livello globale di carichi di lavoro cloud in reti virtuali, siti di filiali, applicazioni SaaS e PaaS e utenti.

![Rete WAN virtuale di Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: rete di transito globale e rete WAN virtuale**

Nell'architettura WAN virtuale di Azure viene eseguito il provisioning di hub WAN virtuali in aree di Azure, in cui è possibile scegliere di connettere i rami, reti virtuali e gli utenti remoti. I siti dei rami fisici sono connessi all'hub da ExpressRoute Premium o VPN da sito a sito, reti virtuali sono connessi all'hub da connessioni VNet e gli utenti remoti possono connettersi direttamente all'hub usando la VPN utente (VPN da punto a sito). La rete WAN virtuale supporta anche la connessione VNet tra aree in cui un VNet in un'area può essere connesso a un hub WAN virtuale in un'area diversa.

È possibile stabilire una rete WAN virtuale creando un singolo hub WAN virtuale nell'area con il maggior numero di spoke (Branch, reti virtuali, utenti) e quindi connettendo i spoke che si trovano in altre aree all'hub. Si tratta di un'opzione ideale quando un footprint aziendale è prevalentemente in un'area con alcuni spoke remoti.  
  
## <a name="hubtohub"></a>Connettività da Hub a hub

Un footprint cloud aziendale può estendersi su più aree cloud ed è ottimale (latenza) per accedere al cloud da un'area più vicina al sito fisico e agli utenti. Uno dei principi chiave dell'architettura di rete di transito globale è quello di abilitare la connettività tra aree tra tutti gli endpoint di rete cloud e locali. Ciò significa che il traffico da un ramo connesso al cloud in un'area può raggiungere un altro ramo o una VNet in un'area diversa tramite la connettività da Hub a hub abilitata dalla [rete globale di Azure](https://azure.microsoft.com/global-infrastructure/global-network/).

![tra aree](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: connettività WAN virtuale tra aree**

Quando più hub sono abilitati in una singola rete WAN virtuale, gli hub vengono automaticamente interconnessi tramite collegamenti da Hub a hub, abilitando così la connettività globale tra i rami e reti virtuali distribuiti in più aree. 

Inoltre, gli hub che fanno parte della stessa rete WAN virtuale possono essere associati a criteri di accesso e di sicurezza a livello di area diversi. Per ulteriori informazioni, vedere [sicurezza e controllo dei criteri](#security) più avanti in questo articolo.

## <a name="anytoany"></a>Connettività any-to-any

L'architettura di rete di transito globale consente la connettività any-to-any tramite hub WAN virtuali. Questa architettura Elimina o riduce la necessità di una connettività di rete completa o mesh parziale tra i spoke, più complessi da compilare e gestire. Inoltre, il controllo di routing nelle reti hub e spoke e rete mesh è più semplice da configurare e gestire.

La connettività any-to-any (nel contesto di un'architettura globale) consente a un'azienda con utenti, rami, Data Center, reti virtuali e applicazioni distribuite a livello globale di connettersi tra loro attraverso gli hub di transito. La rete WAN virtuale di Azure funge da sistema di transito globale.

![Any per qualsiasi](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: percorsi di traffico della rete WAN virtuale**

La rete WAN virtuale di Azure supporta i seguenti percorsi di connettività di transito globali. Le lettere tra parentesi vengono mappate alla figura 4.

* Branch-to-VNet (a)
* Ramo a ramo (b)
  * ExpressRoute Copertura globale e WAN virtuale
* Utente remoto-VNet (c)
* Remote User-to-Branch (d)
* Da VNet a VNet (e)
* Da ramo a hub-hub a ramo (f)
* Da ramo a hub-hub a VNet (g)
* Da VNet a hub-hub a VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Da ramo a VNet (a) e da ramo a VNet tra aree (g)

Branch-to-VNet è il percorso primario supportato dalla rete WAN virtuale di Azure. Questo percorso consente di connettere i rami ai carichi di lavoro aziendali di Azure IAAS distribuiti in Azure reti virtuali. È possibile connettere i rami alla rete WAN virtuale tramite ExpressRoute o VPN da sito a sito. Il traffico viene transitato verso reti virtuali connesse agli hub WAN virtuali tramite connessioni VNet. Il [transito del gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) esplicito non è necessario per la rete WAN virtuale perché la rete WAN virtuale Abilita automaticamente il transito al sito di succursale. Vedere l'articolo sui [partner WAN virtuali](virtual-wan-configure-automation-providers.md) per informazioni su come connettere un CPE SD-WAN alla rete WAN virtuale.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Copertura globale e WAN virtuale

ExpressRoute è un modo privato e resiliente per connettere le reti locali al Microsoft Cloud. La rete WAN virtuale supporta le connessioni del circuito Express route. La connessione di un circuito di un sito a WAN virtuale con Express Route richiede 1) circuito Premium 2) perché si trovi in una posizione Copertura globale abilitata.

ExpressRoute Copertura globale è una funzionalità del componente aggiuntivo per ExpressRoute. Con Copertura globale è possibile collegare circuiti ExpressRoute insieme per creare una rete privata tra le reti locali. I rami connessi alla rete WAN virtuale di Azure con ExpressRoute richiedono che il ExpressRoute Copertura globale per comunicare tra loro.

In questo modello ogni ramo connesso all'hub WAN virtuale usando ExpressRoute può connettersi a reti virtuali usando il percorso da ramo a VNet. Il traffico da ramo a ramo non transiterà nell'hub perché ExpressRoute Copertura globale Abilita un percorso più ottimale sulla rete WAN di Azure.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Da ramo a ramo (b) e da ramo a ramo tra aree (f)

I rami possono essere connessi a un hub WAN virtuale di Azure usando circuiti ExpressRoute e/o connessioni VPN da sito a sito. È possibile connettere i rami all'hub WAN virtuale che si trova nell'area più vicina al ramo.

Questa opzione consente alle aziende di sfruttare la backbone di Azure per connettere i rami. Tuttavia, anche se questa funzionalità è disponibile, è necessario valutare i vantaggi della connessione dei rami sulla rete WAN virtuale di Azure rispetto all'uso di una rete WAN privata.  

### <a name="remote-user-to-vnet-c"></a>Utente remoto-VNet (c)

È possibile abilitare l'accesso remoto diretto e sicuro ad Azure tramite una connessione da punto a sito da un client utente remoto a una rete WAN virtuale. Gli utenti remoti aziendali non devono più eseguire il tornamento al cloud usando una VPN aziendale.

### <a name="remote-user-to-branch-d"></a>Remote User-to-Branch (d)

Il percorso remoto da utente a ramo consente agli utenti remoti che usano una connessione da punto a sito ad Azure di accedere ai carichi di lavoro e alle applicazioni locali passando attraverso il cloud. Questo percorso offre agli utenti remoti la flessibilità necessaria per accedere ai carichi di lavoro distribuiti in Azure e in locale. Le aziende possono abilitare il servizio di accesso remoto sicuro basato sul cloud in una rete WAN virtuale di Azure.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-to-VNet Transit (e) e da VNet a VNet tra aree (h)

Il transito da VNet a VNet consente a reti virtuali di connettersi tra loro per collegare applicazioni multilivello implementate in più reti virtuali. Facoltativamente, è possibile connettere reti virtuali tra loro tramite il peering VNet e questo potrebbe essere adatto per alcuni scenari in cui il transito tramite l'hub VWAN non è necessario.

## <a name="security"></a>Sicurezza e controllo dei criteri

Gli hub WAN virtuali di Azure interconnettono tutti gli endpoint di rete attraverso la rete ibrida e potenzialmente visualizzano tutto il traffico di rete di transito. Gli hub WAN virtuali possono essere convertiti in hub virtuali protetti distribuendo il firewall di Azure all'interno degli hub VWAN per abilitare la sicurezza basata sul cloud, l'accesso e il controllo dei criteri. L'orchestrazione di firewall di Azure negli hub WAN virtuali può essere eseguita da gestione firewall di Azure.

[Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) offre le funzionalità per gestire e ridimensionare la sicurezza per le reti di transito globali. Azure Firewall Manager offre la possibilità di gestire in modo centralizzato il routing, la gestione dei criteri globali, i servizi di sicurezza Internet avanzati tramite terze parti insieme al firewall di Azure.

![hub virtuale protetto con il firewall di Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: hub virtuale protetto con il firewall di Azure**

Il firewall di Azure per la rete WAN virtuale supporta i seguenti percorsi di connettività di transito protetti globali. Le lettere tra parentesi vengono mappate alla figura 5.

* Transito sicuro da VNet a VNet (e)
* Servizio di sicurezza da VNet a Internet o di terze parti (i)
* Da ramo a Internet o da un servizio di sicurezza di terze parti (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Transito protetto da VNet a VNet (e)

Il transito protetto da VNet a VNet consente a reti virtuali di connettersi tra loro tramite il firewall di Azure nell'hub WAN virtuale.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Servizio di sicurezza da VNet a Internet o di terze parti (i)

Il transito protetto da VNet a Internet o di terze parti consente a reti virtuali di connettersi a Internet o a servizi di sicurezza di terze parti supportati tramite il firewall di Azure nell'hub WAN virtuale.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Da ramo a Internet o da un servizio di sicurezza di terze parti (j)
Il transito sicuro da ramo a Internet o di terze parti consente ai Branch di connettersi a Internet o a servizi di sicurezza di terze parti supportati tramite il firewall di Azure nell'hub WAN virtuale.

## <a name="next-steps"></a>Passaggi successivi

Creare una connessione usando la rete WAN virtuale e distribuire il firewall di Azure negli hub VWAN.

* [Connessioni da sito a sito tramite la rete WAN virtuale](virtual-wan-site-to-site-portal.md)
* [Connessioni ExpressRoute tramite la rete WAN virtuale](virtual-wan-expressroute-portal.md)
* [Gestione firewall di Azure per la distribuzione di FW di Azure in VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
