---
title: Interconnettersi con la Cina tramite la rete WAN virtuale di Azure e l'hub sicuro
description: Informazioni sulla connettività scalabile da ramo a ramo, sulle aree disponibili e sui partner.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985627"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnettersi con la Cina tramite la rete WAN virtuale di Azure e l'hub sicuro

Quando si esaminano le aziende comuni di settore automobilistico, manifatturiero, logistico o di altro tipo, ad esempio le ambasciate, spesso si pone la domanda su come migliorare l'interconnessione con la Cina. Questi miglioramenti sono per lo più rilevanti per l'uso di servizi cloud come Office 365, servizi globali di Azure o rami di interconnessione all'interno della Cina con una dorsale del cliente.

Nella maggior parte dei casi, i clienti faticano a latenze elevate, larghezza di banda ridotta, connessione instabile e costi elevati per la connessione all'esterno della Cina, ad esempio Europa o il Stati Uniti.

Un motivo di queste lotte è il "ottimo firewall della Cina", che protegge la parte cinese di Internet e filtra il traffico verso la Cina. Quasi tutto il traffico eseguito dalla Cina continentale all'esterno della Cina, eccetto le zone di amministrazione speciali come Hong Kong e Macao, passa il grande firewall. Il traffico eseguito attraverso Hong Kong e Macao non raggiunge il grande firewall con la massima forzatura, ma viene gestito da un sottoinsieme del grande firewall.

![Interconnessione provider](./media/interconnect-china/provider.png)

Grazie alla rete WAN virtuale, un cliente può stabilire una connessione più efficiente e stabile ai servizi Microsoft Cloud e a una connessione alla rete aziendale senza compromettere la legge Cybersecurity cinese.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisiti e flusso di lavoro

Se si desidera mantenere la conformità alla legge Cybersecurity cinese, è necessario soddisfare una serie di determinate condizioni.

In primo luogo, è necessario collaborare con una rete e un ISP che possiede una licenza ICP (provider di contenuti Internet) per la Cina. Nella maggior parte dei casi, si otterrà uno dei provider seguenti:

* China Telecom Global Ltd.
* Cina Mobile Ltd.
* Cina Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

A seconda del provider e delle esigenze, è ora necessario acquistare uno dei seguenti servizi di connettività di rete per collegare i rami in Cina.

* Rete MPLS/IPVPN 
* Una rete WAN definita dal software (SDWAN)
* Accesso a Internet dedicato

A questo punto, è necessario accettare il provider per fornire un breakout alla rete globale Microsoft e alla relativa periferia Rete in ingresso Hong Kong, non a Pechino o a Shanghai. In questo caso, Hong Kong è molto importante a causa della connessione fisica e della località alla Cina.

Sebbene la maggior parte dei clienti pensi a usare Singapore per l'interconnessione, è il caso migliore perché sembra più vicino alla Cina quando si esamina la mappa, questo non è vero. Quando si seguono i mapping fibre di rete, quasi tutte le connessioni di rete passano attraverso Beijing, Shanghai e Hong Kong. In questo modo, Hong Kong è una scelta ottimale per l'interconnessione alla Cina.

A seconda del provider, è possibile ottenere diverse offerte di servizio. La tabella seguente illustra un esempio di provider e il servizio offerti, in base alle informazioni al momento della scrittura di questo articolo.

| Servizio | Esempi di provider |
| --- | --- |
| Rete MPLS/IPVPN |PCCW, Cina Telecom globale |
|SDWAN| PCCW, Cina Telecom globale|
| Accesso a Internet dedicato | PCCW, Hong Kong Telecom, Cina Mobil|

Con il provider è possibile accettare le due soluzioni seguenti da usare per raggiungere la backbone globale Microsoft:

* Ottenere una Microsoft Azure ExpressRoute terminata a Hong Kong. Questo è il caso per l'uso di MPLS/IPVPN. Attualmente, solo il provider di licenze ICP con ExpressRoute per Hong Kong è Global Telecom per la Cina. Tuttavia, possono anche comunicare con gli altri provider se sfruttano i provider di cloud Exchange, ad esempio Megaport o intercloud. Per ulteriori informazioni, vedere [provider di connettività ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Uso di un accesso Internet dedicato direttamente a uno dei seguenti punti di Internet Exchange o tramite un'interconnessione di rete privata.

L'elenco seguente Mostra gli scambi Internet possibili in Hong Kong:

* AMS-IX Hong Kong
* BBIX di Hong Kong
* Equinix di Hong Kong
* HKIX

Quando si usa questa connessione, l'hop BGP successivo per i servizi Microsoft deve essere il numero di sistema autonomo Microsoft (#) 8075. Se si usa un'unica posizione o una soluzione SDWAN, questa sarà la scelta della connessione.

In entrambi i casi, è comunque consigliabile avere un secondo e un normale Internet breakout in Cina continentale. Questo consente di suddividere il traffico tra il traffico aziendale e i servizi cloud come Microsoft 365 e Azure e il traffico Internet regolamentato per legge.

Un'architettura di rete conforme in Cina potrebbe avere un aspetto simile all'esempio seguente:

![Più rami](./media/interconnect-china/multi-branch.png)

In questo esempio, con un'interconnessione con Microsoft Global Rete in ingresso Hong Kong, è ora possibile iniziare a sfruttare l' [architettura di transito globale WAN di Azure](virtual-wan-global-transit-network-architecture.md) e i servizi aggiuntivi, come l'hub WAN virtuale sicuro di Azure, per utilizzare i servizi e l'interconnessione ai rami e al Data Center al di fuori della Cina.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicazione da Hub a hub

In questa sezione si userà la comunicazione da Hub a hub WAN virtuale a Interconnect. In questo scenario si crea una nuova risorsa Hub WAN virtuale per connettersi a un hub WAN virtuale di Hong Kong, ad altre aree preferite, a un'area in cui si dispone già di risorse di Azure o in cui si vuole connettersi.

Un'architettura di esempio può essere simile all'esempio seguente:

![Rete WAN di esempio](./media/interconnect-china/sample.png)

In questo esempio, i rami Cina si connettono ad Azure cloud Cina e a vicenda usando connessioni VPN o MPLS. I rami che devono essere connessi ai servizi globali usano servizi MPLS o basati su Internet collegati direttamente a Hong Kong. Se si vuole usare ExpressRoute in Hong Kong e nell'altra area, è necessario configurare [ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) per collegare entrambi i circuiti ExpressRoute.

ExpressRoute Copertura globale non è disponibile in alcune aree. Se è necessario interconnettersi con il Brasile o l'India, ad esempio, è necessario sfruttare i [provider di cloud Exchange](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) per fornire i servizi di routing.

Nella figura seguente sono illustrati entrambi gli esempi per questo scenario.

![Copertura globale](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Secure Internet breakout per Office 365

Un'altra considerazione è la sicurezza di rete, nonché la registrazione per il punto di ingresso tra la Cina e il componente backbone stabilito della WAN virtuale e la dorsale del cliente. Nella maggior parte dei casi, è necessario eseguire il breakout a Internet di Hong Kong per raggiungere direttamente la rete Microsoft Edge e, con questo, i server di front-end di Azure usati per Microsoft 365 servizi.

Per entrambi gli scenari con la rete WAN virtuale, è possibile sfruttare l' [Hub protetto WAN virtuale di Azure](../firewall-manager/secured-virtual-hub.md). Con gestione firewall di Azure è possibile modificare un normale Hub WAN virtuale in un hub protetto, quindi distribuire e gestire un firewall di Azure all'interno di tale hub.

Nella figura seguente viene illustrato un esempio di questo scenario:

![Internet breakout per il traffico di servizi Web e Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architettura e flussi di traffico

A seconda della scelta relativa alla connessione a Hong Kong, l'architettura complessiva può variare leggermente. Questa sezione mostra tre architetture disponibili in una combinazione diversa con VPN o SDWAN e/o ExpressRoute.

Tutte queste opzioni usano l'hub protetto WAN virtuale di Azure per la connettività M365 diretta di Hong Kong. Queste architetture supportano anche i requisiti di conformità per l'area [geografica di office 365](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantengono il traffico vicino alla successiva sede della porta principale di Office 365. Di conseguenza, è anche un miglioramento per l'utilizzo di Microsoft 365 fuori dalla Cina.

Quando si usa la rete WAN virtuale di Azure con le connessioni Internet, ogni connessione può trarre vantaggio da servizi aggiuntivi come [Microsoft Azure servizi di peering (Maps)](https://docs.microsoft.com/azure/peering-service/about). MAPS è stata creata per ottimizzare il traffico proveniente dalla rete globale Microsoft da provider di servizi Internet di terze parti.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opzione 1: SDWAN o VPN

Questa sezione illustra una progettazione che usa SDWAN o VPN per Hong Kong e altri rami. Questa opzione Mostra l'uso e il flusso del traffico quando si usa una connessione Internet pura in entrambi i siti del backbone WAN virtuale. In questo caso, la connessione viene portata a Hong Kong usando l'accesso a Internet dedicato o una soluzione SDWAN del provider ICP. Altri rami usano anche soluzioni Internet pure o SDWAN.

![Da Cina a traffico di Hong Kong](./media/interconnect-china/china-traffic.png)

In questa architettura, ogni sito è connesso alla rete globale Microsoft tramite VPN e WAN virtuale di Azure. Il traffico tra i siti e Hong Kong viene trasmesso attraverso la rete Microsoft e usa solo una normale connessione Internet nell'ultimo miglio.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opzione 2: ExpressRoute e SDWAN o VPN

Questa sezione illustra una progettazione che usa ExpressRoute in Hong Kong e altri rami con rami VPN/SDWAN. Questa opzione Mostra l'uso di e ExpressRoute terminato in Hong Kong e in altri rami connessi tramite SDWAN o VPN. ExpressRoute di Hong Kong è attualmente limitato a un breve elenco di provider, che è possibile trovare nell'elenco dei [partner Express Route](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Dalla Cina al traffico di Hong Kong ExpressRoute](./media/interconnect-china/expressroute.png)

Sono disponibili anche opzioni per terminare ExpressRoute dalla Cina, ad esempio, nella Corea del sud o in Giappone. Tuttavia, in base alla conformità, alla regolamentazione e alla latenza, Hong Kong rappresenta attualmente la scelta migliore.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opzione 3: solo ExpressRoute

Questa sezione illustra una progettazione in cui viene usato ExpressRoute per Hong Kong e altri rami. Questa opzione Mostra l'interconnessione usando ExpressRoute su entrambe le estremità. Qui è presente un flusso di traffico diverso rispetto all'altro. Il traffico Microsoft 365 viene propagato all'hub protetto WAN virtuale di Azure e da tale rete alla rete Microsoft Edge e a Internet.

Il traffico che passa ai rami interconnessi o da essi alle posizioni in Cina seguirà un approccio diverso all'interno dell'architettura. Attualmente la rete WAN virtuale non supporta il transito da ExpressRoute a ExpressRoute. Il traffico userà ExpressRoute Copertura globale o l'interconnessione di terze parti senza passare l'hub WAN virtuale. Il flusso passerà direttamente da un Microsoft Enterprise Edge (MSEE) a un altro.

![Copertura globale di ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Attualmente ExpressRoute Copertura globale non è disponibile in ogni paese, ma è possibile configurare una soluzione usando la rete WAN virtuale di Azure.

È possibile, ad esempio, configurare un ExpressRoute con il peering Microsoft e connettere un tunnel VPN tramite tale peering alla rete WAN virtuale di Azure. A questo punto è stato abilitato, di nuovo, il transito tra VPN e ExpressRoute senza Copertura globale e provider e servizio di terze parti, ad esempio Megaport cloud.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

* [Architettura di rete di transito globale con WAN virtuale di Azure](virtual-wan-global-transit-network-architecture.md)

* [Creare un hub WAN virtuale](virtual-wan-site-to-site-portal.md)

* [Configurare un hub protetto WAN virtuale](../firewall-manager/secure-cloud-network.md)

* [Panoramica dell'anteprima del servizio peering di Azure](https://docs.microsoft.com/azure/peering-service/about)
