---
title: "Verifica della connettività: guida alla risoluzione dei problemi di Azure ExpressRoute | Microsoft Docs"
description: "Questa pagina fornisce istruzioni sulla risoluzione dei problemi e convalida della connettività end-to-end di un circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 960ea1540d644b6f41b95ab7df61cf91adcbb4ad
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="verifying-expressroute-connectivity"></a>Verifica della connettività di ExpressRoute
ExpressRoute, che consente di estendere una rete locale nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività, coinvolge le tre diverse aree di rete seguenti:

-   Rete del cliente
-   Rete del provider
-   Datacenter Microsoft

Lo scopo di questo documento è di aiutare l'utente a identificare dove (o anche se) esiste un problema di connettività e all'interno di quale area, in modo tale da richiedere assistenza al team appropriato per risolvere il problema. Se per risolvere un problema è necessaria l'assistenza tecnica di Microsoft, aprire un ticket di supporto al [supporto tecnico Microsoft][Support].

> [!IMPORTANT]
> Questo documento è pensato per aiutare l'utente a rilevare e risolvere problemi semplici. Non sostituisce tuttavia il supporto tecnico Microsoft. Se non si riesce a risolvere il problema tramite la procedura descritta, è necessario aprire un ticket di supporto al [supporto tecnico Microsoft][Support].
>
>

## <a name="overview"></a>Panoramica
Il diagramma seguente illustra la connettività logica della rete di un cliente alla rete Microsoft usando ExpressRoute.
[![1]][1]

Nel diagramma precedente i numeri indicano i punti principali delle reti. Tali punti sono spesso citati in questo articolo tramite il numero associato.

A seconda del modello di connettività ExpressRoute (condivisione percorso per Cloud Exchange, connessione Ethernet punto a punto o any-to-any (IPVPN)), i punti 3 e 4 della rete potrebbero essere switch (dispositivi di livello 2). I punti principali delle reti illustrati sono i seguenti:

1.  Dispositivo di calcolo del cliente (ad esempio, un server o un PC)
2.  CE: router perimetrali del cliente 
3.  PE (rivolti verso i CE): router/switch perimetrali del provider rivolti verso i router perimetrali del cliente. Indicata con il nome di PE-CE nel presente documento.
4.  PE (rivolti verso gli MSEE): router/switch perimetrali del provider rivolti verso gli MSEE. Indicata con il nome di PE-MSEE nel presente documento.
5.  MSEE: router ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Gateway di rete virtuale (VNet)
7.  Dispositivo di calcolo sulla rete virtuale di Azure

Se vengono usati modelli di connettività di condivisione percorso per Cloud Exchange o connessione Ethernet punto a punto, il router perimetrale del cliente (2) stabilisce il peering BGP con gli MSEE (5). I punti 3 e 4 sono ancora presenti, ma in forma trasparente, come dispositivi di livello 2.

Se si usa il modello di connettività any-to-any (IPVPN), i PE (rivolti verso gli MSEE) (4) stabiliscono il peering BGP con gli MSEE (5). Le route si propagano quindi nuovamente alla rete del cliente attraverso la rete del provider di servizi IPVPN.

>[!NOTE]
>Per la disponibilità elevata di ExpressRoute, Microsoft richiede una coppia ridondante di sessioni BGP tra MSEE (5) e PE-MSEE (4). Si consiglia anche una coppia ridondante di percorsi di rete tra rete del cliente e PE-CE. Tuttavia, nel modello di connessione any-to-any (IPVPN), un singolo dispositivo CE (2) può essere collegato a uno o più PE (3).
>
>

Per convalidare un circuito ExpressRoute, vengono seguiti i seguenti passaggi, con il punto di rete indicato dal numero associato:
1. [Convalidare il provisioning del circuito e lo stato (5)](#validate-circuit-provisioning-and-state)
2. [Convalidare la configurazione di almeno un peering di ExpressRoute (5)](#validate-peering-configuration)
3. [Convalidare ARP tra Microsoft e il provider di servizi (collegamento tra 4 e 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Convalidare BGP e route sul MSEE (BGP tra 4 e 5 e 5 e 6 se una rete virtuale è connessa)](#validate-bgp-and-routes-on-the-msee)
5. [Verificare le statistiche del traffico (traffico che attraversa 5)](#check-the-traffic-statistics)

In futuro verranno aggiunti altri controlli e maggiori convalide. Si consiglia quindi di verificare mensilmente.

##<a name="validate-circuit-provisioning-and-state"></a>Convalidare il provisioning del circuito e lo stato
Indipendentemente dal modello di connettività, è necessario creare un circuito ExpressRoute e, pertanto, generare una chiave del servizio per il provisioning del circuito. Il provisioning di un circuito ExpressRoute stabilisce connessioni ridondanti di livello 2 tra PE-MSEE (4) e MSEE (5). Per altre informazioni su come creare, modificare, eseguire il provisioning e verificare un circuito ExpressRoute, vedere l'articolo [Creare e modificare un circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Una chiave del servizio identifica in modo univoco un circuito ExpressRoute. Questa chiave è necessaria per la maggior parte dei comandi di Powershell indicati in questo documento. Se fosse necessario richiedere l'assistenza di Microsoft o di un partner ExpressRoute per risolvere un problema di ExpressRoute, fornire la chiave del servizio per identificare facilmente il circuito.
>
>

###<a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure
Nel portale di Azure lo stato di un circuito ExpressRoute può essere controllato selezionando ![2][2] nel menu sulla barra laterale sinistra e quindi selezionando il circuito ExpressRoute. La selezione di un circuito ExpressRoute elencato in "All resources" (Tutte le risorse) determina l'apertura del pannello del circuito ExpressRoute. Nella sezione ![3][3] del pannello sono elencate le informazioni di base di ExpressRoute come illustrato nella schermata seguente:

![4][4]    

Nelle informazioni di base di ExpressRoute *Stato circuito* indica lo stato del circuito sul lato Microsoft. *Stato provider* indica se si tratta di un circuito con *Provisioning eseguito/Senza provisioning* sul lato del provider dei servizi. 

Per consentire il funzionamento di un circuito ExpressRoute, *Stato circuito* deve essere impostato su *Abilitato* e *Stato provider* su *Provisioning eseguito*.

>[!NOTE]
>Se *Stato circuito* non è impostato su Abilitato, contattare il [supporto tecnico Microsoft][Support]. Se *Stato provider* è impostato su Senza provisioning, contattare il provider dei servizi.
>
>

###<a name="verification-via-powershell"></a>Verifica tramite PowerShell
Per elencare tutti i circuiti ExpressRoute in un gruppo di risorse, usare il comando seguente:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>È possibile ottenere il nome del gruppo di risorse tramite Azure. Vedere la sezione precedente di questo documento e notare che il nome del gruppo di risorse è elencato nella schermata di esempio.
>
>

Per selezionare uno specifico circuito ExpressRoute in un gruppo di risorse, usare il comando seguente:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

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

>[!NOTE]
>Se *CircuitProvisioningState* non è abilitato, contattare il [supporto tecnico Microsoft][Support]. Se *ServiceProviderProvisioningState* è senza provisioning, contattare il provider di servizi.
>
>

###<a name="verification-via-powershell-classic"></a>Verifica tramite PowerShell (versione classica)
Per elencare tutti i circuiti ExpressRoute in una sottoscrizione, usare il comando seguente:

    Get-AzureDedicatedCircuit

Per selezionare uno specifico circuito ExpressRoute, usare il comando seguente:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Una risposta di esempio:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Per verificare se un circuito ExpressRoute è operativo, prestare particolare attenzione ai campi seguenti: ServiceProviderProvisioningState: Provisioned Status: Enabled

>[!NOTE]
>Se *Status* non è abilitato, contattare il [supporto tecnico Microsoft][Support]. Se *ServiceProviderProvisioningState* è senza provisioning, contattare il provider di servizi.
>
>

##<a name="validate-peering-configuration"></a>Convalidare la configurazione del peering
Dopo che il provider di servizi ha completato il provisioning del circuito ExpressRoute, è possibile creare una configurazione di routing sul circuito ExpressRoute tra MSEE-PR (4) e MSEE (5). Ogni circuito ExpressRoute può avere uno, due o tre contesti di routing abilitati: peering privato di Azure, ovvero il traffico verso reti virtuali private in Azure, peering pubblico di Azure, ovvero il traffico verso indirizzi IP pubblici in Azure e peering Microsoft, ovvero il traffico verso Office 365 e Dynamics 365. Per altre informazioni su come creare e modificare la configurazione di routing, vedere l'articolo [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering].

###<a name="verification-via-the-azure-portal"></a>Verifica tramite il portale di Azure

>[!NOTE]
>Se il livello 3 viene fornito dal provider di servizi e i peering sono vuoti nel portale, aggiornare la configurazione del circuito utilizzando il pulsante di aggiornamento del portale. Questa operazione applicherà la configurazione di routing corretta nel circuito. 
>
>

Nel portale di Azure lo stato di un circuito ExpressRoute può essere controllato selezionando ![2][2] nel menu sulla barra laterale sinistra e quindi selezionando il circuito ExpressRoute. La selezione di un circuito ExpressRoute elencato in "All resources" (Tutte le risorse) determina l'apertura del pannello del circuito ExpressRoute. Nella sezione ![3][3] del pannello saranno elencate le informazioni di base di ExpressRoute come illustrato nella schermata seguente:

![5][5]

Nell'esempio precedente, come indicato, il contesto di routing di peering privato di Azure è abilitato, mentre i contesti di routing di peering pubblico e Microsoft non sono abilitati. Per un contesto di peering abilitato correttamente vengono anche elencate le subnet punto a punto primarie e secondarie (per il protocollo BGP). Le /30 subnet vengono usate per l'indirizzo IP dell'interfaccia degli MSEE e PE-MSEE. 

>[!NOTE]
>Se non è abilitato alcun peering, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione su PE-MSEE. In caso contrario, per modificare la configurazione sui router MSEE, fare riferimento a [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>Verifica tramite PowerShell
Per ottenere i dettagli di configurazione del peering privato di Azure, usare i comandi seguenti:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt

Una risposta di esempio, per un peering privato configurato correttamente:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 In un contesto di peering abilitato correttamente vengono elencati i prefissi di indirizzi primari e secondari. Le /30 subnet vengono usate per l'indirizzo IP dell'interfaccia degli MSEE e PE-MSEE.

Per ottenere i dettagli di configurazione del peering pubblico di Azure, usare i comandi seguenti:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

Per ottenere i dettagli di configurazione del peering Microsoft, usare i comandi seguenti:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

Se non è configurato alcun peering, viene visualizzato un messaggio di errore. Una risposta di esempio, quando il peering indicato (peering pubblico di Azure in questo esempio) non è configurato all'interno del circuito:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Se non è abilitato alcun peering, verificare se le subnet primarie e secondarie assegnate corrispondono alla configurazione sul PE-MSEE collegato. Controllare anche che siano usati i valori corretti *VlanId*, *AzureASN* e *PeerASN* sugli MSEE e che tali valori corrispondano a quelli usati sul PE-MSEE collegato. Se si sceglie l'hash MD5, la chiave condivisa deve essere identica nella coppia MSEE e PE-MSEE. Per modificare la configurazione sui router MSEE, fare riferimento a [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verifica tramite PowerShell (versione classica)
Per ottenere i dettagli di configurazione del peering privato di Azure, usare il comando seguente:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Una risposta di esempio, per un peering privato configurato correttamente:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

In un contesto di peering abilitato correttamente vengono elencate le subnet peer primarie e secondarie. Le /30 subnet vengono usate per l'indirizzo IP dell'interfaccia degli MSEE e PE-MSEE.

Per ottenere i dettagli di configurazione del peering pubblico di Azure, usare i comandi seguenti:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Per ottenere i dettagli di configurazione del peering Microsoft, usare i comandi seguenti:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Se il provider di servizi ha impostato peering di livello 3, l'impostazione dei peering di ExpressRoute tramite il portale o PowerShell sovrascrive le impostazioni del provider di servizi. La riconfigurazione delle impostazioni di peering lato provider richiede il supporto del provider di servizi. Modificare i peering di ExpressRoute solo se si è certi che il provider di servizi offre esclusivamente servizi di livello 2.
>
>

<p/>
>[!NOTE]
>Se non è abilitato alcun peering, verificare se le subnet peer primarie e secondarie assegnate corrispondono alla configurazione sul PE-MSEE collegato. Controllare anche che siano usati i valori corretti *VlanId*, *AzureASN* e *PeerASN* sugli MSEE e che tali valori corrispondano a quelli usati sul PE-MSEE collegato. Per modificare la configurazione sui router MSEE, fare riferimento a [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Convalidare ARP tra Microsoft e il provider di servizi
In questa sezione vengono usati i comandi di PowerShell (versione classica). Se si usano i comandi di PowerShell basati su Azure Resource Manager, assicurarsi di avere l'accesso di amministratore/coamministratore alla sottoscrizione. Per la risoluzione dei problemi tramite i comandi di Azure Resource Manager, vedere il documento [Recupero di tabelle ARP nel modello di distribuzione Resource Manager][ARP].

>[!NOTE]
>Per ottenere ARP, è possibile usare sia il portale di Azure sia i comandi di PowerShell basati su Azure Resource Manager. In caso di errori con i comandi di PowerShell basati su Azure Resource Manager, i comandi classici di PowerShell dovrebbero essere operativi in quanto tali comandi funzionano anche con circuiti ExpressRoute in Azure Resource Manager.
>
>

Per ottenere la tabella ARP dal router MSEE primario per il peering privato, usare il comando seguente:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Un esempio di risposta per il comando, nello scenario di esito positivo:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Allo stesso modo è possibile controllare la tabella ARP dal MSEE nel percorso *Primary*/*Secondary* per peering *Private*/*Public*/*Microsoft*.

L'esempio seguente mostra la risposta del comando per un peering inesistente.

    ARP Info:
       
>[!NOTE]
>Se la tabella ARP non dispone di indirizzi IP delle interfacce associate agli indirizzi MAC, esaminare le informazioni seguenti:
>1. Se il primo indirizzo IP delle /30 subnet assegnate per il collegamento tra il MSEE-PR e il MSEE viene usato nell'interfaccia di MSEE-PR. Azure usa sempre il secondo indirizzo IP per MSEE.
>2. Verificare se i tag VLAN del cliente (C-Tag) e del servizio (S-Tag) corrispondono nella coppia MSEE-PR e MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Convalidare BGP e route sul MSEE
In questa sezione vengono usati i comandi di PowerShell (versione classica). Se si usano i comandi di PowerShell basati su Azure Resource Manager, assicurarsi di avere l'accesso di amministratore/coamministratore alla sottoscrizione.

>[!NOTE]
>Per ottenere informazioni BGP, è possibile usare sia il portale di Azure sia i comandi di PowerShell basati su Azure Resource Manager. In caso di errori con i comandi di PowerShell basati su Azure Resource Manager, i comandi classici di PowerShell dovrebbero essere operativi in quanto tali comandi funzionano anche con circuiti ExpressRoute in Azure Resource Manager.
>
>

Per ottenere il riepilogo della tabella di routing (BGP adiacente) per un particolare contesto di routing, usare il comando seguente:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Una risposta di esempio:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Come illustrato nell'esempio precedente, il comando è utile per determinare da quanto tempo è stato stabilito il contesto di routing. Indica inoltre il numero di prefissi di route annunciati dal router di peering.

>[!NOTE]
>Se lo stato è attivo o inattivo, verificare se le subnet peer primarie e secondarie assegnate corrispondono alla configurazione sul PE-MSEE collegato. Controllare anche che siano usati i valori corretti *VlanId*, *AzureASN* e *PeerASN* sugli MSEE e che tali valori corrispondano a quelli usati sul PE-MSEE collegato. Se si sceglie l'hash MD5, la chiave condivisa deve essere identica nella coppia MSEE e PE-MSEE. Per modificare la configurazione sui router MSEE, fare riferimento a [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Se alcune destinazioni non sono raggiungibili tramite un particolare peering, controllare la tabella di route dei MSEE appartenente al contesto di peering specifico. Se un prefisso corrispondente, ad esempio un IP su cui è stato eseguito il NAT, questo è presente nella tabella di routing, controllare che ci siano firewalls/NSG/ACLs nel percorso e che questi consentano il traffico.
>
>

Per ottenere la tabella di routing completa da MSEE sul percorso *Primary* per il contesto di routing *Private* specifico, usare il comando seguente:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Un risultato positivo di esempio per il comando:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Allo stesso modo è possibile controllare la tabella di routing dal MSEE nel percorso *Primary*/*Secondary*, per un contesto di peering *Private*/*Public*/*Microsoft*.

L'esempio seguente mostra la risposta del comando per un peering inesistente:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>Controllare le statistiche sul traffico
Per ottenere le statistiche sul traffico del percorso primario e secondario combinato, byte in entrata e in uscita, di un contesto di peering, usare il comando seguente:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Output di esempio del comando:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Output di esempio del comando per un peering inesistente:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

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
[OldPortal]: https://manage.windowsazure.com
[ARP]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






