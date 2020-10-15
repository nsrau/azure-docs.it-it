---
title: Connettività tra reti di Azure
description: Questa pagina descrive uno scenario di applicazione per la connettività tra reti e la soluzione basata sulle funzionalità di rete di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: f13e3c03c0cfd747c7819d95a5fb98560db861c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398070"
---
# <a name="cross-network-connectivity"></a>Connettività tra reti

Fabrikam Inc. è ampiamente presente negli Stati Uniti orientali, dove ha una distribuzione di Azure. Fabrikam stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute. Analogamente, Contoso Ltd. ha una presenza e una distribuzione di Azure negli Stati Uniti occidentali. Contoso stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute.  

Fabrikam Inc. acquisisce Contoso Ltd. Dopo la fusione, Fabrikam desidera interconnettere le reti. Lo scenario viene illustrato nell'immagine seguente:

![Scenario dell'applicazione](./media/cross-network-connectivity/premergerscenario.png)

Le frecce tratteggiate al centro della figura sopra indicata indicano le interconnessioni di rete desiderate. In particolare, sono disponibili tre tipi di connessioni incrociate: 1) Fabrikam e contoso reti virtuali Cross Connect, 2) Cross-Side-Regional locale e reti virtuali Cross Connection (ovvero la connessione della rete locale Fabrikam a Contoso VNet e la connessione della rete locale Contoso a Fabrikam VNet) e 3) Fabrikam e la rete locale di Contoso Cross Connect. 

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., prima della fusione.

![Tabella di route contoso ExpressRoute prima della fusione](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

La tabella seguente mostra le route valide di una macchina virtuale nella sottoscrizione Contoso, prima della fusione. Per la tabella, la macchina virtuale in VNet è in grado di riconoscere lo spazio degli indirizzi VNet e la rete locale Contoso, oltre a quelle predefinite.

![Route VM contoso prima della fusione](./media/cross-network-connectivity/contosovm-routes-premerger.png)

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc. prima della fusione.

![Tabella di route ExpressRoute Fabrikam prima della fusione](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

La tabella seguente mostra le route valide di una macchina virtuale nella sottoscrizione Fabrikam, prima dell'Unione. Per la tabella, la macchina virtuale in VNet è in grado di riconoscere lo spazio degli indirizzi VNet e la rete locale Fabrikam, oltre a quelle predefinite.

![Route VM Fabrikam prima della fusione](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Questo articolo illustra in dettaglio come ottenere le connessioni incrociate desiderate usando le funzionalità di rete di Azure seguenti:

* [Peering di rete virtuale][Virtual network peering] 
* [Connessione ExpressRoute rete virtuale][connection]
* [Copertura globale][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross Connecting reti virtuali

Il peering di rete virtuale (peering VNet) fornisce le prestazioni di rete più ottimali e migliori quando si connettono due reti virtuali. Il peering VNet supporta il peering di due reti virtuali sia all'interno della stessa area di Azure (comunemente chiamato peering VNet) che in due diverse aree di Azure, comunemente denominate peering VNet globali. 

Si configurerà il peering VNet globale tra reti virtuali nelle sottoscrizioni di Azure Contoso e fabrikam. Per informazioni su come creare il peering di rete virtuale tra due reti virtuali, vedere l'articolo [creare un peering di rete virtuale][Configure VNet peering] .

Nell'immagine seguente viene illustrata l'architettura di rete dopo la configurazione del peering VNet globale.

![Architettura dopo VNet-peering](./media/cross-network-connectivity/vnet-peering.png )

La tabella seguente mostra le route note alla VM di sottoscrizione Contoso. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

![Route VM Contoso dopo il peering VNet](./media/cross-network-connectivity/contosovm-routes-peering.png)

Nella tabella seguente vengono illustrate le route note alla VM di sottoscrizione fabrikam. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

![Route VM Fabrikam dopo il peering VNet](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

Il peering di VNet collega direttamente due reti virtuali (vedere non sono presenti hop successivi per la voce *VNetGlobalPeering* nelle due tabelle precedenti)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross Connecting reti virtuali to the locale Networks

È possibile connettere un circuito ExpressRoute a più reti virtuali. Vedere [sottoscrizione e limiti del servizio][Subscription limits] per il numero massimo di reti virtuali che è possibile connettere a un circuito ExpressRoute. 

Connettersi al circuito ExpressRoute di Fabrikam alla sottoscrizione Contoso VNet e allo stesso modo di Contoso ExpressRoute Circuit alla sottoscrizione Fabrikam VNet per abilitare la connettività incrociata tra le reti virtuali e le reti locali. Per connettere una rete virtuale a un circuito ExpressRoute in un'altra sottoscrizione, è necessario creare e usare un'autorizzazione.  Vedere l'articolo: [connettere una rete virtuale a un circuito ExpressRoute][Connect-ER-VNet].

Nell'immagine seguente viene illustrata l'architettura di rete dopo la configurazione di ExpressRoute Cross Connectivity per le reti virtuali.

![Architettura dopo delle expressroute Cross Connection](./media/cross-network-connectivity/exr-x-connect.png)

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo la connessione tra reti virtuali e le reti locali tramite ExpressRoute. Verificare che la tabella di route includa Route appartenenti a entrambe le reti virtuali.

![Tabella di route contoso ExpressRoute dopo la connessione tra ExR e reti virtuali](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc., dopo la connessione tra reti virtuali e le reti locali tramite ExpressRoute. Verificare che la tabella di route includa Route appartenenti a entrambe le reti virtuali.

![Tabella di route ExpressRoute Fabrikam dopo la connessione incrociata ExR e reti virtuali](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

La tabella seguente mostra le route note alla VM di sottoscrizione Contoso. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La VM Visualizza le route per entrambe le reti locali.

![Route VM Contoso dopo la connessione incrociata ExR e reti virtuali](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

Nella tabella seguente vengono illustrate le route note alla VM di sottoscrizione fabrikam. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La VM Visualizza le route per entrambe le reti locali.

![Route VM Fabrikam dopo la connessione incrociata ExR e reti virtuali](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Nelle sottoscrizioni Fabrikam e/o Contoso è anche possibile che spoke reti virtuali per il rispettivo Hub VNet (la progettazione di hub e spoke non è illustrata nei diagrammi dell'architettura in questo articolo). Le connessioni incrociate tra i gateway VNet dell'hub a ExpressRoute consentiranno anche la comunicazione tra gli hub est e ovest e i spoke.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross Connecting di reti locali

ExpressRoute Copertura globale fornisce la connettività tra le reti locali connesse a circuiti ExpressRoute diversi. Viene ora configurata Copertura globale tra i circuiti ExpressRoute di Contoso e fabrikam. Poiché i circuiti ExpressRoute si trovano in sottoscrizioni diverse, è necessario creare e usare un'autorizzazione. Per istruzioni dettagliate, vedere l'articolo [Configure ExpressRoute copertura globale][Configure Global Reach] .

Nell'immagine seguente viene illustrata l'architettura di rete dopo aver configurato Copertura globale.

![Architettura dopo la configurazione di Copertura globale](./media/cross-network-connectivity/globalreach.png)

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo aver configurato Copertura globale. Verificare che la tabella di route includa Route appartenenti a entrambe le reti locali. 

![Tabella di route contoso ExpressRoute dopo Copertura globale](./media/cross-network-connectivity/contosoexr-rt-gr.png)

La tabella seguente illustra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc. dopo aver configurato Copertura globale. Verificare che la tabella di route includa Route appartenenti a entrambe le reti locali.

![Tabella di route ExpressRoute Fabrikam dopo Copertura globale]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Passaggi successivi

Per altre domande su VNet e VNet-peering, vedere domande [frequenti sulla rete virtuale][VNet-FAQ]. Per altre domande su ExpressRoute e sulla connettività di rete virtuale, vedere domande [frequenti su ExpressRoute][ER-FAQ] .

Copertura globale viene implementato in un paese/area geografica in base al paese. Per verificare se Copertura globale è disponibile nei paesi/aree geografiche desiderate, vedere [ExpressRoute copertura globale][Global Reach].

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq