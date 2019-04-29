---
title: 'Interoperabilità nelle funzionalità di connettività back-end di Azure: Installazione test | Microsoft Docs'
description: Questo articolo illustra un'installazione test che è possibile usare per analizzare l'interoperabilità tra ExpressRoute, una VPN da sito a sito e il peering reti virtuali in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811120"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilità nelle funzionalità di connettività back-end di Azure: Impostazione del test

Questo articolo descrive un'installazione test che è possibile usare per analizzare l'interoperabilità dei servizi di rete di Azure a livello di piano di controllo e a livello di piano dati. Verranno ora esaminati brevemente i componenti di rete di Azure:

-   **Azure ExpressRoute**: il peering privato in Azure ExpressRoute consente di connettere direttamente gli spazi IP privati nella rete locale alle distribuzioni di reti virtuali di Azure. Con ExpressRoute è possibile ottenere una maggiore larghezza di banda e una connessione privata. Numerosi partner di ExpressRoute offrono la connettività di ExpressRoute con contratti di servizio. Per altre informazioni su ExpressRoute e su come configurarlo, vedere [Panoramica di ExpressRoute][ExpressRoute].
-   **VPN da sito a sito**: è possibile usare Gateway VPN di Azure come VPN da sito a sito per connettere in modo sicuro una rete locale ad Azure tramite Internet o usando ExpressRoute. Per informazioni su come configurare una VPN da sito a sito per la connessione ad Azure, vedere [Configurare il gateway VPN][VPN].
-   **Peering reti virtuali**: usare il peering reti virtuali per stabilire la connettività tra reti virtuali nella rete virtuale di Azure. Per altre informazioni sul peering reti virtuali, vedere l'[esercitazione sul peering reti virtuali][VNet].

## <a name="test-setup"></a>Impostazione del test

La figura seguente illustra l'installazione test:

[![1]][1]

La parte centrale dell'installazione test è la rete virtuale dell'hub nell'area 1 di Azure. La rete virtuale dell'hub è connessa a diverse reti nei modi seguenti:

-   La rete virtuale dell'hub è connessa alla rete virtuale spoke tramite il peering reti virtuali. La rete virtuale spoke dispone di accesso remoto a entrambi i gateway nella rete virtuale dell'hub.
-   La rete virtuale dell'hub è connessa alla rete virtuale della filiale tramite VPN da sito a sito. La connettività usa eBGP per scambiare le route.
-   La rete virtuale dell'hub è connessa alla rete della posizione 1 locale tramite il peering privato di ExpressRoute come percorso principale. Viene usata la connettività VPN da sito a sito come percorso di backup. Nella parte restante di questo articolo si fa riferimento a questo circuito ExpressRoute come ExpressRoute 1. Per impostazione predefinita, i circuiti ExpressRoute offrono connettività ridondante per la disponibilità elevata. In ExpressRoute 1, la sottointerfaccia del router della rete perimetrale del cliente che si interfaccia con il router Microsoft Enterprise Edge (MSEE) secondario è disabilitata. Una linea rossa sulla freccia a doppia linea nella figura precedente rappresenta la sottointerfaccia del router della rete perimetrale del cliente disabilitata.
-   La rete virtuale dell'hub è connessa alla rete della posizione 2 locale tramite un altro peering privato di ExpressRoute. Nella parte restante di questo articolo si fa riferimento a questo secondo circuito ExpressRoute come ExpressRoute 2.
-   ExpressRoute 1 connette inoltre sia la rete virtuale dell'hub che la rete della posizione 1 locale a una rete virtuale remota nell'area 2 di Azure.

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

Leggere le informazioni sui [dettagli della configurazione][Configuration] per la topologia di test.

Leggere le informazioni sull'[analisi del piano di controllo][Control-Analysis] dell'installazione test e sulle visualizzazioni delle diverse reti virtuali o VLAN nella topologia.

Leggere le informazioni sull'[analisi del piano dati][Data-Analysis] dell'installazione test e sulle visualizzazioni delle funzionalità di monitoraggio di rete di Azure.

Vedere le [domande frequenti su ExpressRoute][ExR-FAQ] per:
-   Informazioni sul numero di circuiti ExpressRoute che è possibile connettere a un gateway ExpressRoute.
-   Informazioni sul numero di gateway ExpressRoute che è possibile connettere a un circuito ExpressRoute.
-   Informazioni su altri limiti di scalabilità di ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagramma della topologia di test"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


