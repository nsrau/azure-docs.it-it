---
title: Interconnettersi con la Cina tramite l'hub sicuro e la rete WAN virtuale di Azure
description: Informazioni su come interconnettersi con la Cina usando la rete WAN virtuale di Azure e un hub protetto.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: a92dafe6237d0f061f837f07c5dcf2686b1a8a7e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510736"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnettersi con la Cina tramite l'hub sicuro e la rete WAN virtuale di Azure

Quando si esaminano le aziende comuni di settore automobilistico, manifatturiero, logistico o di altro tipo, ad esempio le ambasciate, spesso si pone la domanda su come migliorare l'interconnessione con la Cina. Questi miglioramenti sono per lo più rilevanti per l'uso di servizi cloud come Microsoft 365, servizi globali di Azure o rami di interconnessione all'interno della Cina con una dorsale del cliente.

Nella maggior parte dei casi, i clienti riscontrano difficoltà relative a latenze elevate, larghezza di banda ridotta, connessione instabile e costi elevati per la connessione dalla Cina verso l'esterno, ad esempio l'Europa o gli Stati Uniti.

Uno dei motivi di queste difficoltà è dato dal "Grande Firewall cinese", che protegge la parte cinese di Internet e filtra il traffico verso la Cina. Quasi tutto il traffico dalla Cina continentale verso l'esterno, ad eccezione delle zone di amministrazione speciali come Hong Kong e Macao, passa attraverso il Grande Firewall. Il traffico che passa attraverso Hong Kong e Macao non tocca direttamente il Grande Firewall, ma viene gestito da un subset di tale firewall.

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="Il diagramma mostra l'interconnessione del provider.":::

Usando la rete WAN virtuale, un cliente può stabilire una connessione più efficiente e stabile ai servizi Microsoft Cloud e a una connessione alla rete aziendale senza infrangere la legge cinese sulla cybersecurity.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisiti e flusso di lavoro

Per agire in conformità alla legge cinese sulla cybersecurity, è necessario soddisfare una serie di condizioni.

Prima di tutto, è necessario collaborare con una rete e un ISP (Internet Service Provider, provider di servizi Internet) che possiede una licenza ICP (Internet Content Provider, provider di contenuti Internet) per la Cina. Nella maggior parte dei casi, ci si affiderà a uno dei provider seguenti:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

A seconda del provider e delle proprie esigenze, è ora necessario acquistare uno dei seguenti servizi di connettività di rete per interconnettere i vari rami all'interno della Cina.

* Rete MPLS/IPVPN 
* Rete WAN definita da software (SDWAN)
* Accesso a Internet dedicato

Successivamente, è necessario concordare con il provider una soluzione per ottenere un breakout alla rete globale Microsoft e alla relativa rete perimetrale a Hong Kong, non a Pechino o a Shanghai. In questo caso, Hong Kong è molto importante in considerazione della sua connessione fisica e della posizione rispetto alla Cina.

Anche se la maggior parte dei clienti pensa che Singapore offra la soluzione migliore per l'interconnessione, perché sulla mappa il paese sembra più vicino alla Cina, questo non è vero. Seguendo le mappe delle fibre di rete, si noterà che quasi tutte le connessioni di rete passano attraverso Pechino, Shanghai e Hong Kong. Hong Kong è quindi una scelta ottimale come base per l'interconnessione con la Cina.

A seconda del provider, è possibile ottenere diverse offerte di servizi. La tabella seguente illustra alcuni esempi di provider e il servizio offerto, in base alle informazioni disponibili al momento della scrittura di questo articolo.

| Service | Esempi di provider |
| --- | --- |
| Rete MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Accesso a Internet dedicato | PCCW, Hong Kong Telecom, China Mobil|

Con il provider è possibile concordare una delle due soluzioni seguenti da usare per raggiungere il backbone globale Microsoft:

* Ottenere una connessione Microsoft Azure ExpressRoute terminata a Hong Kong. In questo caso, verrebbe usata una rete MPLS/IPVPN. Attualmente, l'unico provider di licenze ICP con ExpressRoute per Hong Kong è China Telecom Global, che può comunque comunicare anche con gli altri provider sfruttando provider Cloud Exchange, ad esempio Megaport o InterCloud. Per altre informazioni, vedere [Provider di connettività ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Usare un accesso Internet dedicato direttamente presso uno dei seguenti Internet Exchange Point o tramite un'interconnessione di rete privata.

L'elenco seguente include gli Internet Exchange possibili a Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Quando si usa questa connessione, l'hop BGP successivo per i servizi Microsoft deve essere Microsoft Autonomous System Number (AS#) 8075. Se si usa una soluzione con singolo sito o SDWAN, questa sarebbe la connessione da preferire.

Con le modifiche attuali riguardanti le interconnessioni tra la Cina continentale e Hong Kong, la maggior parte di questi provider di rete crea un Bridge MPLS tra la Cina continentale e Hong Kong.

È possibile osservare che le connessioni VPN da sito a sito in Cina sono consentite e sono per lo più stabili. Lo stesso vale per le connessioni da sito a sito tra i rami nel resto del mondo. I provider ora creano un'aggregazione VPN/SDWAN su entrambi i lati e Bridge tramite MPLS tra di essi.

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="Il diagramma mostra il Bridge MPLS Cina.":::

In entrambi i casi, è comunque consigliabile avere un secondo e un normale Internet breakout in Cina continentale. Questo consente di suddividere il traffico tra il traffico aziendale e i servizi cloud come Microsoft 365 e Azure e il traffico Internet regolamentato per legge.

Un'architettura di rete conforme ai requisiti delle autorità cinesi potrebbe avere un aspetto simile al seguente:

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="Il diagramma mostra più rami.":::

In questo esempio, avendo un'interconnessione con la rete globale Microsoft a Hong Kong, è ora possibile iniziare a sfruttare l'[architettura di transito globale della rete WAN virtuale di Azure ](virtual-wan-global-transit-network-architecture.md) e servizi aggiuntivi, come l'hub sicuro della rete WAN virtuale di Azure, per utilizzare i servizi e interconnettersi con i rami e il data center al di fuori della Cina.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicazione da hub a hub

In questa sezione si userà la comunicazione da hub a hub della rete WAN virtuale per l'interconnessione. In questo scenario si crea una nuova risorsa Hub di rete WAN virtuale per connettersi a un hub di rete WAN virtuale a Hong Kong, ad altre aree preferite, a un'area in cui si dispone già di risorse di Azure o a una zona a cui si vuole connettersi.

Un'architettura di esempio potrebbe avere un aspetto simile al seguente:

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="Il diagramma mostra la rete WAN di esempio.":::

In questo esempio, i rami della Cina si connettono ad Azure Cloud China e tra di loro tramite connessioni VPN o MPLS. I rami che devono essere connessi ai servizi globali usano servizi basati su Internet o MPLS oppure collegati direttamente a Hong Kong. Se si vuole usare ExpressRoute sia a Hong Kong che nell'altra area, è necessario configurare [Copertura globale di ExpressRoute](../expressroute/expressroute-global-reach.md) per interconnettere entrambi i circuiti ExpressRoute.

Copertura globale di ExpressRoute non è disponibile in alcune aree. Se è necessario interconnettersi con il Brasile o l'India, ad esempio, occorre sfruttare [provider Cloud Exchange](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) per fornire i servizi di routing.

Nella figura seguente sono illustrati entrambi gli esempi per questo scenario.

:::image type="content" source="./media/interconnect-china/global.png" alt-text="Il diagramma mostra Copertura globale.":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Secure Internet breakout per Microsoft 365

Un'altra considerazione riguarda la sicurezza di rete, nonché la registrazione per il punto di ingresso tra la Cina, con il componente backbone stabilito dalla rete WAN virtuale, e il backbone del cliente. Nella maggior parte dei casi, è necessario stabilire il breakout a Internet a Hong Kong per raggiungere direttamente la rete perimetrale Microsoft e, con questa, i server Frontdoor di Azure usati per i servizi Microsoft 365.

Per entrambi gli scenari con la rete WAN virtuale, è possibile usare l'[hub protetto della rete WAN virtuale di Azure](../firewall-manager/secured-virtual-hub.md). Usando Gestione firewall di Azure è possibile modificare un normale hub di rete WAN virtuale in un hub protetto e quindi distribuire e gestire un firewall di Azure all'interno di tale hub.

La figura seguente illustra un esempio di questo scenario:

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="Il diagramma mostra Internet breakout per il traffico dei servizi Web e Microsoft.":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architettura e flussi di traffico

A seconda della scelta effettuata per la connessione a Hong Kong, l'architettura complessiva può variare leggermente. Questa sezione mostra tre architetture disponibili con una combinazione diversa di VPN o SDWAN e/o ExpressRoute.

Tutte queste opzioni usano l'hub protetto WAN virtuale di Azure per la connettività Direct Microsoft 365 in Hong Kong. Queste architetture supportano anche i requisiti di conformità per [Microsoft 365 la funzionalità multigeo](/microsoft-365/enterprise/microsoft-365-multi-geo) e mantengono il traffico vicino alla successiva posizione di Azure front door. Si ottiene così anche un miglioramento per l'uso di Microsoft 365 al di fuori della Cina.

Quando si usa una rete WAN virtuale di Azure con connessioni Internet, ogni connessione può trarre vantaggio da servizi aggiuntivi come il [Servizio di peering di Microsoft Azure (MAPS)](../peering-service/about.md), appositamente creato per ottimizzare il traffico verso la rete globale Microsoft da provider di servizi Internet di terze parti.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opzione 1: SDWAN o VPN

Questa sezione illustra un'architettura in cui vengono usate connessioni SDWAN o VPN verso Hong Kong e altri rami. L'opzione mostra il flusso del traffico quando si usa una connessione Internet pura in entrambi i siti del backbone WAN virtuale. In questo caso, la connessione viene portata a Hong Kong usando un accesso a Internet dedicato o una soluzione SDWAN del provider ICP. Altri rami usano anche soluzioni Internet pure o SDWAN.

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="Il diagramma mostra la Cina al traffico di Hong Kong.":::

In questa architettura, ogni sito è connesso alla rete globale Microsoft tramite una VPN e la rete WAN virtuale di Azure. Il traffico tra i siti e Hong Kong viene trasmesso attraverso la rete Microsoft e usa soltanto una normale connessione Internet nell'ultimo miglio.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opzione 2: ExpressRoute e SDWAN o VPN

Questa sezione illustra un'architettura in cui vengono usati il servizio ExpressRoute a Hong Kong e connessioni VPN/SDWAN per altri rami. L'opzione mostra l'uso della connessione ExpressRoute terminata a Hong Kong e altri rami connessi tramite SDWAN o VPN. A Hong Kong il servizio ExpressRoute è attualmente limitato a pochi provider, che è possibile trovare nell'elenco dei [partner ExpressRoute](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="Il diagramma mostra la Cina al traffico di Hong Kong-ExpressRoute.":::

Sono disponibili anche opzioni per terminare ExpressRoute dalla Cina, ad esempio, in Corea del Sud o in Giappone. Tuttavia, in considerazione dei requisiti di conformità, delle norme vigenti e della latenza, Hong Kong rappresenta attualmente la scelta migliore.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opzione 3: solo ExpressRoute

Questa sezione illustra un'architettura in cui viene usato il servizio ExpressRoute per Hong Kong e per altri rami. L'opzione mostra l'interconnessione tramite ExpressRoute a entrambe le estremità. In questo caso, il flusso di traffico è differente. Il traffico di Microsoft 365 viene propagato all'hub protetto della rete WAN virtuale di Azure e da quest'ultima alla rete perimetrale Microsoft e a Internet.

Il traffico diretto verso i rami interconnessi o da questi verso le varie sedi in Cina seguirà un approccio diverso all'interno dell'architettura. Attualmente la rete WAN virtuale non supporta il transito da ExpressRoute a ExpressRoute. Il traffico sfrutterà Copertura globale di ExpressRoute o l'interconnessione di terze parti senza passare attraverso l'hub di rete WAN virtuale. Il flusso passerà direttamente da un Microsoft Enterprise Edge (MSEE) a un altro.

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="Il diagramma mostra ExpressRoute Copertura globale.":::

Attualmente Copertura globale di ExpressRoute non è disponibile in tutti i paesi o aree geografiche, ma è possibile configurare una soluzione usando la rete WAN virtuale di Azure.

È possibile, ad esempio, configurare una connessione ExpressRoute con il peering Microsoft e connettere un tunnel VPN tramite tale peering alla rete WAN virtuale di Azure. In questo modo viene di nuovo abilitato il transito tra VPN ed ExpressRoute senza Copertura globale né provider e servizio di terze parti, ad esempio Megaport Cloud.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

* [Architettura della rete di transito globale - Rete WAN virtuale di Azure](virtual-wan-global-transit-network-architecture.md)

* [Creare un hub di rete WAN virtuale](virtual-wan-site-to-site-portal.md)

* [Configurare un hub protetto della rete WAN virtuale](../firewall-manager/secure-cloud-network.md)

* [Panoramica del Servizio di peering di Azure (anteprima)](../peering-service/about.md)