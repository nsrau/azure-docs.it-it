---
title: Uso di UNA VPN S2S come backup per il peering privato di Azure ExpressRoute Documenti Microsoft
description: Questa pagina fornisce consigli sull'architettura per il backup del peering privato di Azure ExpressRoute con VPN S2S.This page provides architectural recommendations for backing up Azure ExpressRoute private peering with S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687851"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Uso della VPN S2S come backup per il peering privato ExpressRouteUsing S2S VPN as a backup for ExpressRoute private peering

Nell'articolo [intitolato Progettazione per il ripristino di emergenza con peering privato ExpressRoute][DR-PP]è stata illustrata la necessità di una soluzione di connettività di backup per una connettività di peering privato ExpressRoute e come usare i circuiti ExpressRoute con ridondanza geografica allo scopo. In this article, let us consider how to leverage and maintain site-to-site (S2S) VPN as a back for ExpressRoute private peering. 

A differenza dei circuiti ExpressRoute con ridondanza geografica, è possibile usare la combinazione di ripristino di emergenza ExpressRoute-VPN solo in modalità attivo-passivo. Una sfida importante dell'utilizzo di qualsiasi connettività di rete di backup in modalità passiva è che la connessione passiva spesso non riesce insieme alla connessione primaria. Il motivo più comune degli errori della connessione passiva è la mancanza di manutenzione attiva. Pertanto, in questo articolo concentreremo su come verificare e gestire attivamente la connettività VPN S2S che esegue il backup di un peering privato ExpressRoute.Therefore, in this article let's focus on how to verify and actively maintain S2S VPN connectivity that is backing up an ExpressRoute private peering.

>[!NOTE] 
>Quando una determinata route viene annunciata tramite ExpressRoute e VPN, Azure preferisce il routing rispetto a ExpressRoute.When a given route is advertised via both ExpressRoute and VPN, Azure would prefer routing over ExpressRoute.  
>

In questo articolo viene illustrato come verificare la connettività sia dal punto di vista di Azure che dal punto di vista del perimetro della rete lato cliente. La possibilità di eseguire la convalida da entrambe le estremità può essere utile indipendentemente dal fatto che si gestiscano o meno i dispositivi di rete lato cliente peer con le entità di rete Microsoft. 

## <a name="example-topology"></a>Topologia di esempio

Nella configurazione, abbiamo una rete locale connessa a una rete virtuale hub di Azure tramite un circuito ExpressRoute e una connessione VPN S2S. La rete virtuale dell'hub di Azure è a sua volta sottoposta a peered a una rete virtuale a raggio, come illustrato nel diagramma seguente:The Azure hub VNet is in turn peered to a spoke VNet, as shown in the diagram below:

![1][1]

Nell'installazione, il circuito ExpressRoute viene terminato su una coppia di router "Customer Edge" (CE) in locale. La LAN locale è connessa ai router CE tramite una coppia di firewall che operano in modalità leader-follower. La VPN S2S viene terminata direttamente sui firewall.

Nella tabella seguente sono elencati i prefissi IP chiave della topologia:

| **Entità** | **Prefisso** |
| --- | --- |
| LAN locale | 10.1.11.0/25 |
| Rete virtuale hub di AzureAzure Hub VNet | 10.17.11.0/25 |
| Rete virtuale a raggio di AzureAzure spoke VNet | 10.17.11.128/26 |
| Server di test locale | 10.1.11.10 |
| VM di test della rete virtuale spoke | 10.17.11.132 |
| Subnet p2p della connessione primaria ExpressRouteExpressRoute primary connection p2p subnet | 192.168.11.16/30 |
| Subnet p2p di connessione secondaria ExpressRouteExpressRoute secondary connection p2p subnet | 192.168.11.20/30 |
| IP peer BGP primario del gateway VPN | 10.17.11.76 |
| IP peer BGP secondario del gateway VPN | 10.17.11.77 |
| IP peer BGP VPN del firewall locale | 192.168.11.88 |
| Router CE primario i/f verso ip firewall | 192.168.11.0/31 |
| Firewall i/f verso l'IP primario del router CE | 192.168.11.1/31 |
| Router CE secondario i/f verso ip firewall | 192.168.11.2/31 |
| Firewall i/f verso l'IP secondario del router CE | 192.168.11.3/31 |


Nella tabella seguente sono elencati gli ASN della topologia:

| **Sistema autonomo** | **Asn** |
| --- | --- |
| Locale | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rete virtuale GW (ExR) | 65515 |
| Rete virtuale GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Disponibilità elevata senza asimmetria

### <a name="configuring-for-high-availability"></a>Configurazione per la disponibilità elevata

[Configure ExpressRoute e le connessioni coesistenti][Conf-CoExist] da sito a sito illustrano come configurare il circuito ExpressRoute coesistente e le connessioni VPN S2S. Come illustrato in [Progettazione per la disponibilità elevata con ExpressRoute][HA], per migliorare la disponibilità elevata ExpressRoute la configurazione mantiene la ridondanza di rete (evita un singolo punto di errore) fino agli endpoint. Inoltre, le connessioni primarie e secondarie dei circuiti ExpressRoute sono configurate per operare in modalità attivo-attivo annunciando i prefissi locali allo stesso modo tramite entrambe le connessioni. 

L'annuncio di route locale del router CE primario tramite la connessione primaria del circuito ExpressRoute è illustrato di seguito (comandi Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

L'annuncio di route locale del router CE secondario tramite la connessione secondaria del circuito ExpressRoute è illustrato di seguito (comandi Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Per migliorare l'elevata disponibilità della connessione di backup, la VPN S2S viene configurata anche in modalità attivo-attivo. La configurazione del gateway VPN di Azure è illustrata di seguito. Notare che, come parte della VPN di configurazione VPN, vengono elencati anche gli indirizzi IP peer BGP del gateway, ovvero 10.17.11.76 e 10.17.11.77.

![2][2]

La route locale viene annunciata dai firewall ai peer BGP primari e secondari del gateway VPN. Gli annunci di percorso sono mostrati di seguito (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>La configurazione della VPN S2S in modalità attivo-attivo non solo garantisce un'elevata disponibilità alla connettività di rete di backup di ripristino di emergenza, ma fornisce anche una maggiore velocità effettiva per la connettività di backup. In altre parole, la configurazione della VPN S2S in modalità attivo-attivo è consigliata in quanto forza la creazione di più tunnel sottostanti.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configurazione per il flusso di traffico simmetrico

Abbiamo notato che quando una determinata route locale viene annunciata tramite la VPN ExpressRoute e S2S, Azure preferisce il percorso ExpressRoute.We noted that when a given on-premises route is advertised via both ExpressRoute and S2S VPN, Azure would prefer the ExpressRoute path. Per forzare Azure preferisce il percorso VPN S2S rispetto al percorso ExpressRoute coesistente, è necessario annunciare route più specifiche (prefisso più lungo con subnet mask più grande) tramite la connessione VPN. Il nostro obiettivo qui è quello di utilizzare le connessioni VPN come solo indietro. Pertanto, il comportamento di selezione del percorso predefinito di Azure è in linea con l'obiettivo. 

È nostra responsabilità garantire che il traffico destinato ad Azure dall'ambiente locale preferisca anche il percorso ExpressRoute rispetto alla VPN S2S.It is our responsibility to ensure that the traffic destined to Azure from on-premises also prefers ExpressRoute path over S2S VPN. La preferenza locale predefinita dei router e dei firewall CE nella configurazione locale è 100. Pertanto, configurando la preferenza locale delle route ricevute tramite i peering privati ExpressRoute superiori a 100 (ad esempio 150), è possibile rendere il traffico destinato ad Azure preferire il circuito ExpressRoute nello stato stabile.

Di seguito è illustrata la configurazione BGP del router CE primario che termina la connessione primaria del circuito ExpressRoute. Si noti che il valore della preferenza locale delle route annunciate sulla sessione iBGP è configurato su 150. Analogamente, è necessario garantire che anche la preferenza locale del router CE secondario che termina la connessione secondaria del circuito ExpressRoute sia configurata su 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

La tabella di routing dei firewall locali conferma (mostrata di seguito) che per il traffico locale destinato ad Azure il percorso preferito è over ExpressRoute nello stato stabile.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

Nella tabella di route precedente, per le route di route hub e spoke VNet--10.17.11.0/25 e 10.17.11.128/26: il circuito ExpressRoute è preferito rispetto alle connessioni VPN. Gli indirizzi IP 192.168.11.0 e 192.168.11.2 sono indirizzi IP sull'interfaccia firewall verso i router CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Convalida dello scambio di route su S2S VPN

In precedenza in questo articolo è stato verificato l'annuncio di route locale dei firewall ai peer BGP primario e secondario del gateway VPN. Inoltre, confermiamo le route di Azure ricevute dai firewall dai peer BGP primari e secondari del gateway VPN.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Analogamente, verifichiamo i prefissi di route di rete locali ricevuti dal gateway VPN di Azure.Similarly let's verify for on-premises network route prefixes received by the Azure VPN gateway. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Come illustrato in precedenza, il gateway VPN ha route ricevute sia dai peer BGP primari o secondari del gateway VPN. Ha anche visibilità sulle route ricevute tramite connessioni ExpressRoute primarie e secondarie (quelle con AS-path precedute da 12076). Per confermare le route ricevute tramite connessioni VPN, è necessario conoscere l'IP peer BGP locale delle connessioni. Nel nostro setup in esame, è 192.168.11.88 e vediamo i percorsi ricevuti da esso.

Verifichiamo quindi le route annunciate dal gateway VPN di Azure al peer BGP del firewall locale (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


La mancata comunicazione degli scambi di route indica un errore di connessione. Vedere [Risoluzione dei problemi: una connessione VPN da sito a sito di Azure non può connettersi e smette][VPN Troubleshoot] di funzionare per informazioni sulla risoluzione dei problemi della connessione VPN.

## <a name="testing-failover"></a>Failover di testTesting failover

Ora che sono stati confermati gli scambi di route riusciti tramite la connessione VPN (piano di controllo), siamo impostati per cambiare il traffico (piano dati) dalla connettività ExpressRoute alla connettività VPN. 

>[!NOTE] 
>Negli ambienti di produzione il test di failover deve essere eseguito durante la finestra di lavoro manutenzione di rete pianificata in quanto può essere dirompente per il servizio.
>

Prima di eseguire l'opzione del traffico, è possibile tracciare il percorso corrente nella configurazione dal server di test locale alla macchina virtuale di test nella rete virtuale spoke.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

Le subnet di connessione point-to-point ExpressRoute primarie e secondarie della configurazione sono, rispettivamente, 192.168.11.16/30 e 192.168.11.20/30. Nella route di traccia sopra, nel passaggio 3 vediamo che stiamo colpendo 192.168.11.18, che è l'IP di interfaccia del MSEE primario. La presenza dell'interfaccia MSEE conferma che, come previsto, il percorso corrente si trova su ExpressRoute.

Come riportato nel percorso [Reimposta peering][RST]del circuito ExpressRoute , utilizziamo i comandi di PowerShell seguenti per disabilitare il peering primario e secondario del circuito ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Il tempo dell'interruttore di failover dipende dal tempo di convergenza BGP. Nella configurazione, l'opzione di failover richiede alcuni secondi (meno di 10). Dopo l'opzione, la ripetizione del traceroute mostra il percorso seguente:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Il risultato del traceroute conferma che la connessione di backup tramite VPN S2S è attiva e può fornire continuità del servizio in caso di errore delle connessioni ExpressRoute primarie e secondarie. Per completare il test del failover, è possibile abilitare le connessioni ExpressRoute indietro e normalizzare il flusso di traffico, usando il set di comandi seguente.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Per verificare che il traffico sia passato a ExpressRoute, ripetere il traceroute e assicurarsi che venga eseguito tramite il peering privato ExpressRoute.To confirm the traffic is switched back to ExpressRoute, repeat the traceroute and ensure that it is going through the ExpressRoute private peering.

## <a name="next-steps"></a>Passaggi successivi

ExpressRoute è progettato per la disponibilità elevata senza un singolo punto di errore all'interno della rete Microsoft.ExpressRoute is designed for high availability with no single point of failure within the Microsoft network. Ancora un circuito ExpressRoute è limitato a una singola area geografica e a un provider di servizi. S2S VPN può essere una buona soluzione di backup passivo di ripristino di emergenza in un circuito ExpressRoute.S2S VPN can be a good disaster recovery passive backup solution to an ExpressRoute circuit. Per una soluzione affidabile per la connessione di backup passivo, la manutenzione regolare della configurazione passiva e la convalida periodica della connessione sono importanti. È essenziale non lasciare che la configurazione VPN diventi obsoleta e ripetere periodicamente (ad esempio ogni trimestre) i passaggi di test di convalida e failover descritti in questo articolo durante la finestra di manutenzione.

Per abilitare il monitoraggio e gli avvisi in base alle metriche del gateway VPN, vedere [Configurare avvisi sulle metriche del gateway VPN.][VPN-alerts]

Per accelerare la convergenza BGP in seguito a un errore ExpressRoute, [configurare BFD su ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia in esame"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configurazione VPN GW"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



