---
title: Uso della VPN S2S come backup per il peering privato di Azure ExpressRoute | Microsoft Docs
description: Questa pagina fornisce consigli sull'architettura per il backup del peering privato di Azure ExpressRoute con VPN S2S.
services: networking
author: rambk
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: cb6ebdcae837216efac5b9333789dee032219251
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738091"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Uso della VPN S2S come backup per il peering privato di ExpressRoute

Nell'articolo intitolato [progettazione per il ripristino di emergenza con peering privato ExpressRoute][DR-PP], è stata discussa la necessità di una soluzione di connettività di backup per una connettività di peering privato ExpressRoute e come usare circuiti ExpressRoute con ridondanza geografica per lo scopo. Questo articolo illustra come usare e gestire la VPN da sito a sito (S2S) come back per il peering privato di ExpressRoute. 

A differenza dei circuiti ExpressRoute con ridondanza geografica, è possibile usare la combinazione di ripristino di emergenza ExpressRoute-VPN solo in modalità attivo/passivo. Un problema principale dell'utilizzo della connettività di rete di backup in modalità passiva è che la connessione passiva avrebbe spesso esito negativo insieme alla connessione primaria. La causa comune degli errori della connessione passiva è la mancanza di manutenzione attiva. Pertanto, in questo articolo si concentrerà su come verificare e mantenere attivamente la connettività VPN S2S che esegue il backup di un peering privato ExpressRoute.

>[!NOTE] 
>Quando una determinata route viene annunciata tramite ExpressRoute e VPN, Azure preferisce il routing rispetto a ExpressRoute.  
>

In questo articolo viene illustrato come verificare la connettività sia dal punto di vista di Azure che dal punto di vista del perimetro della rete lato client. La possibilità di convalidare da entrambe le estremità può essere utile indipendentemente dal fatto che i dispositivi di rete lato cliente vengano gestiti da peer con le entità di rete Microsoft. 

## <a name="example-topology"></a>Topologia di esempio

Nel programma di installazione è presente una rete locale connessa a un VNet Hub di Azure tramite un circuito ExpressRoute e una connessione VPN S2S. Il VNet dell'hub di Azure viene a sua volta sottoposta a peering in un VNet spoke, come illustrato nel diagramma seguente:

![1][1]

Nel programma di installazione, il circuito ExpressRoute viene terminato in una coppia di router "Customer Edge" (CE) in locale. La LAN locale è connessa ai router CE tramite una coppia di firewall che operano in modalità di guida. La VPN S2S viene terminata direttamente nei firewall.

Nella tabella seguente sono elencati i prefissi IP principali della topologia:

| **Entità** | **Prefisso** |
| --- | --- |
| LAN locale | 10.1.11.0/25 |
| VNet Hub di Azure | 10.17.11.0/25 |
| Azure spoke VNet | 10.17.11.128/26 |
| Server di prova locale | 10.1.11.10 |
| VM di test VNet spoke | 10.17.11.132 |
| ExpressRoute subnet P2P della connessione primaria | 192.168.11.16/30 |
| Subnet P2P della connessione secondaria ExpressRoute | 192.168.11.20/30 |
| IP peer BGP primario del gateway VPN | 10.17.11.76 |
| IP del peer BGP secondario del gateway VPN | 10.17.11.77 |
| IP peer BGP VPN firewall locale | 192.168.11.88 |
| I/o del router CE primario per l'IP del firewall | 192.168.11.0/31 |
| I/o del firewall verso l'indirizzo IP del router CE primario | 192.168.11.1/31 |
| I/o del router CE secondario per l'IP del firewall | 192.168.11.2/31 |
| I/o del firewall verso l'IP del router CE secondario | 192.168.11.3/31 |


Nella tabella seguente sono elencati i ASN della topologia:

| **Sistema autonomo** | **ASN** |
| --- | --- |
| Locale | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rete virtuale GW (ExR) | 65515 |
| Rete virtuale GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Disponibilità elevata senza asimmetria

### <a name="configuring-for-high-availability"></a>Configurazione per la disponibilità elevata

[Configurare connessioni coesistenti ExpressRoute e da sito a sito][Conf-CoExist] illustra come configurare il circuito ExpressRoute coesistente e le connessioni VPN S2S. Come illustrato in [progettazione per la disponibilità elevata con ExpressRoute][HA], per migliorare la disponibilità elevata di ExpressRoute la configurazione mantiene la ridondanza della rete (evita il singolo punto di errore) fino agli endpoint. Inoltre, le connessioni primarie e secondarie dei circuiti ExpressRoute sono configurate per essere eseguite in modalità attivo-attivo mediante l'annuncio dei prefissi locali nello stesso modo tramite entrambe le connessioni. 

L'annuncio della route locale del router CE primario attraverso la connessione primaria del circuito ExpressRoute è illustrato di seguito (comandi Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

L'annuncio della route locale del router CE secondario tramite la connessione secondaria del circuito ExpressRoute è illustrato di seguito (comandi di Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Per migliorare la disponibilità elevata della connessione di backup, la VPN S2S viene configurata anche in modalità attivo-attivo. La configurazione del gateway VPN di Azure è illustrata di seguito. Nota come parte della VPN di configurazione VPN, sono elencati anche gli indirizzi IP del peer BGP del gateway, 10.17.11.76 e 10.17.11.77.

![2][2]

La route locale viene annunciata dai firewall ai peer BGP primari e secondari del gateway VPN. Gli annunci di route sono riportati di seguito (Junos):

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
>La configurazione della VPN S2S in modalità Active-Active non solo garantisce la disponibilità elevata per la connettività di rete per il ripristino di emergenza, ma offre anche una velocità effettiva superiore per la connettività di backup. In altre parole, è consigliabile configurare la VPN S2S in modalità Active-Active, perché forza la creazione di più tunnel sottostanti.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configurazione per il flusso del traffico simmetrico

Si noti che quando una determinata route locale viene annunciata tramite la VPN ExpressRoute e S2S, Azure preferisce il percorso ExpressRoute. Per fare in modo che Azure preferisca il percorso VPN S2S sulla ExpressRoute coesistente, è necessario annunciare route più specifiche (prefisso più lungo con subnet mask più grandi) tramite la connessione VPN. Il nostro obiettivo consiste nell'usare le connessioni VPN solo come backup. Il comportamento predefinito per la selezione dei percorsi di Azure è quindi in linea con l'obiettivo. 

È responsabilità del garante che il traffico destinato ad Azure da locale preferisca anche il percorso ExpressRoute sulla VPN S2S. La preferenza locale predefinita dei router CE e dei firewall nell'installazione locale è 100. Quindi, configurando la preferenza locale delle route ricevute tramite i peering privati di ExpressRoute maggiori di 100 (ad indicare 150), è possibile fare in modo che il traffico destinato ad Azure preferisca il circuito ExpressRoute nello stato stazionario.

Di seguito è riportata la configurazione BGP del router CE primario che termina la connessione primaria del circuito ExpressRoute. Si noti che il valore della preferenza locale delle route annunciate sulla sessione iBGP è configurato per essere 150. Analogamente, è necessario assicurarsi che la preferenza locale del router CE secondario che termina la connessione secondaria del circuito ExpressRoute sia configurata anche come 150.

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

La tabella di routing dei firewall locali conferma (mostrata di seguito) che per il traffico locale destinato ad Azure il percorso preferito è rispetto a ExpressRoute nello stato stazionario.

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

Nella tabella di route precedente, per l'hub e spoke VNet Routes--10.17.11.0/25 e 10.17.11.128/26. viene visualizzato il circuito ExpressRoute preferito rispetto alle connessioni VPN. 192.168.11.0 e 192.168.11.2 sono indirizzi IP sull'interfaccia firewall per i router CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Convalida dello scambio delle route tramite VPN S2S

In precedenza in questo articolo è stata verificata l'annuncio delle route locali dei firewall per i peer BGP primari e secondari del gateway VPN. Inoltre, è necessario confermare le route di Azure ricevute dai firewall dai peer BGP primari e secondari del gateway VPN.

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

Analogamente, si verificheranno i prefissi di route di rete locali ricevuti dal gateway VPN di Azure. 

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

Come illustrato in precedenza, il gateway VPN contiene route ricevute dal peer BGP primario e secondario del gateway VPN. Ha anche visibilità sulle route ricevute tramite le connessioni ExpressRoute primarie e secondarie (quelle con il percorso come anteposto a 12076). Per confermare le route ricevute tramite connessioni VPN, è necessario che l'indirizzo IP del peer BGP locale delle connessioni sia noto. Nel corso della configurazione, in considerazione, è 192.168.11.88 e vengono visualizzate le route ricevute.

Successivamente, è possibile verificare le route annunciate dal gateway VPN di Azure al peer BGP del firewall locale (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


La mancata visualizzazione degli scambi di route indica un errore di connessione. Vedere [risoluzione dei problemi: una connessione VPN da sito a sito di Azure non può connettersi e smette di funzionare][VPN Troubleshoot] per informazioni sulla risoluzione dei problemi relativi alla connessione VPN.

## <a name="testing-failover"></a>Test del failover

Ora che sono stati confermati gli scambi di route riusciti sulla connessione VPN (piano di controllo), viene impostato il passaggio del traffico (piano dati) dalla connettività ExpressRoute alla connettività VPN. 

>[!NOTE] 
>Negli ambienti di produzione il test di failover deve essere eseguito durante la manutenzione pianificata della rete-finestra di lavoro, perché può essere un disturbo del servizio.
>

Prima di eseguire il cambio di traffico, è possibile tracciare il percorso corrente dell'installazione dal server di prova locale alla VM di test nel VNet spoke.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

Le subnet di connessione Point-to-Point ExpressRoute primaria e secondaria della configurazione sono rispettivamente 192.168.11.16/30 e 192.168.11.20/30. Nella route di traccia precedente, nel passaggio 3 si noterà che si sta colpendo 192.168.11.18, che è l'indirizzo IP dell'interfaccia del MSEE primario. La presenza dell'interfaccia MSEE conferma che, come previsto, il percorso corrente è sopra il ExpressRoute.

Come indicato nei [peering del circuito ExpressRoute Reset][RST], usare i comandi di PowerShell seguenti per disabilitare il peering primario e secondario del circuito ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Il tempo di commutazione del failover dipende dal tempo di convergenza BGP. Nel programma di installazione, l'opzione di failover richiede pochi secondi (minore di 10). Dopo l'opzione, la ripetizione del traceroute mostra il percorso seguente:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Il risultato del traceroute conferma che la connessione di backup tramite la VPN S2S è attiva e può garantire la continuità del servizio se le connessioni ExpressRoute primarie e secondarie hanno esito negativo. Per completare il test di failover, abilitare le connessioni ExpressRoute e normalizzare il flusso del traffico, usando il set di comandi seguente.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Per confermare che il traffico viene reimpostato su ExpressRoute, ripetere il traceroute e verificare che venga eseguito il peering privato di ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

ExpressRoute è progettato per la disponibilità elevata senza un singolo punto di errore all'interno della rete Microsoft. Un circuito ExpressRoute è comunque limitato a una singola area geografica e a un provider di servizi. La VPN S2S può essere una soluzione di backup passivo per il ripristino di emergenza in un circuito ExpressRoute. Per una soluzione di connessione di backup passiva affidabile, è importante la manutenzione regolare della configurazione passiva e la convalida periodica della connessione. È essenziale non lasciare che la configurazione VPN diventi obsoleta e periodicamente (ad ogni trimestre) Ripetere i passaggi di convalida e failover del test descritti in questo articolo durante la finestra di manutenzione.

Per abilitare il monitoraggio e gli avvisi in base alle metriche del gateway VPN, vedere [configurare gli avvisi per le metriche del gateway VPN][VPN-alerts].

Per accelerare la convergenza BGP in seguito a un errore ExpressRoute, [configurare BFD in ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia in considerazione"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configurazione del GW VPN"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



