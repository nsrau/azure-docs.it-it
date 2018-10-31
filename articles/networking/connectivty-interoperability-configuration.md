---
title: 'Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali - dettagli di configurazione: interoperabilità delle funzionalità per la connettività di back-end di Azure | Microsoft Docs'
description: Questa pagina contiene i dettagli di configurazione dell'installazione test usata per analizzare l'interoperabilità delle funzionalità ExpressRoute, VPN da sito a sito e peering reti virtuali.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947096"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali: dettagli della configurazione di test

In questo articolo sono illustrati i dettagli di configurazione dell'installazione test. Per esaminare l'installazione test, vedere la relativa sezione in [questo articolo][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connettività della rete virtuale spoke con il peering reti virtuali

Lo screenshot del portale di Azure riportato sotto illustra i dettagli del peering reti virtuali per la rete virtuale spoke. Per istruzioni dettagliate per la configurazione del peering tra due reti virtuali, vedere [Creare, modificare o eliminare un peering reti virtuali][VNet-Config]. Se si vuole impostare la rete virtuale spoke in modo che usi i gateway connessi alla rete virtuale dell'hub, vedere *Use remote gateways* (Usare gateway remoti).

[![1]][1]

Lo screenshot del portale di Azure riportato sotto illustra i dettagli del peering reti virtuali per la rete virtuale dell'hub. Se si vuole impostare la rete virtuale dell'hub in modo che usi i propri gateway, vedere *Use remote gateways* (Usare gateway remoti).

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Creare un ramo della connettività di rete virtuale usando la VPN da sito a sito

La connettività VPN da sito a sito tra l'hub e le reti virtuali di diramazione viene configurata usando i gateway VPN. Per impostazione predefinita, i gateway VPN ed ExpressRoute sono configurati con il valore ASN privato 65515. il gateway VPN consente di modificare il valore ASN. Nell'installazione test, come illustrato nello screenshot del portale di Azure che segue, il valore ASN del gateway VPN della rete virtuale di diramazione viene modificato in 65516 per consentire il routing eBGP tra l'hub e le reti virtuali di diramazione.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Connettività della rete locale Location-1 con ExpressRoute e VPN da sito a sito

###<a name="expressroute1-configuration-details"></a>Dettagli di configurazione di ExpressRoute1

Lo screenshot del portale che segue illustra la configurazione del circuito ExpressRoute dell'area 1 di Azure verso i router CE della rete locale Location-1.

[![4]][4]

Lo screenshot del portale che segue illustra la configurazione della connessione tra il circuito ExpressRoute1 e la rete virtuale dell'hub.

[![5]][5]

La configurazione seguente è il listato della configurazione del router CE primario (i router Cisco ASR1000 vengono usati come router CE nell'installazione test) relativa alla connettività di peering privata di ExpressRoute. Quando sia la VPN da sito a sito, sia il circuito ExpressRoute sono configurati in parallelo per connettere una rete locale ad Azure, per impostazione predefinita Azure sceglie il circuito ExpressRoute. Per evitare un routing asimmetrico, anche la rete locale deve scegliere ExpressRoute piuttosto che la VPN da sito a sito per le route ricevute sia la VPN da sito che attraverso ExpressRoute. Questo risultato viene ottenuto nella configurazione seguente usando l'attributo BGP di preferenza locale. 

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

###<a name="site-to-site-vpn-configuration-details"></a>Dettagli della configurazione VPN da sito a sito

Di seguito è riportato il listato della configurazione del router CE primario correlato alla connettività VPN da sito a sito:

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

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Connettività della rete locale Location 2 con ExpressRoute

Un secondo circuito ExpressRoute, vicino alla rete locale Location 2, connette la rete locale Location 2 alla rete virtuale dell'hub. Lo screenshot del portale seguente illustra la seconda configurazione ExpressRoute.

[![6]][6]

Lo screenshot del portale che segue illustra la configurazione della connessione tra il secondo circuito ExpressRoute e la rete virtuale dell'hub.

[![7]][7]

ExpressRoute1 connette sia la rete virtuale dell'hub, sia la rete locale Location-1 a una rete virtuale remota in un'altra area di Azure.

[![8]][8]

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

Per l'analisi del piano di controllo dell'installazione test e per comprendere le visualizzazioni di diverse reti virtuali o VLAN della topologia, vedere [Analisi del piano di controllo][Control-Analysis].

Per l'analisi del piano dati dell'installazione test e per le visualizzazioni delle funzionalità di monitoraggio della rete di Azure, vedere [Analisi del piano dati][Data-Analysis].

Per informazioni sul numero di circuiti ExpressRoute che possono essere connessi a un gateway ExpressRoute, sul numero di gateway ExpressRoute che si possono connettere a un circuito ExpressRoute o per informazioni su altri limiti di scalabilità di ExpressRoute, vedere le [domande frequenti su ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Peering reti virtuali per le reti virtuali spoke"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Peering reti virtuali per la rete virtuale dell'hub"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configurazione del gateway VPN per la rete virtuale di diramazione"
[4]: ./media/backend-interoperability/ExR1.png "Configurazione di ExpressRoute1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configurazione della connessione di ExpressRoute1 al gateway ExR della rete virtuale dell'hub"
[6]: ./media/backend-interoperability/ExR2.png "Configurazione di ExpressRoute2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configurazione della connessione di ExpressRoute2 al gateway ExR della rete virtuale dell'hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configurazione della connessione di ExpressRoute2 al gateway ExR della rete virtuale remota"

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




