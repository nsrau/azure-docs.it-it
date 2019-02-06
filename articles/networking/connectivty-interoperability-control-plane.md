---
title: 'Interoperabilità nelle funzionalità di connettività back-end di Azure: Analisi del piano di controllo | Microsoft Docs'
description: Questo articolo illustra l'analisi del piano di controllo dell'installazione test che è possibile usare per analizzare l'interoperabilità tra ExpressRoute, una VPN da sito a sito e il peering reti virtuali in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 77a405e2f020ff764348370fc001388610ad75b6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155528"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Interoperabilità nelle funzionalità di connettività back-end di Azure: Analisi del piano di controllo

Questo articolo illustra l'analisi del piano di controllo dell'[installazione test][Setup]. È anche possibile esaminare la [configurazione dell'installazione test][Configuration] e l'[analisi del piano dati][Data-Analysis] dell'installazione test.

L'analisi del piano di controllo essenzialmente esamina le route scambiate tra le reti all'interno di una topologia. L'analisi del piano di controllo aiuta a capire come le diverse reti vedono la topologia.

## <a name="hub-and-spoke-vnet-perspective"></a>Prospettiva delle reti virtuali spoke e dell'hub

La figura seguente illustra la rete dalla prospettiva di una rete virtuale dell'hub e di una rete virtuale spoke (evidenziata in blu). La figura illustra anche il numero ASN (Autonomous System Number, numero sistema autonomo) di varie reti e le route scambiate tra reti diverse: 

[![1]][1]

Il numero ASN del gateway Azure ExpressRoute della rete virtuale è diverso da quello dei router Microsoft Enterprise Edge (MSEE). Un gateway ExpressRoute usa un numero ASN privato (con valore **65515**) e i router MSEE usano un numero ASN pubblico (con valore **12076**) a livello globale. Quando si configura il peering di ExpressRoute, poiché MSEE è il peer, si usa **12076** come numero ASN peer. Sul lato Azure, MSEE stabilisce il peering eBGP con il gateway ExpressRoute. Il peering eBGP doppio stabilito da MSEE per ogni peering di ExpressRoute è trasparente a livello di piano di controllo. Di conseguenza, quando si visualizza una tabella di route ExpressRoute, viene indicato il numero ASN del gateway ExpressRoute della rete virtuale per i prefissi della rete virtuale. 

La figura seguente illustra un esempio di tabella di route ExpressRoute: 

[![5]][5]

In Azure il numero ASN è significativo solo dal punto di vista del peering. Per impostazione predefinita, il numero ASN sia del gateway ExpressRoute che del gateway VPN nel gateway VPN di Azure è **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Prospettiva della posizione 1 locale e della rete virtuale remota tramite ExpressRoute 1

Sia la posizione 1 locale che la rete virtuale remota sono connesse alla rete virtuale dell'hub tramite ExpressRoute 1. Viene condivisa la stessa prospettiva della topologia, come illustrato nel diagramma seguente:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Prospettiva della posizione 1 locale e della rete virtuale della filiale tramite una VPN da sito a sito

Sia la posizione 1 locale che la rete virtuale della filiale sono connesse al gateway VPN di una rete di virtuale dell'hub tramite una connessione VPN da sito a sito. Viene condivisa la stessa prospettiva della topologia, come illustrato nel diagramma seguente:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>Prospettiva della posizione 2 locale

La posizione 2 locale è connessa a una rete virtuale dell'hub tramite il peering privato di ExpressRoute 2: 

[![4]][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e connettività VPN da sito a sito in parallelo

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN da sito a sito con ExpressRoute

È possibile configurare una VPN da sito a sito con il peering Microsoft ExpressRoute per scambiare privatamente i dati tra la rete locale e le reti virtuali di Azure. Con questa configurazione, i dati possono essere scambiati garantendone riservatezza, autenticità e integrità. Lo scambio di dati è anche impossibile da riprodurre. Per altre informazioni su come configurare una VPN IPSec da sito a sito in modalità tunnel con il peering Microsoft ExpressRoute, vedere [Configurare una VPN da sito a sito tramite peering ExpressRoute Microsoft][S2S-Over-ExR]. 

La limitazione principale per la configurazione di una VPN da sito a sito che usa il peering Microsoft è rappresentata dalla velocità effettiva. La velocità effettiva nel tunnel IPSec è limitata dalla capacità del gateway VPN. La velocità effettiva del gateway VPN è inferiore alla velocità effettiva di ExpressRoute. In questo scenario, usando il tunnel IPSec per il traffico a sicurezza elevata e il peering privato per tutti gli altri tipi di traffico è possibile ottimizzare l'uso della larghezza di banda di ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN da sito a sito come percorso di failover sicuro per ExpressRoute

ExpressRoute funge da coppia di circuiti ridondanti per garantire la disponibilità elevata. È possibile configurare la connettività ExpressRoute con ridondanza geografica in diverse aree di Azure. Come dimostrato nell'installazione test, in un'area di Azure è anche possibile usare una VPN da sito a sito per creare un percorso di failover per la connettività di ExpressRoute. Quando gli stessi prefissi vengono annunciati sia in ExpressRoute che in una VPN da sito a sito, Azure dà la priorità a ExpressRoute. Per evitare il routing asimmetrico tra ExpressRoute e la VPN da sito a sito, la configurazione di rete locale deve fare lo stesso, preferendo la connettività di ExpressRoute rispetto alla connettività VPN da sito a sito.

Per altre informazioni su come configurare connessioni coesistenti per ExpressRoute e VPN da sito a sito, vedere [Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estendere la connettività back-end a reti virtuali spoke e del ramo

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Connettività della rete virtuale spoke tramite il peering reti virtuali

L'architettura di rete virtuale dell'hub e spoke è molto diffusa. L'hub è una rete virtuale in Azure che funge da punto centrale di connettività tra le reti virtuali spoke e la rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub e che è possibile usare per isolare i carichi di lavoro. Il traffico scorre tra il data center locale e l'hub attraverso una connessione VPN o ExpressRoute. Per altre informazioni sull'architettura, vedere [Implementare una topologia di rete hub-spoke in Azure][Hub-n-Spoke].

Nel peering reti virtuali all'interno di un'area, le reti virtuali spoke possono usare i gateway di rete virtuale dell'hub (gateway ExpressRoute e VPN) per comunicare con le reti remote.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Connettività delle reti virtuali del ramo mediante VPN da sito a sito

È possibile far comunicare tra loro le reti virtuali del ramo in aree diverse e le reti virtuali tramite una rete virtuale dell'hub. La soluzione di Azure nativa per questa configurazione è la connettività VPN da sito a sito tramite una rete VPN. In alternativa, è possibile usare un'appliance virtuale di rete per il routing nell'hub.

Per altre informazioni, vedere [Che cos'è un Gateway VPN?][VPN] e [Distribuire appliance virtuali di rete con disponibilità elevata][Deploy-NVA].

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni sull'[analisi del piano dati][Data-Analysis] dell'installazione test e sulle visualizzazioni delle funzionalità di monitoraggio di rete di Azure.

Vedere le [domande frequenti su ExpressRoute][ExR-FAQ] per:
-   Informazioni sul numero di circuiti ExpressRoute che è possibile connettere a un gateway ExpressRoute.
-   Informazioni sul numero di gateway ExpressRoute che è possibile connettere a un circuito ExpressRoute.
-   Informazioni su altri limiti di scalabilità di ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Prospettiva della topologia delle reti virtuali dell'hub e spoke"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Prospettiva della topologia della posizione 1 e della rete virtuale remota tramite ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Prospettiva della topologia della posizione 1 e della rete virtuale della filiale tramite VPN da sito a sito"
[4]: ./media/backend-interoperability/Loc2View.png "Prospettiva della topologia della posizione 2"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabella di route di ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


