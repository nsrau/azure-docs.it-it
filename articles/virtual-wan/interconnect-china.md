---
title: Interagisci con la Cina usando la rete WAN virtuale di Azure e l'hub sicuro
description: Informazioni sulla connettività scalabile da ramo a ramo, sulle aree disponibili e sui partner.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985627"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnettersi con la Cina usando la rete WAN virtuale di Azure e l'hub sicuro

Quando si guarda a automotive comune, produzione, industrie logistiche o altri istituti come le ambasciate, c'è spesso la domanda su come migliorare l'interconnessione con la Cina. Tali miglioramenti sono per lo più rilevanti per l'utilizzo di servizi cloud come Office 365, Servizi globali di Azure o rami di interconnessione all'interno della Cina con una backbone del cliente.

Nella maggior parte dei casi, i clienti sono alle prese con latenze elevate, bassa larghezza di banda, connessione instabile e costi elevati di connessione al di fuori della Cina (ad esempio, Europa o Stati Uniti).

Una ragione di queste lotte è il "Grande Firewall della Cina", che protegge la parte cinese di Internet e filtra il traffico verso la Cina. Quasi tutto il traffico che corre dalla Cina continentale verso l'esterno della Cina, ad eccezione delle zone di amministrazione speciali come Hong Kong e Macao, passa il Great Firewall. Il traffico che attraversa Hong Kong e Macao non colpisce il Great Firewall in piena forza, è gestito da un sottoinsieme del Great Firewall.

![Interconnessione provider](./media/interconnect-china/provider.png)

Utilizzando la rete WAN virtuale, un cliente può stabilire una connessione più performante e stabile a Microsoft Cloud Services e una connessione alla rete aziendale senza infrangere la legge cinese sulla sicurezza informatica.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisiti e flusso di lavoro

Se si desidera mantenere la conformità alla legge cinese di sicurezza informatica, è necessario soddisfare una serie di determinate condizioni.

In primo luogo, è necessario collaborare con una rete e un ISP proprietario di una licenza ICP (Internet Content Provider) per la Cina. Nella maggior parte dei casi, si finirà con uno dei seguenti provider:

* China Telecom Global Ltd.
* China Mobile Ltd.
* Cina Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

A seconda del provider e delle esigenze, è ora necessario acquistare uno dei seguenti servizi di connettività di rete per interconnettere le filiali all'interno della Cina.

* Una rete MPLS/IPVPN 
* Una WAN definita dal software (SDWAN)
* Accesso a Internet dedicato

Successivamente, è necessario concordare con tale provider per dare un breakout per il Microsoft Global Network e la sua rete perimetrale a Hong Kong, non a Pechino o Shanghai. In questo caso, Hong Kong è molto importante a causa della sua connessione fisica e della posizione con la Cina.

Mentre la maggior parte dei clienti pensa che l'utilizzo di Singapore per l'interconnessione sia il caso migliore perché sembra più vicino alla Cina quando si guarda sulla mappa, questo non è vero. Quando si seguono le mappe delle fibre di rete, quasi tutte le connessioni di rete passano attraverso Pechino, Shanghai e Hong Kong. Questo rende Hong Kong una scelta migliore per interconnettersi con la Cina.

A seconda del provider, è possibile ottenere diverse offerte di servizi. La tabella seguente mostra un esempio di provider e il servizio che offrono, in base alle informazioni al momento della stesa di questo articolo.

| Service | Esempi di provider |
| --- | --- |
| Rete MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Accesso a Internet dedicato | PCCW, Hong Kong Telecom, Cina Mobil|

Con il provider, è possibile concordare quale delle due soluzioni seguenti da utilizzare per raggiungere la backbone globale Microsoft:

* Recupero di un Microsoft Azure ExpressRoute terminato a Hong Kong. Questo sarebbe il caso per l'uso di MPLS/IPVPN. Attualmente, solo l'unico provider di licenze ICP con ExpressRoute a Hong Kong è China Telecom Global. Tuttavia, possono anche parlare con gli altri provider se sfruttano i provider di scambio cloud come Megaport o InterCloud. Per altre informazioni, vedere Provider di [connettività ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).For more information, see ExpressRoute connectivity providers .

* Utilizzo di un accesso a Internet dedicato direttamente in uno dei seguenti punti Internet exchange o utilizzando un'interconnessione di rete privata.

L'elenco seguente mostra Internet Exchanges possibili a Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Quando si utilizza questa connessione, l'hop BGP successivo per i servizi Microsoft deve essere Microsoft Autonomous System Number (AS) 8075. Se si utilizza una singola posizione o una soluzione SDWAN, questa sarebbe la scelta della connessione.

In entrambi i casi, ti consigliamo comunque di avere un secondo e regolare Internet Breakout nella Cina continentale. In questo modo si suddivide il traffico tra il traffico aziendale e i servizi cloud come Microsoft 365 e Azure e il traffico Internet regolamentato per legge.

Un'architettura di rete conforme all'interno della Cina potrebbe essere simile all'esempio seguente:A compliant network architecture within China could look like the following example:

![Diramazioni multiple](./media/interconnect-china/multi-branch.png)

In questo esempio, avendo un'interconnessione con Microsoft Global Network a Hong Kong, è ora possibile iniziare a sfruttare l'architettura di transito [globale della rete WAN virtuale](virtual-wan-global-transit-network-architecture.md) di Azure e servizi aggiuntivi, ad esempio l'hub WAN virtuale sicuro di Azure, per utilizzare i servizi e interconnettere le filiali e il data center al di fuori della Cina.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicazione hub-hub

In questa sezione viene usata la comunicazione hub-hub WAN per l'interconnessione. In questo scenario si crea una nuova risorsa hub WAN virtuale per connettersi a un hub WAN virtuale a Hong Kong, altre aree preferite, un'area in cui si dispone già di risorse di Azure o in cui si desidera connettersi.

Un'architettura di esempio potrebbe essere simile all'esempio seguente:A sample architecture could look like following example:

![ESEMPIO WAN](./media/interconnect-china/sample.png)

In questo esempio, le filiali in Cina si connettono ad Azure Cloud China e si unialtro usando connessioni VPN o MPLS. Le filiali che devono essere connesse ai servizi globali utilizzano MPLS o servizi basati su Internet connessi direttamente a Hong Kong. Se si vuole usare ExpressRoute in Hong Kong e nell'altra area, è necessario configurare la copertura globale di ExpressRoute per interconnettere entrambi i circuiti ExpressRoute.If you want to use ExpressRoute in Hong Kong as well in the other region, you need to configure [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) to interconnect both ExpressRoute Circuits.

La copertura globale di ExpressRoute non è disponibile in alcune aree. Se è necessario interconnettersi con il Brasile o l'India, ad esempio, è necessario sfruttare [i provider](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) di Servizi di cloud per fornire i servizi di routing.

La figura seguente mostra entrambi gli esempi per questo scenario.

![Copertura globale](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Scarico Internet sicuro per Office 365

Un'altra considerazione è la sicurezza di rete, nonché la registrazione per il punto di ingresso tra la Cina e il componente backbone stabilito dalla rete WAN virtuale e la backbone del cliente. Nella maggior parte dei casi, è necessario eseguire il breakout di Internet a Hong Kong per raggiungere direttamente la rete perimetrale Microsoft e, con questo, i server front-frontati di Azure utilizzati per i servizi di Microsoft 365.

Per entrambi gli scenari con la rete WAN virtuale, è necessario sfruttare l'hub protetto WAN virtuale di [Azure.](../firewall-manager/secured-virtual-hub.md) Con Gestione firewall di Azure è possibile modificare un normale hub WAN virtuale in un hub protetto e quindi distribuire e gestire un firewall di Azure all'interno di tale hub.

Nella figura seguente viene illustrato un esempio di questo scenario:The following figure shows an example of this scenario:

![Breakout Internet per il traffico dei servizi Web e Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architettura e flussi di traffico

A seconda della scelta per quanto riguarda la connessione a Hong Kong, l'architettura complessiva può cambiare leggermente. In questa sezione vengono illustrate tre architetture disponibili in diverse combinazioni con VPN o SDWAN e/o ExpressRoute.

Tutte queste opzioni usano l'hub protetto della rete virtuale di Azure per la connettività M365 diretta a Hong Kong. Queste architetture supportano anche i requisiti di conformità per [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantengono il traffico vicino alla prossima posizione di Office 365 Front Door. Di conseguenza, è anche un miglioramento per l'utilizzo di Microsoft 365 fuori dalla Cina.

Quando si usa la rete WAN virtuale di Azure con le connessioni Internet, ogni connessione può trarre vantaggio da servizi aggiuntivi come [Microsoft Azure Peering Services (MAPS)](https://docs.microsoft.com/azure/peering-service/about). MAPS è stato creato per ottimizzare il traffico proveniente da Microsoft Global Network da provider di servizi Internet di terze parti.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opzione 1: SDWAN o VPN

In questa sezione viene illustrata una progettazione che utilizza SDWAN o VPN per Hong Kong e in altre filiali. Questa opzione mostra l'utilizzo e il flusso di traffico quando si utilizza una connessione Internet pura in entrambi i siti della backbone della rete WAN virtuale. In questo caso, la connessione viene portata a Hong Kong utilizzando l'accesso a Internet dedicato o una soluzione SDWAN del provider ICP. Anche altre filiali utilizzano soluzioni Internet o SDWAN pure.

![Traffico dalla Cina a Hong Kong](./media/interconnect-china/china-traffic.png)

In this architecture, every site is connected to the Microsoft Global Network by using VPN and Azure Virtual WAN. Il traffico tra i siti e Hong Kong viene trasmesso attraverso la rete Microsoft e utilizza solo regolare connessione Internet all'ultimo miglio.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opzione 2: ExpressRoute e SDWAN o VPNOption 2: ExpressRoute and SDWAN or VPN

In questa sezione viene illustrata una progettazione che usa ExpressRoute in Hong Kong e in altri rami con diramazioni VPN/SDWAN. Questa opzione mostra l'uso di ed ExpressRoute terminato a Hong Kong e ad altri rami connessi tramite SDWAN o VPN. ExpressRoute a Hong Kong è attualmente limitato a un breve elenco di provider, disponibile nell'elenco dei [partner Express Route](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Il traffico da Cina a Hong Kong ExpressRoute](./media/interconnect-china/expressroute.png)

Sono inoltre disponibili opzioni per terminare ExpressRoute dalla Cina, ad esempio in Corea del Sud o in Giappone. Ma, data la conformità, la regolamentazione e la latenza, Hong Kong è attualmente la scelta migliore.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opzione 3: Solo ExpressRouteOption 3: ExpressRoute only

In questa sezione viene illustrata una progettazione in cui ExpressRoute viene usato per Hong Kong e altri rami. Questa opzione mostra l'interconnessione usando ExpressRoute su entrambe le estremità. Qui si ha un flusso di traffico diverso rispetto all'altro. Il traffico di Microsoft 365 verrà trasportato all'hub protetto della rete WAN virtuale di Azure e da lì alla rete perimetrale Microsoft e a Internet.

Il traffico che va alle filiali interconnesse o da esse verso le sedi in Cina seguirà un approccio diverso all'interno di tale architettura. La rete WAN virtuale attualmente non supporta il transito da ExpressRoute a ExpressRoute.Currently virtual WAN does not support ExpressRoute to ExpressRoute transit. Il traffico sfrutterà la copertura globale ExpressRoute o l'interconnessione di terze parti senza passare l'hub WAN virtuale. Il flusso verrà evitato direttamente da Microsoft Enterprise Edge (MSEE) a un altro.

![Copertura globale di ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Attualmente La copertura globale ExpressRoute non è disponibile in tutti i paesi, ma è possibile configurare una soluzione usando la rete WAN virtuale di Azure.Currently ExpressRoute Global Reach is not available in every country, but you can configure a solution using Azure Virtual WAN.

È possibile, ad esempio, configurare un ExpressRoute con microsoft Peering e connettere un tunnel VPN tramite tale peering alla rete WAN virtuale di Azure.You can, for example, configure an ExpressRoute with Microsoft Peering and connect a VPN tunnel through that peering to Azure Virtual WAN. Ora è stato abilitato, ancora una volta, il transito tra VPN ed ExpressRoute senza Global Reach e provider e servizio di terze parti, ad esempio Megaport Cloud.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

* [Architettura della rete di transito globale con la rete WAN virtuale di AzureGlobal Transit network architecture with Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Creare un hub WAN virtualeCreate a Virtual WAN hub](virtual-wan-site-to-site-portal.md)

* [Configurare un hub protetto con WAN virtuale](../firewall-manager/secure-cloud-network.md)

* [Panoramica dell'anteprima del servizio di peering di AzureAzure Peering Service Preview Overview](https://docs.microsoft.com/azure/peering-service/about)
