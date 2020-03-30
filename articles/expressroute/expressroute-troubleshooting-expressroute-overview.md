---
title: 'Azure ExpressRoute: Verify Connectivity - Troubleshooting Guide'
description: Questa pagina fornisce istruzioni sulla risoluzione dei problemi e convalida della connettività end-to-end di un circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330958"
---
# <a name="verifying-expressroute-connectivity"></a>Verifica della connettività di ExpressRoute
Questo articolo fornisce informazioni sulla verifica e la risoluzione dei problemi di connettività di ExpressRoute. ExpressRoute estende una rete locale nel cloud Microsoft tramite una connessione privata comunemente facilitata da un provider di connettività. La connettività ExpressRoute prevede tradizionalmente tre aree di rete distinte, come indicato di seguito:ExpressRoute connectivity traditionally involves three distinct network zones, as follows:

-   Rete del cliente
-   Rete del provider
-   Datacenter Microsoft

> [!NOTE]
> Nel modello di connettività diretta ExpressRoute (offerto a 10/100 Gbps di larghezza di banda), i clienti possono connettersi direttamente alla porta dei router Di Microsoft Enterprise Edge (MSEE). Pertanto, nel modello di connettività diretta, sono presenti solo le aree di rete client e Microsoft.Therefore, in the direct connectivity model, there are only customer and Microsoft network zones.
>


Lo scopo di questo documento è aiutare l'utente a identificare se e dove si verifica un problema di connettività. Di seguito, per aiutare a cercare il supporto del team appropriato per risolvere un problema. Se per risolvere un problema è necessaria l'assistenza tecnica di Microsoft, aprire un ticket di supporto al [supporto tecnico Microsoft][Support].

> [!IMPORTANT]
> Questo documento è pensato per aiutare l'utente a rilevare e risolvere problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Se non si riesce a risolvere il problema tramite la procedura descritta, è necessario aprire un ticket di supporto al [supporto tecnico Microsoft][Support].
>
>

## <a name="overview"></a>Panoramica
Il diagramma seguente illustra la connettività logica della rete di un cliente alla rete Microsoft usando ExpressRoute.
[![1]][1]

Nel diagramma precedente i numeri indicano i punti principali delle reti. Questi punti di rete sono referenziati in questo articolo a volte in base al numero associato. A seconda del modello di connettività ExpressRoute: Cloud Exchange Co-location, Point-to-Point Ethernet Connection o Any-to-any (IPVPN), i punti di rete 3 e 4 possono essere switch (dispositivi di livello 2) o router (dispositivi di livello 3). Nel modello di connettività diretta, non sono presenti punti di rete 3 e 4; invece, i CE (2) sono collegati direttamente agli MSEt tramite fibra scura. I punti principali delle reti illustrati sono i seguenti:

1.  Dispositivo di calcolo del cliente (ad esempio, un server o un PC)
2.  CE: router perimetrali del cliente 
3.  PE (rivolti verso i CE): router/switch perimetrali del provider rivolti verso i router perimetrali del cliente. Indicata con il nome di PE-CE nel presente documento.
4.  PE (rivolti verso gli MSEE): router/switch perimetrali del provider rivolti verso gli MSEE. Indicata con il nome di PE-MSEE nel presente documento.
5.  MSEE: router ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Gateway di rete virtuale (VNet)
7.  Dispositivo di calcolo sulla rete virtuale di Azure

Se vengono utilizzati i modelli di connettività diretta, Co-location cloud Exchange o Ethernet point o di connettività diretta, i Ce (2) stabiliscono il peering BGP con MSE (5). 

Se viene utilizzato il modello di connettività Any-to-any (IPVPN), PE-MSEEs (4) stabilisce il peering BGP con MSE (5). PE-MSEE propaga le route ricevute da Microsoft alla rete del cliente tramite la rete di provider di servizi IPVPN.

> [!NOTE]
>Per la disponibilità elevata, Microsoft stabilisce una connettività parallela completamente ridondante tra le coppie MSEE (5) e PE-MSEE (4). Un percorso di rete parallela completamente ridondante è inoltre incoraggiato tra la rete del cliente e la coppia PE-CE. Per altre informazioni sulla disponibilità elevata, vedere l'articolo [Progettazione per la disponibilità elevata con ExpressRouteFor][HA] more information regarding high availability, see the article Designing for high availability with ExpressRoute
>
>

Di seguito sono riportati i passaggi logici per la risoluzione dei problemi relativi al circuito ExpressRoute:The following are the logical steps, in troubleshooting ExpressRoute circuit:

* [Verificare il provisioning e lo stato del circuito](#verify-circuit-provisioning-and-state)
  
* [Convalidare la configurazione del peering](#validate-peering-configuration)
  
* [Convalida ARP](#validate-arp)
  
* [Convalidare BGP e route sul MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Confermare il flusso di traffico](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificare il provisioning e lo stato del circuito
Il provisioning di un circuito ExpressRoute stabilisce una connessione ridondante di livello 2 tra CE/PE-MSEEs (2)/(4) e MSEE (5). Per altre informazioni su come creare, modificare, eseguire il provisioning e verificare un circuito ExpressRoute, vedere l'articolo [Creare e modificare un circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Una chiave del servizio identifica in modo univoco un circuito ExpressRoute. Se è necessaria assistenza da Microsoft o da un partner ExpressRoute per risolvere un problema di ExpressRoute, fornire la chiave del servizio per identificare facilmente il circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure
Nel portale di Azure aprire il pannello Circuito ExpressRoute.In the Azure portal, open the ExpressRoute circuit blade. Nella sezione 3 del pannello, gli elementi essenziali di ExpressRoute sono elencati come illustrato nella schermata seguente:In the ![3][3] section of the blade, the ExpressRoute essentials are listed as shown in the following screenshot:

![4][4]    

Nelle informazioni di base di ExpressRoute *Stato circuito* indica lo stato del circuito sul lato Microsoft. *Stato provider* indica se si tratta di un circuito con *Provisioning eseguito/Senza provisioning* sul lato del provider dei servizi. 

Per consentire il funzionamento di un circuito ExpressRoute, *Stato circuito* deve essere impostato su *Abilitato* e *Stato provider* su *Provisioning eseguito*.

> [!NOTE]
> Dopo aver configurato un circuito ExpressRoute, se *lo stato* del circuito viene raggiunto con stato non abilitato, contattare il supporto [tecnico Microsoft][Support]. D'altra parte, se *lo stato* del provider viene raggiunto nello stato non di cui è stato eseguito il provisioning, contattare il provider di servizi.
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
> Dopo aver configurato un circuito ExpressRoute, se *lo stato* del circuito viene raggiunto con stato non abilitato, contattare il supporto [tecnico Microsoft][Support]. D'altra parte, se *lo stato* del provider viene raggiunto nello stato non di cui è stato eseguito il provisioning, contattare il provider di servizi.
>
>

## <a name="validate-peering-configuration"></a>Convalidare la configurazione del peering
Dopo che il provider di servizi ha completato il provisioning del circuito ExpressRoute, è possibile creare più configurazioni di routing basate su eBGP sul circuito ExpressRoute tra CE/MSEE-PEs (2)/(4) e MSE (5). Ogni circuito ExpressRoute può avere: peering privato di Azure (traffico verso reti virtuali private in Azure) e/o peering Microsoft (traffico verso endpoint pubblici di PaaS e SaaS). Per altre informazioni su come creare e modificare la configurazione di routing, vedere l'articolo [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure

> [!NOTE]
> Nel modello di connettività IPVPN, i provider di servizi gestiscono la responsabilità di configurare i peering (servizi di livello 3). In un modello di questo tipo, dopo che il provider di servizi ha configurato un peering e se il peering è vuoto nel portale, provare ad aggiornare la configurazione del circuito utilizzando il pulsante Aggiorna nel portale. Questa operazione estrarrà la configurazione di routing corrente dal circuito. 
>

Nel portale di Azure lo stato di un peering del circuito ExpressRoute può essere controllato nel pannello Circuito ExpressRoute.In the Azure portal, status of an ExpressRoute circuit peering can be checked under the ExpressRoute circuit blade. Nella sezione 3 del pannello, i peering ExpressRoute verranno elencati come illustrato nella schermata seguente:In the ![3][3] section of the blade, the ExpressRoute peerings would be listed as shown in the following screenshot:

![5][5]

Nell'esempio precedente viene eseguito il provisioning del peering privato di Azure, mentre non viene eseguito il provisioning dei peer pubblici di Azure e di Microsoft.In the preceding example, as noted Azure private peering is provisioned, whereas Azure public and Microsoft peerings are not provisioned. In un contesto di peering sottoposto a provisioning riuscito sono elencate anche le subnet point-to-point primarie e secondarie. Le subnet /30 vengono utilizzate per l'indirizzo IP di interfaccia degli MSE e dei CE/PE-MSEE. Per i peering di cui viene eseguito il provisioning, l'elenco indica anche chi ha modificato la configurazione per ultimo. 

> [!NOTE]
> Se l'abilitazione di un peering non riesce, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione nella CE/PE-MSEE collegata. Verificare inoltre se nei MSE vengono utilizzati i valori *corretti VlanId*, *AzureASN*e *PeerASN* in MSEE e se questi valori sono mappati a quelli utilizzati nel file CE/PE-MSEE collegato. Se si preveda l'hashing MD5, la chiave condivisa deve essere la stessa nella coppia MSEE e PE-MSEE/CE. La chiave condivisa configurata in precedenza non viene visualizzata per motivi di sicurezza. Se è necessario modificare una di queste configurazioni su un router MSEE, fare riferimento a Creare e modificare il [routing per un circuito ExpressRoute][CreatePeering].  
>

> [!NOTE]
> In una subnet /30 assegnata per l'interfaccia, Microsoft sceglierà il secondo indirizzo IP utilizzabile della subnet per l'interfaccia MSEE. Pertanto, assicurarsi che il primo indirizzo IP utilizzabile della subnet sia stato assegnato sul CE/PE-MSEE con peering.
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

 In un contesto di peering abilitato correttamente vengono elencati i prefissi di indirizzi primari e secondari. Le subnet /30 vengono utilizzate per l'indirizzo IP di interfaccia degli MSE e dei CE/PE-MSEE.

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
> Se l'abilitazione di un peering non riesce, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione nella CE/PE-MSEE collegata. Verificare inoltre se nei MSE vengono utilizzati i valori *corretti VlanId*, *AzureASN*e *PeerASN* in MSEE e se questi valori sono mappati a quelli utilizzati nel file CE/PE-MSEE collegato. Se si preveda l'hashing MD5, la chiave condivisa deve essere la stessa nella coppia MSEE e PE-MSEE/CE. La chiave condivisa configurata in precedenza non viene visualizzata per motivi di sicurezza. Se è necessario modificare una di queste configurazioni su un router MSEE, fare riferimento a Creare e modificare il [routing per un circuito ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> In una subnet /30 assegnata per l'interfaccia, Microsoft sceglierà il secondo indirizzo IP utilizzabile della subnet per l'interfaccia MSEE. Pertanto, assicurarsi che il primo indirizzo IP utilizzabile della subnet sia stato assegnato sul CE/PE-MSEE con peering.
>

## <a name="validate-arp"></a>Convalida ARP

La tabella ARP fornisce un mapping dell'indirizzo IP e dell'indirizzo MAC per un peering specifico. La tabella ARP del peering di un circuito ExpressRoute fornisce le informazioni seguenti per ogni interfaccia (primaria e secondaria):
* Mapping dell'indirizzo IP dell'interfaccia del router locale all'indirizzo MAC
* Mapping dell'indirizzo IP dell'interfaccia del router di ExpressRoute all'indirizzo MAC
* L'età delle tabelle ARP di mapping consente di convalidare la configurazione di livello 2 e risolvere i problemi di connettività di livello 2 di base.


Per informazioni su come visualizzare la tabella ARP di un peering ExpressRoute e per informazioni su come usare le informazioni per risolvere il problema di connettività di livello 2, vedere Recupero di tabelle ARP nel documento del modello di distribuzione di Resource Manager, per informazioni su come visualizzare la tabella ARP di un peering ExpressRoute e per usare le informazioni per risolvere il problema di connettività di livello 2.See [Getting ARP tables in][ARP] the Resource Manager deployment model document, for how to view the ARP table of an ExpressRoute peering, and for how to use the information to troubleshoot layer 2 connectivity issue.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Convalidare BGP e route sul MSEE

Per ottenere la tabella di routing da MSEE nel percorso *primario* per il contesto di routing *Private,* utilizzare il comando seguente:

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
> Se lo stato di un peering eBGP tra un MSEE e un ME/PE-MSEE è attivo o inattivo, verificare se le subnet peer primarie e secondarie assegnate corrispondono alla configurazione nella versione CAB/PE-MSEE collegata. Verificare inoltre se nei MSE o in *PeerAsn* Cui questi valori sono stati utilizzati i valori *corretti*vengono utilizzati in MSEE e se questi valori sono mappati a quelli utilizzati nel PE-MSEE/CE collegato. *AzureAsn* Se si preveda l'hashing MD5, la chiave condivisa deve essere la stessa nelle coppie MSEE e CE/PE-MSEE. Se è necessario modificare una di queste configurazioni su un router MSEE, fare riferimento a Creare e modificare il [routing per un circuito ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se alcune destinazioni non sono raggiungibili tramite un peering, controllare la tabella di route degli MSE per il contesto di peering corrispondente. Se nella tabella di routing è presente un prefisso corrispondente (potrebbe essere UN indirizzo IP NAT), verificare se sono presenti firewall/NSG/ACL nel percorso che bloccano il traffico.
>


Nell'esempio seguente viene illustrata la risposta del comando per un peering che non esiste:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Confermare il flusso di traffico
Per ottenere le statistiche sul traffico del percorso primario e secondario combinato, byte in entrata e in uscita, di un contesto di peering, usare il comando seguente:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

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





