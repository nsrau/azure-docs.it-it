---
title: 'Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali - installazione test: interoperabilità delle funzionalità per la connettività di back-end di Azure | Microsoft Docs'
description: Questa pagina contiene un'installazione test usata per analizzare l'interoperabilità delle funzionalità ExpressRoute, VPN da sito a sito e peering reti virtuali.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947113"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali: installazione test
In questo articolo verrà identificata un'installazione test che è possibile usare per analizzare il modo in cui le diverse funzionalità interagiscono tra loro sia a livello di piano di controllo, sia a livello di piano dati. Prima di descrivere l'installazione test, è opportuno vedere qual è il significato delle diverse funzionalità di rete di Azure.

ExpressRoute: usando il peering privato ExpressRoute è possibile connettere direttamente gli spazi IP privati della rete locale alle distribuzioni di reti virtuali di Azure.  Con ExpressRoute è possibile avere una maggiore larghezza di banda e una connessione privata. Numerosi partner di ExpressRoute offrono la connettività ExpressRoute con contratto di servizio. Per altre informazioni su ExpressRoute e su come configurarlo, vedere l'[introduzione a ExpressRoute][ExpressRoute]

VPN da sito a sito: per connettere in modo sicuro una rete locale ad Azure via Internet o con ExpressRoute, è disponibile l'opzione VPN da sito a sito. Per informazioni sulla configurazione di una VPN da sito a sito per la connessione ad Azure, vedere [Configurazione del gateway VPN][VPN]

Peering reti virtuali: è disponibile per stabilire la connettività tra reti virtuali. Per altre informazioni sul peering reti virtuali, vedere l'[esercitazione sul peering reti virtuali][VNet].

##<a name="test-setup"></a>Impostazione del test

Il diagramma seguente illustra l'installazione test.

[![1]][1]

La parte centrale dell'installazione test è la rete virtuale dell'hub nell'area 1 di Azure. La rete virtuale dell'hub è connessa a diverse reti come indicato di seguito:

1.  Alla rete virtuale spoke via peering reti virtuali. La rete virtuale spoke usa un accesso remoto a entrambi i gateway nella rete virtuale dell'hub.
2.  Alla rete virtuale di diramazione via VPN da sito a sito. La connettività usa eBGP per scambiare le route.
3.  Alla rete locale Location-1 via peering privato ExpressRoute come percorso principale e connettività VPN da sito a sito come percorso di backup. Nella parte restante di questo documento si farà riferimento a questo circuito ExpressRoute come ExpressRoute1. Per impostazione predefinita, i circuiti ExpressRoute offrono connettività ridondante per la disponibilità elevata. In ExpressRoute1 sottointerfaccia del router CE secondario rivolta verso il router MSEE secondario è disabilitata. Ciò è indicato da una linea rossa sopra la freccia a linea doppia nel diagramma precedente.
4.  Alla rete locale Location-2 attraverso un altro peering privato ExpressRoute. Nella parte restante di questo documento si farà riferimento al secondo circuito ExpressRoute come ExpressRoute2.
5.  ExpressRoute1 inoltre connette sia la rete virtuale dell'hub, sia la rete locale Location-1 a una rete virtuale remota nell'area 2 di Azure.

## <a name="further-reading"></a>Altre informazioni

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Uso di ExpressRoute e della connettività VPN da sito a sito in parallelo

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN da sito a sito con ExpressRoute 

La connettività VPN da sito a sito può essere configurata con il peering ExpressRoute di Microsoft per scambiare privatamente i dati tra la rete locale e le reti virtuali di Azure con riservatezza, impossibilità di riproduzione, autenticità e integrità. Per altre informazioni su come configurare la VPN IPSec da sito a sito in modalità tunnel con il peering ExpressRoute di Microsoft, vedere [Configurare una VPN da sito a sito tramite peering ExpressRoute Microsoft][S2S-Over-ExR]. 

La limitazione principale della configurazione VPN da sito a sito con peering Microsoft è la velocità effettiva. La velocità effettiva nel tunnel IPSec è limitata dalla capacità del gateway VPN. La velocità effettiva del gateway VPN è inferiore a quella di ExpressRoute. In questi scenari l'uso del tunnel IPSec per il traffico ad alta protezione e del peering privato per tutti gli altri tipi di traffico consente di ottimizzare l'uso della larghezza di banda di ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN da sito a sito come percorso di failover protetto per ExpressRoute
ExpressRoute viene offerto come coppia di circuiti ridondanti per garantire la disponibilità elevata. È possibile configurare la connettività ExpressRoute con ridondanza geografica in diverse aree di Azure. Inoltre, come si è visto nell'installazione test, se all'interno di una determinata area di Azure si vuole usare un percorso di failover per la connettività ExpressRoute, è possibile usare la VPN da sito a sito. Quando gli stessi prefissi vengono annunciati sia su ExpressRoute che sulla VPN da sito a sito, Azure preferisce ExpressRoute alla VPN da sito a sito. Per evitare il routing asimmetrico tra ExpressRoute e la VPN da sito a sito, la configurazione di rete locale deve anche ricambiare preferendo ExpressRoute piuttosto che la connettività VPN da sito a sito.

Per altre informazioni su come configurare le connessioni ExpressRoute e VPN da sito a sito coesistenti, vedere [Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estensione della connettività di back-end a reti virtuali spoke e diramazioni

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connettività della rete virtuale spoke con il peering reti virtuali

L'architettura di rete virtuale hub-spoke è ampiamente usata. L'hub è una rete virtuale di Azure che funge da punto centrale di connettività tra le reti virtuali spoke e la rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub e possono essere usati per isolare i carichi di lavoro. Il traffico scorre tra il data center locale e l'hub attraverso una connessione VPN o ExpressRoute. Per altre informazioni sull'architettura, vedere [Implementare una topologia di rete hub-spoke in Azure][Hub-n-Spoke]

Il peering reti virtuali all'interno di un'area consente alle reti virtuali spoke di usare i gateway di rete virtuale dell'hub (gateway ExpressRoute e VPN) per comunicare con le reti remote.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Creare un ramo della connettività di rete virtuale usando la VPN da sito a sito

Se si vuole creare un ramo di reti virtuali (in aree diverse) e le reti locali comunicano tra loro attraverso una rete virtuale dell'hub, la soluzione nativa di Azure è la connettività VPN da sito a sito che usa la VPN. Un'opzione alternativa è usare un'appliance virtuale di rete per il routing nell'hub.

Per configurare i gateway VPN, vedere [Configurazione del gateway VPN][VPN]. Per la distribuzione di appliance di rete virtuale con disponibilità elevata, vedere [Distribuire appliance virtuali di rete con disponibilità elevata][Deploy-NVA].

## <a name="next-steps"></a>Passaggi successivi

Per i dettagli di configurazione della topologia di test, vedere [Dettagli configurazione][Configuration].

Per l'analisi del piano di controllo dell'installazione test e per comprendere le visualizzazioni di diverse reti virtuali o VLAN della topologia, vedere [Analisi del piano di controllo][Control-Analysis].

Per l'analisi del piano dati dell'installazione test e per le visualizzazioni delle funzionalità di monitoraggio della rete di Azure, vedere [Analisi del piano dati][Data-Analysis].

Per informazioni sul numero di circuiti ExpressRoute che possono essere connessi a un gateway ExpressRoute, sul numero di gateway ExpressRoute che si possono connettere a un circuito ExpressRoute o per informazioni su altri limiti di scalabilità di ExpressRoute, vedere le [domande frequenti su ExpressRoute][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "La topologia di test"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




