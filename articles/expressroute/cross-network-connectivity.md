---
title: Connettività tra reti di Azure
description: Questa pagina descrive uno scenario di applicazione per la connettività tra reti e la soluzione basata sulle funzionalità di rete di Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: e503dc2b4ae8773ebfedc7a9b73bc5ea93dd9d5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076740"
---
# <a name="cross-network-connectivity"></a>Connettività tra reti

Fabrikam Inc. è ampiamente presente negli Stati Uniti orientali, dove ha una distribuzione di Azure. Fabrikam stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute. Analogamente, Contoso Ltd. ha una presenza e una distribuzione di Azure negli Stati Uniti occidentali. Contoso stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute.  

Fabrikam Inc. acquisisce Contoso Ltd. Dopo la fusione, Fabrikam desidera interconnettere le reti. Lo scenario viene illustrato nell'immagine seguente:

 [![1]][1]

Le frecce tratteggiate al centro della figura sopra indicata indicano le interconnessioni di rete desiderate. In particolare, sono disponibili tre tipi di connessioni incrociate: 1) Fabrikam e contoso reti virtuali Cross Connect, 2) Cross-Side-Regional locale e reti virtuali Cross Connection (ovvero la connessione della rete locale Fabrikam a Contoso VNet e la connessione di contoso da rete locale a Fabrikam VNet) e 3) Fabrikam e Contoso in locale rete Cross Connect. 

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., prima della fusione.

[![2]][2]

La tabella seguente mostra le route valide di una macchina virtuale nella sottoscrizione Contoso, prima della fusione. Per la tabella, la macchina virtuale in VNet è in grado di riconoscere lo spazio degli indirizzi VNet e la rete locale Contoso, oltre a quelle predefinite. 

[![4]][4]

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc. prima della fusione.

[![3]][3]

La tabella seguente mostra le route valide di una macchina virtuale nella sottoscrizione Fabrikam, prima dell'Unione. Per la tabella, la macchina virtuale in VNet è in grado di riconoscere lo spazio degli indirizzi VNet e la rete locale Fabrikam, oltre a quelle predefinite.

[![5]][5]

Questo articolo illustra in dettaglio come ottenere le connessioni incrociate desiderate usando le funzionalità di rete di Azure seguenti:

* [Peering di rete virtuale][Virtual network peering] 
* [Connessione ExpressRoute rete virtuale][connection]
* [Copertura globale][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross Connecting reti virtuali

Il peering di rete virtuale (peering VNet) fornisce le prestazioni di rete più ottimali e migliori quando si connettono due reti virtuali. Il peering VNet supporta il peering di due reti virtuali sia all'interno della stessa area di Azure (comunemente chiamato peering VNet) che in due diverse aree di Azure, comunemente denominate peering VNet globali. 

Si configurerà il peering VNet globale tra reti virtuali nelle sottoscrizioni di Azure Contoso e fabrikam. Per informazioni su come creare il peering di rete virtuale tra due reti virtuali, vedere l'articolo [creare un peering di rete virtuale][Configure VNet peering] .

Nell'immagine seguente viene illustrata l'architettura di rete dopo la configurazione del peering VNet globale.

[![6]][6]

La tabella seguente mostra le route note alla VM di sottoscrizione Contoso. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

[![7]][7]

Nella tabella seguente vengono illustrate le route note alla VM di sottoscrizione fabrikam. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

[![8]][8]

Il peering di VNet collega direttamente due reti virtuali (vedere non sono presenti hop successivi per la voce *VNetGlobalPeering* nelle due tabelle precedenti)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross Connecting reti virtuali to the locale Networks

È possibile connettere un circuito ExpressRoute a più reti virtuali. Vedere [sottoscrizione e limiti del servizio][Subscription limits] per il numero massimo di reti virtuali che è possibile connettere a un circuito ExpressRoute. 

Connettersi al circuito ExpressRoute di Fabrikam alla sottoscrizione Contoso VNet e allo stesso modo di Contoso ExpressRoute Circuit alla sottoscrizione Fabrikam VNet per abilitare la connettività incrociata tra le reti virtuali e le reti locali. Per connettere una rete virtuale a un circuito ExpressRoute in un'altra sottoscrizione, è necessario creare e usare un'autorizzazione.  Vedere l'articolo: [connettere una rete virtuale a un circuito ExpressRoute][Connect-ER-VNet].

Nell'immagine seguente viene illustrata l'architettura di rete dopo la configurazione di ExpressRoute Cross Connectivity per le reti virtuali.

[![9]][9]

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo la connessione tra reti virtuali e le reti locali tramite ExpressRoute. Verificare che la tabella di route includa Route appartenenti a entrambe le reti virtuali.

[![10]][10]

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc., dopo la connessione tra reti virtuali e le reti locali tramite ExpressRoute. Verificare che la tabella di route includa Route appartenenti a entrambe le reti virtuali.

[![11]][11]

La tabella seguente mostra le route note alla VM di sottoscrizione Contoso. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La VM Visualizza le route per entrambe le reti locali.

[![12]][12]

Nella tabella seguente vengono illustrate le route note alla VM di sottoscrizione fabrikam. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La VM Visualizza le route per entrambe le reti locali.

[![13]][13]

>[!NOTE]
>Nelle sottoscrizioni Fabrikam e/o Contoso è anche possibile che spoke reti virtuali per il rispettivo Hub VNet (la progettazione di hub e spoke non è illustrata nei diagrammi dell'architettura in questo articolo). Le connessioni incrociate tra i gateway VNet dell'hub a ExpressRoute consentiranno anche la comunicazione tra gli hub est e ovest e i spoke.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross Connecting di reti locali

ExpressRoute Copertura globale fornisce la connettività tra le reti locali connesse a circuiti ExpressRoute diversi. Viene ora configurata Copertura globale tra i circuiti ExpressRoute di Contoso e fabrikam. Poiché i circuiti ExpressRoute si trovano in sottoscrizioni diverse, è necessario creare e usare un'autorizzazione. Per istruzioni dettagliate, vedere l'articolo [Configure ExpressRoute copertura globale][Configure Global Reach] .

Nell'immagine seguente viene illustrata l'architettura di rete dopo aver configurato Copertura globale.

[![14]][14]

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo aver configurato Copertura globale. Verificare che la tabella di route includa Route appartenenti a entrambe le reti locali. 

[![15]][15]

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc. dopo aver configurato Copertura globale. Verificare che la tabella di route includa Route appartenenti a entrambe le reti locali.

[![16]][16]

## <a name="next-steps"></a>Passaggi successivi

Per altre domande su VNet e VNet-peering, vedere domande [frequenti sulla rete virtuale][VNet-FAQ]. Per altre domande su ExpressRoute e sulla connettività di rete virtuale, vedere domande [frequenti su ExpressRoute][ER-FAQ] .

Copertura globale viene implementato in un paese/area geografica in base al paese. Per verificare se Copertura globale è disponibile nei paesi/aree geografiche desiderate, vedere [ExpressRoute copertura globale][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scenario di applicazione"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabella di route contoso ExpressRoute prima della fusione"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "tabella di route ExpressRoute di Fabrikam prima della fusione"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Route VM contoso prima della fusione"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Route VM Fabrikam prima della fusione"
[6]: ./media/cross-network-connectivity/vnet-peering.png "l'architettura dopo VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Route VM Contoso dopo il peering VNet"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Route VM Fabrikam dopo il peering VNet"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "architettura dopo delle expressroute Cross Connection"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "tabella di route contoso ExpressRoute dopo la connessione tra EXR e reti virtuali"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "tabella di route ExpressRoute Fabrikam dopo la connessione incrociata EXR e reti virtuali"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Route VM di Contoso dopo la connessione incrociata EXR e reti virtuali"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Route VM Fabrikam dopo la connessione incrociata EXR e reti virtuali"
[14]: ./media/cross-network-connectivity/globalreach.png "l'architettura dopo la configurazione di copertura globale"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute tabella di route dopo copertura globale"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "tabella di route ExpressRoute Fabrikam dopo copertura globale"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq