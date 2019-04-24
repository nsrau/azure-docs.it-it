---
title: 'Interoperabilità nelle funzionalità di connettività back-end di Azure: Dettagli di configurazione | Microsoft Docs'
description: Questo articolo illustra i dettagli della configurazione per l'installazione test che è possibile usare per analizzare l'interoperabilità tra ExpressRoute, una VPN da sito a sito e il peering reti virtuali in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 2ceb4aeac55bd555a41c29bd41b00c771490e5f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425755"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilità nelle funzionalità di connettività back-end di Azure: Dettagli di configurazione di test

In questo articolo sono illustrati i dettagli dell'[installazione test][Setup]. L'installazione test consente di analizzare l'interoperabilità dei servizi di rete di Azure a livello di piano di controllo e a livello di piano dati.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Connettività della rete virtuale spoke tramite il peering reti virtuali

La figura seguente mostra i dettagli del peering reti virtuali di Azure per una rete virtuale spoke. Per informazioni su come configurare il peering tra due reti virtuali, vedere [Gestire il peering reti virtuali][VNet-Config]. Se si vuole impostare la rete virtuale spoke in modo che usi i gateway connessi alla rete virtuale dell'hub, selezionare **Usa gateway remoti**.

[![1]][1]

La figura seguente illustra i dettagli del peering reti virtuali per la rete virtuale dell'hub. Se si vuole impostare la rete virtuale spoke in modo che usi i gateway di rete virtuale dell'hub, selezionare **Usa gateway remoti**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Connettività delle reti virtuali delle filiali tramite VPN da sito a sito

Configurare la connettività VPN da sito a sito tra le reti virtuali dell'hub e delle filiali usando gateway VPN nel gateway VPN di Azure. Per impostazione predefinita, i gateway VPN e i gateway Azure ExpressRoute usano un valore di numero ASN (Autonomous System Number, numero sistema autonomo) **65515**. È possibile modificare il valore ASN nel gateway VPN. Nell'installazione test il valore ASN del gateway VPN della rete virtuale della filiale viene modificato in **65516** per supportare il routing eBGP tra l'hub e le reti virtuali delle filiali.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Connettività della posizione 1 locale tramite ExpressRoute e una VPN da sito a sito

### <a name="expressroute-1-configuration-details"></a>Dettagli della configurazione di ExpressRoute 1

La figura seguente illustra la configurazione del circuito ExpressRoute dell'area 1 di Azure verso i router della rete perimetrale del cliente della posizione 1 locale:

[![4]][4]

La figura seguente illustra la configurazione della connessione tra il circuito ExpressRoute 1 e la rete virtuale dell'hub:

[![5]][5]

L'elenco seguente mostra la configurazione del router della rete perimetrale del cliente principale per la connettività di peering privata di ExpressRoute. Nell'installazione test vengono usati router Cisco ASR1000 come router della rete perimetrale del cliente. Quando la VPN da sito a sito e i circuiti ExpressRoute vengono configurati in parallelo per connettere una rete locale ad Azure, per impostazione predefinita Azure dà la priorità al circuito ExpressRoute. Per evitare il routing asimmetrico, anche la rete locale deve dare la priorità alla connettività di ExpressRoute rispetto alla connettività VPN da sito a sito. La configurazione seguente stabilisce la priorità tramite l'attributo **local-preference** di BGP:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Dettagli della configurazione VPN da sito a sito

L'elenco seguente mostra la configurazione del router della rete perimetrale del cliente principale per la connettività VPN da sito a sito:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Connettività della posizione 2 locale tramite ExpressRoute

Un secondo circuito ExpressRoute, vicino alla posizione 2 locale, connette la posizione 2 locale alla rete virtuale dell'hub. La figura seguente mostra la configurazione del secondo circuito ExpressRoute:

[![6]][6]

La figura seguente mostra la configurazione della connessione tra il secondo circuito ExpressRoute e la rete virtuale dell'hub:

[![7]][7]

ExpressRoute 1 connette sia la rete virtuale dell'hub che la posizione 1 locale a una rete virtuale remota in un'altra area di Azure:

[![8]][8]

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

Leggere le informazioni sull'[analisi del piano di controllo][Control-Analysis] dell'installazione test e sulle visualizzazioni delle diverse reti virtuali o VLAN nella topologia.

Leggere le informazioni sull'[analisi del piano dati][Data-Analysis] dell'installazione test e sulle visualizzazioni delle funzionalità di monitoraggio di rete di Azure.

Vedere le [domande frequenti su ExpressRoute][ExR-FAQ] per:
-   Informazioni sul numero di circuiti ExpressRoute che è possibile connettere a un gateway ExpressRoute.
-   Informazioni sul numero di gateway ExpressRoute che è possibile connettere a un circuito ExpressRoute.
-   Informazioni su altri limiti di scalabilità di ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Peering reti virtuali per le reti virtuali spoke"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Peering reti virtuali per la rete virtuale dell'hub"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configurazione del gateway VPN della rete virtuale di una filiale"
[4]: ./media/backend-interoperability/ExR1.png "Configurazione di ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configurazione della connessione di ExpressRoute 1 a un gateway ExR di rete virtuale dell'hub"
[6]: ./media/backend-interoperability/ExR2.png "Configurazione di ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configurazione della connessione di ExpressRoute 2 a un gateway ExR di rete virtuale dell'hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configurazione della connessione di ExpressRoute 2 a un gateway ExR di rete virtuale remoto"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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


