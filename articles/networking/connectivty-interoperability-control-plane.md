---
title: 'Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali - analisi del piano di controllo: interoperabilità delle funzionalità per la connettività di back-end di Azure | Microsoft Docs'
description: Questa pagina contiene i dettagli dell'analisi del piano di controllo dell'installazione test creata per analizzare l'interoperabilità delle funzionalità ExpressRoute, VPN da sito a sito e peering reti virtuali.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947123"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali: analisi del piano di controllo

Questo articolo illustra l'analisi del piano di controllo dell'installazione test. Per esaminare l'installazione test, vedere la relativa sezione in [questo articolo][Setup]. Per esaminare i dettagli di configurazione dell'installazione test, vedere [Configurazione dell'installazione test][Configuration].

L'analisi del piano di controllo essenzialmente esamina le route scambiate tra le reti all'interno di una topologia. Consente di analizzare il modo in cui diverse reti visualizzano la topologia.

##<a name="hub-and-spoke-vnet-perspective"></a>Prospettiva delle reti virtuali spoke e dell'hub

Il diagramma seguente illustra la rete dalla prospettiva della rete virtuale dell'hub e della rete virtuale spoke (evidenziata in blu). Il diagramma illustra anche il Numero sistema autonomo (ASN) di varie rete e le route scambiate tra reti diverse. 

[![1]][1]

Si noti che il numero ASN del gateway ExpressRoute della rete virtuale è diverso dal numero ASN dei router Microsoft Enterprise Edge (MSEE). Il gateway ExpressRoute usa un numero ASN privato (65515) e i MSEE usano un ASN pubblico (12076) a livello globale. Quando si configura il peering di ExpressRoute, poiché MSEE è il peer, si usa 12076 come ASN del peer. Sul lato Azure, MSEE stabilisce il peering eBGP con il gateway ExpressRoute. Il peering eBGP doppio che stabilisce il router MSEE ogni peering ExpressRoute è trasparente a livello di piano di controllo. Di conseguenza, quando viene visualizzata una tabella di route ExpressRoute, appare il numero ASN del gateway ExpressRoute della rete virtuale per i prefissi della rete virtuale. Nello screenshot seguente è riportato un esempio di tabella di route ExpressRoute: 

[![5]][5]

In Azure il numero ASN è significativo solo dal punto di vista del peering. Per impostazione predefinita, l'ASN del gateway ExpressRoute e del gateway VPN è 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Prospettiva della rete locale Location-1 e della rete virtuale remota via ExpressRoute-1

La rete locale Location-1 e la rete virtuale remota sono entrambe connesse alla rete virtuale dell'hub attraverso ExpressRoute 1 e di conseguenza condividono la stessa prospettiva della topologia, come illustrato nel diagramma seguente.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Prospettiva della rete locale Location-1 e della rete virtuale di diramazione via VPN da sito a sito

La rete locale Location-1 e la rete virtuale di diramazione sono entrambe connesse al gateway VPN della rete virtuale dell'hub attraverso connessioni VPN da sito a sito e di conseguenza condividono la stessa prospettiva della topologia, come illustrato nel diagramma seguente.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Prospettiva della rete locale Location-2

La rete locale Location-2 è connessa alla rete virtuale dell'hub attraverso il peering privato di ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Altre informazioni

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Uso di ExpressRoute e della connettività VPN da sito a sito in parallelo

####  <a name="site-to-site-vpn-over-expressroute"></a>VPN da sito a sito con ExpressRoute

La connettività VPN da sito a sito può essere configurata con il peering ExpressRoute di Microsoft per scambiare privatamente i dati tra la rete locale e le reti virtuali di Azure con riservatezza, impossibilità di riproduzione, autenticità e integrità. Per altre informazioni su come configurare la VPN IPSec da sito a sito in modalità tunnel con il peering ExpressRoute di Microsoft, vedere [Configurare una VPN da sito a sito tramite peering ExpressRoute Microsoft][S2S-Over-ExR]. 

La limitazione principale della configurazione VPN da sito a sito con peering Microsoft è la velocità effettiva. La velocità effettiva nel tunnel IPSec è limitata dalla capacità del gateway VPN. La velocità effettiva del gateway VPN è inferiore a quella di ExpressRoute. In questi scenari l'uso del tunnel IPSec per il traffico ad alta protezione e del peering privato per tutti gli altri tipi di traffico consente di ottimizzare l'uso della larghezza di banda di ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN da sito a sito come percorso di failover protetto per ExpressRoute
ExpressRoute viene offerto come coppia di circuiti ridondanti per garantire la disponibilità elevata. È possibile configurare la connettività ExpressRoute con ridondanza geografica in diverse aree di Azure. Inoltre, come si è visto nell'installazione test, se all'interno di una determinata area di Azure si vuole usare un percorso di failover per la connettività ExpressRoute, è possibile usare la VPN da sito a sito. Quando gli stessi prefissi vengono annunciati sia su ExpressRoute che sulla VPN da sito a sito, Azure preferisce ExpressRoute alla VPN da sito a sito. Per evitare il routing asimmetrico tra ExpressRoute e la VPN da sito a sito, la configurazione di rete locale deve anche ricambiare preferendo ExpressRoute piuttosto che la connettività VPN da sito a sito.

Per altre informazioni su come configurare le connessioni ExpressRoute e VPN da sito a sito coesistenti, vedere [Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estensione della connettività di back-end a reti virtuali spoke e diramazioni

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connettività della rete virtuale spoke con il peering reti virtuali

L'architettura di rete virtuale hub-spoke è ampiamente usata. L'hub è una rete virtuale di Azure che funge da punto centrale di connettività tra le reti virtuali spoke e la rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub e possono essere usati per isolare i carichi di lavoro. Il traffico scorre tra il data center locale e l'hub attraverso una connessione VPN o ExpressRoute. Per maggiori dettagli sull'architettura, vedere [Implementare una topologia di rete hub-spoke in Azure][Hub-n-Spoke]

Il peering reti virtuali all'interno di un'area consente alle reti virtuali spoke di usare i gateway di rete virtuale dell'hub (gateway ExpressRoute e VPN) per comunicare con le reti remote.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Creare un ramo della connettività di rete virtuale usando la VPN da sito a sito

Se si vuole creare un ramo di reti virtuali (in aree diverse) e le reti locali comunicano tra loro attraverso una rete virtuale dell'hub, la soluzione nativa di Azure è la connettività VPN da sito a sito che usa la VPN. Un'opzione alternativa è usare un'appliance virtuale di rete per il routing nell'hub.

Per configurare i gateway VPN, vedere [Configurazione del gateway VPN][VPN]. Per la distribuzione di appliance di rete virtuale con disponibilità elevata, vedere [Distribuire appliance virtuali di rete con disponibilità elevata][Deploy-NVA].

## <a name="next-steps"></a>Passaggi successivi

Per l'analisi del piano dati dell'installazione test e per le visualizzazioni delle funzionalità di monitoraggio della rete di Azure, vedere [Analisi del piano dati][Data-Analysis].

Per informazioni sul numero di circuiti ExpressRoute che possono essere connessi a un gateway ExpressRoute, sul numero di gateway ExpressRoute che si possono connettere a un circuito ExpressRoute o per informazioni su altri limiti di scalabilità di ExpressRoute, vedere le [domande frequenti su ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Prospettiva della topologia delle reti virtuali spoke e dell'hub"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Prospettiva della topologia della rete Location-1 e della rete virtuale remota via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Prospettiva della topologia della rete Location-1 e della rete virtuale di diramazione via VPN da sito a sito"
[4]: ./media/backend-interoperability/Loc2View.png "Prospettiva della topologia di Location-2"
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




