---
title: Scenario di applicazione di Copertura globale di Azure ExpressRoute | Microsoft Docs
description: Questa pagina offre uno scenario di applicazione per Copertura globale.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333089"
---
# <a name="expressroute-global-reach-application-scenario"></a>Scenario di applicazione di Copertura globale di ExpressRoute

Per altre informazioni su Copertura globale di ExpressRoute, vedere [Copertura globale di ExpressRoute][Global Reach]. Questo articolo descrive uno scenario di applicazione e illustra come confrontare la soluzione Copertura globale di ExpressRoute con alcune altre soluzioni, configurare Copertura globale per lo scenario di esempio e verificare le connessioni. 

##<a name="application-scenario"></a>Scenario di applicazione

Fabrikam Inc. è ampiamente presente negli Stati Uniti orientali, dove ha una distribuzione di Azure. Fabrikam stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute. Contoso Ltd. è presente negli Stati Uniti occidentali, dove ha una distribuzione di Azure. Contoso stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute.  

Fabrikam Inc. acquisisce Contoso Ltd. Dopo la fusione, Fabrikam vuole che le reti siano interconnesse. Lo scenario viene illustrato nell'immagine seguente:

 [![1]][1]

Le frecce tratteggiate al centro della figura illustrata sopra indicano le interconnessioni di rete necessarie. La tabella seguente illustra la tabella di route del peer privato primario di ExpressRoute di Contoso Ltd. prima della fusione.

[![2]][2]

La tabella seguente illustra la tabella di route del peer privato primario di ExpressRoute di Fabrikam Inc. prima della fusione.

[![3]][3]

## <a name="traditional-solutions"></a>Soluzioni tradizionali

### <a name="option-1-interconnect-on-premises-networks"></a>Opzione 1: Interconnettere le reti locali

Questa opzione viene illustrata nell'immagine seguente. Come illustrato, è possibile interconnettere le due reti locali usando una VPN da sito a sito o altre opzioni di connessione a banda larga e gestire tutto il routing tra le due entità. 

[![4]][4]

Questa opzione presenta gli svantaggi seguenti:

- Si forza la comunicazione di Azure tra aree per la distribuzione tramite una route non ottimale.
- Si perde il vantaggio della disponibilità elevata della rete backbone Microsoft per la comunicazione tra aree.
- Si assume la piena responsabilità del routing per la comunicazione tra aree.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Opzione 2: Cross Connection ExpressRoute e interconnessione delle reti locali

Questa opzione viene illustrata nell'immagine seguente.

[![5]][5]

Come illustrato nella figura precedente, è anche possibile stabilire la connettività tra i circuiti ExpressRoute e le reti virtuali tra aree. La connettività tra aree tra la rete virtuale e i circuiti ExpressRoute consentirà le comunicazioni seguenti:

- Le reti virtuali Stati Uniti occidentali/Stati Uniti orientali per comunicare rispettivamente con le reti locali Fabrikam/Contoso.
- La rete virtuale Stati Uniti occidentali per comunicare con la rete virtuale Stati Uniti orientali.

L'interconnessione tra le due reti locali è ancora necessaria perché possano comunicare tra di esse.

Questa opzione consente la comunicazione tra aree di Azure in modo che la distribuzione privata possa raggiungere il backbone Microsoft e la comunicazione tra le reti locali possa essere avere luogo tramite la rete esterna. Tuttavia, il principale svantaggio della soluzione è che è necessario stabilire più connessioni tra aree, che complicano la manutenzione e la risoluzione dei problemi. Inoltre, l'opzione non consente di sfruttare il backbone globale Microsoft a disponibilità elevata per le comunicazioni tra le due reti locali.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Opzione 3: Peering reti virtuali e interconnessione delle reti locali

Questa opzione viene illustrata nell'immagine seguente. L'opzione usa il peering tra reti virtuali per la comunicazione tra aree delle reti virtuali. L'opzione, come illustrato, è incompleta perché non gestisce la comunicazione tra l'ambiente locale e la rete virtuale tra aree (indicata dalle due frecce tratteggiate al centro). Usare la connessione da ambiente locale ad ambiente locale (come nell'opzione 1) o una Cross Connection ExpressRoute (come nell'opzione 2) per la comunicazione da un ambiente in più aree a quello locale.

[![6]][6]

Questa opzione offre un routing ottimale per la comunicazione tra reti virtuali di aree diverse. Non è tuttavia adeguata se Fabrikam o Contoso ha una distribuzione di Azure più complessa, ad esempio un modello di rete virtuale hub-spoke. Inoltre, come le due opzioni precedenti, questa opzione non consente di sfruttare il backbone globale Microsoft a disponibilità elevata per le comunicazioni tra le due reti locali.

## <a name="global-reach"></a>Copertura globale

Copertura globale di ExpressRoute collega il peer privato dei circuiti ExpressRoute, come illustrato nella figura seguente:

[![7]][7]

La configurazione di Copertura globale tra i due circuiti ExpressRoute consente la comunicazione privata tra le due reti locali e la distribuzione di Azure in due aree. Copertura globale soddisfa tutte le comunicazioni necessarie (indicate dalla linea tratteggiata nella prima figura di questo articolo) tramite la rete backbone Microsoft.

### <a name="configure-global-reach"></a>Configurare Copertura globale

Per informazioni su come configurare Copertura globale di ExpressRoute, vedere [Configurare Copertura globale][Configure Global Reach]. 

Poiché Fabrikam Inc. e Contoso Ltd. hanno caricato Azure come società separate, i circuiti ExpressRoute delle due società sono in due diverse sottoscrizioni di Azure. Per creare la copertura globale in tutte le sottoscrizioni, è necessario creare un'autorizzazione nel circuito ExpressRoute appartenente a Contoso Ltd. e passarla al circuito ExpressRoute di Fabrikam Inc.


Per creare un'autorizzazione per il circuito ExpressRoute di Contoso, accedere prima all'account Azure di Contoso e selezionare la sottoscrizione appropriata (se sono presenti più sottoscrizioni). I comandi di PowerShell per questi passaggi sono:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
I passaggi per la creazione di un'autorizzazione per il circuito ExpressRoute sono elencati di seguito:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

L'output "Set-AzureRmExpressRouteCircuit" elencherà ExpressRouteCircuit. Si notino l'ID peer privato e la chiave di autorizzazione che saranno elencati nella parte finale dell'elenco. Di seguito è riportato un frammento di output di PowerShell di esempio:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Con l'ID peering e la chiave di autorizzazione, è possibile creare la copertura globale nel circuito ExpressRoute di Fabrikam. Accedere all'account Azure di Fabrikam. Se è presente più di una sottoscrizione, selezionare quella appropriata.

Copertura globale crea un set ridondante di connessioni da punto a punto tra le due coppie MSEE. Per le due connessioni da punto a punto, è necessario specificare un prefisso di indirizzo /29. Per l'esecuzione dell'esempio verrà usato 192.168.11.64/29. Usare i comandi seguenti per creare la connessione di Copertura globale:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Una volta eseguiti i comandi precedenti, saranno sufficienti alcuni minuti per creare le connessioni di Copertura globale ridondanti.

### <a name="verify-global-reach"></a>Verificare Copertura globale

La tabella seguente illustra la tabella di route del peer privato primario di ExpressRoute di Contoso Ltd. dopo la configurazione di Copertura globale.

[![8]][8]

La tabella seguente illustra la tabella di route del peer privato primario di ExpressRoute di Fabrikam Inc. dopo la configurazione di Copertura globale.

[![9]][9]

Nelle tabelle precedenti sono elencati tutti i prefissi "NETWORK" di destinazione previsti e l'elemento "NEXT HOP" appropriato.

Sopra viene visualizzato il pannello "Ottieni tabella di route" accessibile nel portale di Azure in "Peer privato" di un circuito ExpressRoute. È anche possibile elencare una tabella di route di ExpressRoute usando il comando di PowerShell seguente:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Per visualizzare la tabella di route della coppia MSEE secondaria, sostituire 'primary' con la parola chiave 'secondary' nel comando precedente.

Verrà ora verificata anche la connettività del piano dati effettuando il ping di una destinazione diversa da reti diverse. I tre ping seguenti verificano la connettività del piano dati alla rete locale di Contoso, alla rete virtuale di Azure di Contoso e alla rete virtuale di Azure di Fabrikam dalla rete locale di Fabrikam.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


I due ping seguenti verificano la connettività del piano dati alla rete virtuale di Azure di Contoso e alla rete virtuale di Azure di Fabrikam dalla rete locale di Contoso.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Il ping seguente verifica la connettività del piano dati alla rete virtuale di Azure di Contoso dalla rete virtuale di Azure di Fabrikam.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Ottimizzazione per il traffico sensibile alla latenza

Copertura globale instrada il traffico tramite dispositivi Microsoft Edge. Per lo specifico scenario considerato in questo articolo, è possibile ottenere il routing ottimale tra le due reti virtuali abilitando il peering tra di esse. È analogamente possibile ottenere il routing ottimale tra le due reti locali tramite un provider di servizi, che può fornire una connessione WAN più diretta tra i siti. In questi scenari è possibile usare il routing di Copertura globale come opzione di failback per tali connessioni. 

## <a name="next-steps"></a>Passaggi successivi

Copertura globale viene implementato in ogni singolo paese. Per verificare se Copertura globale è disponibile nei paesi desiderati, vedere [Copertura globale di ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "Scenario di applicazione"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "Tabella di route di ExpressRoute di Contoso prima della fusione"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "Tabella di route di ExpressRoute di Fabrikam prima della fusione"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "Interconnessione delle reti locali"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Cross Connection ExpressRoute"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "Peering reti virtuali"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Copertura globale"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Tabella di route di ExpressRoute di Contoso con Copertura globale"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Tabella di route di ExpressRoute di Fabrikam con Copertura globale"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





