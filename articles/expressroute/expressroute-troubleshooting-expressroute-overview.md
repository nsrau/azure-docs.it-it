---
title: 'Azure ExpressRoute: verificare la connettività-Guida alla risoluzione dei problemi'
description: Questa pagina fornisce istruzioni sulla risoluzione dei problemi e convalida della connettività end-to-end di un circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 833e7788a5b17b12ad883f705d56f660c2f1d832
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033782"
---
# <a name="verifying-expressroute-connectivity"></a>Verifica della connettività di ExpressRoute
Questo articolo fornisce informazioni sulla verifica e la risoluzione dei problemi di connettività di ExpressRoute. ExpressRoute estende una rete locale nel cloud Microsoft tramite una connessione privata, comunemente facilitata da un provider di connettività. La connettività ExpressRoute prevede tradizionalmente tre aree di rete distinte, come indicato di seguito:

-   Rete del cliente
-   Rete del provider
-   Datacenter Microsoft

> [!NOTE]
> Nel modello di connettività diretta di ExpressRoute (offerta a 10/100 Gbps di larghezza di banda), cusomters è in grado di connettersi direttamente alla porta router Microsoft Enterprise Edge (MSEE). Nel modello di connettività diretta sono pertanto disponibili solo le aree di rete Customer e Microsoft.
>


Lo scopo di questo documento è aiutare gli utenti a identificare se e dove esiste un problema di connettività. Quindi, per aiutare a cercare il supporto del team appropriato per risolvere un problema. Se è necessario il supporto tecnico Microsoft per risolvere un problema, aprire un ticket di supporto con [supporto tecnico Microsoft][Support].

> [!IMPORTANT]
> Questo documento è pensato per aiutare l'utente a rilevare e risolvere problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Aprire un ticket di supporto con [supporto tecnico Microsoft][Support] se non si riesce a risolvere il problema seguendo le indicazioni fornite.
>
>

## <a name="overview"></a>Overview
Il diagramma seguente illustra la connettività logica della rete di un cliente alla rete Microsoft usando ExpressRoute.
[![1]][1]

Nel diagramma precedente i numeri indicano i punti principali delle reti. Questo articolo fa riferimento a questi punti di rete a volte in base al numero associato. A seconda del modello di connettività ExpressRoute--condivisione percorso cloud Exchange, connessione Ethernet da punto a punto o any-to-any (IPVPN)-i punti di rete 3 e 4 possono essere commutatori (dispositivi di livello 2) o router (dispositivi di livello 3). Nel modello di connettività diretta non sono presenti punti di rete 3 e 4; al contrario, il CEs (2) è connesso direttamente a MSEE tramite Fiber scuro. I punti principali delle reti illustrati sono i seguenti:

1.  Dispositivo di calcolo del cliente (ad esempio, un server o un PC)
2.  CE: router perimetrali del cliente 
3.  PE (rivolti verso i CE): router/switch perimetrali del provider rivolti verso i router perimetrali del cliente. Indicata con il nome di PE-CE nel presente documento.
4.  PE (rivolti verso gli MSEE): router/switch perimetrali del provider rivolti verso gli MSEE. Indicata con il nome di PE-MSEE nel presente documento.
5.  MSEE: router ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Gateway di rete virtuale (VNet)
7.  Dispositivo di calcolo sulla rete virtuale di Azure

Se si usano i modelli di condivisione percorso per cloud Exchange, Ethernet da punto a punto o di connettività diretta, CEs (2) stabilisce il peering BGP con MSEE (5). 

Se viene usato il modello di connettività any-to-any (IPVPN), PE-MSEE (4) stabilisce il peering BGP con MSEE (5). PE-MSEE propaga le route ricevute da Microsoft alla rete del cliente tramite la rete del provider di servizi IPVPN.

> [!NOTE]
>Per la disponibilità elevata, Microsoft stabilisce una connettività parallela completamente ridondante tra le coppie MSEE (5) e PE-MSEE (4). Viene inoltre suggerito un percorso di rete parallela completamente ridondante tra la rete del cliente e la coppia PE-CEs. Per altre informazioni sulla disponibilità elevata, vedere l'articolo [progettazione per la disponibilità elevata con ExpressRoute][HA]
>
>

Di seguito sono riportati i passaggi logici per la risoluzione dei problemi relativi al circuito ExpressRoute:

* [Verificare lo stato e il provisioning del circuito](#verify-circuit-provisioning-and-state)
  
* [Convalida configurazione peering](#validate-peering-configuration)
  
* [Convalida ARP](#validate-arp)
  
* [Convalidare BGP e route nel MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Verificare il flusso del traffico](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificare lo stato e il provisioning del circuito
Il provisioning di un circuito ExpressRoute stabilisce connessioni ridondanti di livello 2 tra CEs/PE-MSEE (2)/(4) e MSEE (5). Per altre informazioni su come creare, modificare, effettuare il provisioning e verificare un circuito ExpressRoute, vedere l'articolo [creare e modificare un circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Una chiave del servizio identifica in modo univoco un circuito ExpressRoute. Per richiedere assistenza da parte di Microsoft o da un partner ExpressRoute per risolvere un problema di ExpressRoute, fornire la chiave del servizio per identificare prontamente il circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure
Nel portale di Azure aprire il pannello del circuito ExpressRoute. Nella sezione ![3][3] del pannello i concetti di base di ExpressRoute sono elencati come illustrato nello screenshot seguente:

![4][4]    

Nelle informazioni di base di ExpressRoute *Stato circuito* indica lo stato del circuito sul lato Microsoft. *Stato provider* indica se si tratta di un circuito con *Provisioning eseguito/Senza provisioning* sul lato del provider dei servizi. 

Per consentire il funzionamento di un circuito ExpressRoute, *Stato circuito* deve essere impostato su *Abilitato* e *Stato provider* su *Provisioning eseguito*.

> [!NOTE]
> Dopo aver configurato un circuito ExpressRoute, se lo stato del *circuito* è inattivo, contattare [supporto tecnico Microsoft][Support]. D'altra parte, se lo stato del *provider* viene raggiunto senza provisioning, contattare il provider di servizi.
>
>

### <a name="verification-via-powershell"></a>Verifica tramite PowerShell
Per elencare tutti i circuiti ExpressRoute in un gruppo di risorse, usare il comando seguente:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Se si sta cercando il nome di un gruppo di risorse, è possibile ottenerlo elencando tutti i gruppi di risorse nella sottoscrizione, usando il comando *Get-AzResourceGroup*
>


Per selezionare uno specifico circuito ExpressRoute in un gruppo di risorse, usare il comando seguente:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Una risposta di esempio:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Per verificare se un circuito ExpressRoute è operativo, prestare particolare attenzione ai campi seguenti:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Dopo aver configurato un circuito ExpressRoute, se lo stato del *circuito* è inattivo, contattare [supporto tecnico Microsoft][Support]. D'altra parte, se lo stato del *provider* viene raggiunto senza provisioning, contattare il provider di servizi.
>
>

## <a name="validate-peering-configuration"></a>Convalidare la configurazione del peering
Al termine del provisioning del circuito ExpressRoute da parte del provider di servizi, è possibile creare più configurazioni di routing basate su eBGP sul circuito ExpressRoute tra CEs/MSEE-PEs (2)/(4) e MSEE (5). Ogni circuito ExpressRoute può avere: peering privato di Azure (traffico verso reti virtuali private in Azure) e/o peering Microsoft (traffico verso endpoint pubblici di PaaS e SaaS). Per altre informazioni su come creare e modificare la configurazione del routing, vedere l'articolo [creare e modificare il routing per un circuito ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure

> [!NOTE]
> Nel modello di connettività IPVPN i provider di servizi gestiscono la responsabilità della configurazione dei peering (servizi di livello 3). In un modello di questo tipo, dopo che il provider di servizi ha configurato un peering e se il peering è vuoto nel portale, provare ad aggiornare la configurazione del circuito usando il pulsante Aggiorna del portale. Questa operazione effettuerà il pull della configurazione di routing corrente dal circuito. 
>

Nella portale di Azure è possibile controllare lo stato di un peering del circuito ExpressRoute nel pannello del circuito ExpressRoute. Nella sezione ![3][3] del pannello i peering ExpressRoute verranno elencati come illustrato nello screenshot seguente:

![5][5]

Nell'esempio precedente viene eseguito il provisioning del peering privato di Azure, mentre il provisioning dei peering pubblico e Microsoft di Azure non viene eseguito. Un contesto di peering di cui è stato effettuato il provisioning ha anche le subnet Point-to-Point primarie e secondarie elencate. Le subnet/30 vengono usate per l'indirizzo IP dell'interfaccia di MSEE e CEs/PE-MSEE. Per i peering di cui è stato effettuato il provisioning, l'elenco indica anche chi ha eseguito l'ultima modifica della configurazione. 

> [!NOTE]
> Se l'abilitazione di un peering ha esito negativo, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione in CE/PE-MSEE collegato. Controllare anche se i valori di *VlanId*, *AzureASN*e *Peerasn sugli* corretti vengono usati in MSEE e se questi valori vengono mappati a quelli usati nell'EC/PE-MSEE collegato. Se si sceglie l'hash MD5, la chiave condivisa deve essere identica nella coppia MSEE e PE-MSEE/CE. La chiave condivisa configurata in precedenza non viene visualizzata per motivi di sicurezza. Se è necessario modificare una di queste configurazioni in un router MSEE, vedere [creare e modificare il routing per un circuito ExpressRoute][CreatePeering].  
>

> [!NOTE]
> In una subnet/30 assegnata per l'interfaccia Microsoft sceglierà il secondo indirizzo IP utilizzabile della subnet per l'interfaccia MSEE. Pertanto, assicurarsi che il primo indirizzo IP utilizzabile della subnet sia stato assegnato nella CE/PE-MSEE con peering.
>


### <a name="verification-via-powershell"></a>Verifica tramite PowerShell
Per ottenere i dettagli di configurazione del peering privato di Azure, usare i comandi seguenti:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Una risposta di esempio, per un peering privato configurato correttamente:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 In un contesto di peering abilitato correttamente vengono elencati i prefissi di indirizzi primari e secondari. Le subnet/30 vengono usate per l'indirizzo IP dell'interfaccia di MSEE e CEs/PE-MSEE.

Per ottenere i dettagli di configurazione del peering pubblico di Azure, usare i comandi seguenti:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Per ottenere i dettagli di configurazione del peering Microsoft, usare i comandi seguenti:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Se non è configurato alcun peering, viene visualizzato un messaggio di errore. Una risposta di esempio, quando il peering indicato (peering pubblico di Azure in questo esempio) non è configurato all'interno del circuito:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Se l'abilitazione di un peering ha esito negativo, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione in CE/PE-MSEE collegato. Controllare anche se i valori di *VlanId*, *AzureASN*e *Peerasn sugli* corretti vengono usati in MSEE e se questi valori vengono mappati a quelli usati nell'EC/PE-MSEE collegato. Se si sceglie l'hash MD5, la chiave condivisa deve essere identica nella coppia MSEE e PE-MSEE/CE. La chiave condivisa configurata in precedenza non viene visualizzata per motivi di sicurezza. Se è necessario modificare una di queste configurazioni in un router MSEE, vedere [creare e modificare il routing per un circuito ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> In una subnet/30 assegnata per l'interfaccia Microsoft sceglierà il secondo indirizzo IP utilizzabile della subnet per l'interfaccia MSEE. Pertanto, assicurarsi che il primo indirizzo IP utilizzabile della subnet sia stato assegnato nella CE/PE-MSEE con peering.
>

## <a name="validate-arp"></a>Convalida ARP

La tabella ARP fornisce un mapping dell'indirizzo IP e dell'indirizzo MAC per un particolare peering. La tabella ARP del peering di un circuito ExpressRoute fornisce le informazioni seguenti per ogni interfaccia (primaria e secondaria):
* Mapping dell'indirizzo IP dell'interfaccia del router locale all'indirizzo MAC
* Mapping dell'indirizzo IP dell'interfaccia del router di ExpressRoute all'indirizzo MAC
* Age of the mapping ARP Tables consente di convalidare la configurazione di livello 2 e la risoluzione dei problemi di base di connettività di livello 2.


Per informazioni su come visualizzare la tabella ARP di un peering ExpressRoute e su come usare le informazioni per risolvere il problema di connettività di livello 2, vedere [ottenere le tabelle ARP nel documento del modello di distribuzione gestione risorse][ARP] .


## <a name="validate-bgp-and-routes-on-the-msee"></a>Convalidare BGP e route sul MSEE

Per ottenere la tabella di routing da MSEE nel percorso *primario* per il contesto di routing *privato* , usare il comando seguente:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Una risposta di esempio:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Se lo stato di un peering eBGP tra un MSEE e una CE/PE-MSEE è attivo o inattivo, verificare se le subnet peer primarie e secondarie assegnate corrispondono alla configurazione sull'EC/PE-MSEE collegato. Controllare anche se *VlanId*, *AzureAsn*e *Peerasn sugli* corretti vengono usati in MSEE e se questi valori sono mappati a quelli usati nel PE-MSEE/CE collegato. Se si sceglie l'hash MD5, la chiave condivisa deve essere identica nella coppia MSEE e CE/PE-MSEE. Se è necessario modificare una di queste configurazioni in un router MSEE, vedere [creare e modificare il routing per un circuito ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se alcune destinazioni non sono raggiungibili tramite un peering, controllare la tabella di route di MSEE per il contesto di peering corrispondente. Se nella tabella di routing è presente un prefisso corrispondente (potrebbe essere un indirizzo IP NATe), controllare se sono presenti firewall/NSG/ACL nel percorso che blocca il traffico.
>


Nell'esempio seguente viene illustrata la risposta del comando per un peering inesistente:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Verificare il flusso del traffico
Per ottenere le statistiche sul traffico del percorso primario e secondario combinato, byte in entrata e in uscita, di un contesto di peering, usare il comando seguente:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Output di esempio del comando:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Output di esempio del comando per un peering inesistente:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Passaggi successivi
Per maggiori informazioni o assistenza, consultare i collegamenti seguenti:

- [Supporto tecnico Microsoft][Support]
- [Creare e modificare un circuito ExpressRoute][CreateCircuit]
- [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Connettività logica ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Icona All resources"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Icona Overview"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Schermata di esempio delle informazioni di base di ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Schermata di esempio delle informazioni di base di ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





