---
title: Connettività tra più reti di Azure | Microsoft Docs
description: Questa pagina descrive uno scenario di applicazioni per la connettività di rete e soluzione basata su funzionalità di rete di Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368750"
---
# <a name="cross-network-connectivity"></a>Connettività tra reti

Fabrikam Inc. è ampiamente presente negli Stati Uniti orientali, dove ha una distribuzione di Azure. Fabrikam stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute. Analogamente, Contoso Ltd. ha una presenza e la distribuzione di Azure negli Stati Uniti occidentali. Contoso stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute.  

Fabrikam Inc. acquisisce Contoso Ltd. Dopo la fusione, Fabrikam vuole che le reti siano interconnesse. Lo scenario viene illustrato nell'immagine seguente:

 [![1]][1]

Le frecce tratteggiate a metà nella figura precedente indicano le interconnessioni rete desiderata. In particolare, esistono tre tipi di connessioni incrociate desiderate: Fabrikam e Contoso VNets cross connect 1), 2) incrociato a livello di area locale e le reti virtuali cross si connette (, la connessione di rete locale Fabrikam a Contoso VNet e che la connessione di rete locale Contoso a Fabrikam VNet) e 3) Fabrikam e Contoso rete locale tra la connessione. 

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., prima dell'unione.

[![2]][2]

La tabella seguente illustra le route valide di una macchina virtuale nella sottoscrizione di Contoso, prima dell'unione. Per ogni tabella, la macchina virtuale nella rete virtuale è a conoscenza di spazio di indirizzi della rete virtuale e la rete locale Contoso, oltre a quelli predefiniti. 

[![4]][4]

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc., prima dell'unione.

[![3]][3]

La tabella seguente illustra le route valide di una macchina virtuale nella sottoscrizione di Fabrikam, prima dell'unione. Per ogni tabella, la macchina virtuale nella rete virtuale è a conoscenza di spazio di indirizzi della rete virtuale e la rete locale di Fabrikam, oltre a quelli predefiniti.

[![5]][5]

In questo articolo, verrà ora analizzato dettagliata e illustrano come ottenere le connessioni incrociate desiderate usando le funzionalità di rete di Azure seguenti:

* [Peering di rete virtuale][Virtual network peering] 
* [Connessione ExpressRoute alla rete virtuale][connection]
* [Copertura globale][Global Reach] 

## <a name="cross-connecting-vnets"></a>Tra la connessione di reti virtuali

Peering di rete virtuale (peering reti virtuali) offre come ottimale e le migliori prestazioni di rete quando ci si connette due reti virtuali. Peering reti virtuali supporta peering due reti virtuali nella stessa area di Azure (più comunemente il peering reti virtuali) e in due diverse aree di Azure (comunemente denominato peering reti virtuali globale). 

È possibile configurare reti virtuali globale peering tra reti virtuali nelle sottoscrizioni di Azure di Fabrikam e Contoso. Per informazioni su come creare la rete virtuale di peering tra due reti virtuali, vedere [creare un peering reti virtuali] [ Configure VNet peering] articolo.

L'immagine seguente illustra l'architettura di rete dopo aver configurato il peering reti virtuali globale.

[![6]][6]

La tabella seguente illustra le route noti per la sottoscrizione di Contoso della macchina virtuale. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato del collegamento incrociato le reti virtuali.

[![7]][7]

La tabella seguente illustra le route noti per la sottoscrizione di Fabrikam della macchina virtuale. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato del collegamento incrociato le reti virtuali.

[![8]][8]

Direttamente il peering reti virtuali collega due reti virtuali (vedere non esistono alcuna hop successivo per *VNetGlobalPeering* voce nelle due tabelle precedenti)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Tra la connessione di reti virtuali alle reti locali

È possibile connettere un circuito ExpressRoute a più reti virtuali. Visualizzare [sottoscrizione e limiti dei servizi] [ Subscription limits] per il numero massimo di reti virtuali che possono essere connessi a un circuito ExpressRoute. 

È possibile connettersi circuito ExpressRoute di Fabrikam per la sottoscrizione della rete virtuale di Contoso e allo stesso modo circuito ExpressRoute di Contoso alla sottoscrizione di Fabrikam della rete virtuale per abilitare la connettività cross-tra reti virtuali e le reti locali. Per connettere una rete virtuale a un circuito ExpressRoute in un'altra sottoscrizione, è necessario creare e usare un'autorizzazione.  Vedere l'articolo: [Connettere una rete virtuale a un circuito ExpressRoute][Connect-ER-VNet].

La figura seguente illustra l'architettura di rete dopo la configurazione di ExpressRoute tra la connettività alle reti virtuali.

[![9]][9]

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo tra la connessione di reti virtuali alle reti locali tramite ExpressRoute. La tabella di route presenti route appartenente a entrambe le reti virtuali.

[![10]][10]

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc dopo tra la connessione di reti virtuali alle reti locali tramite ExpressRoute. La tabella di route presenti route appartenente a entrambe le reti virtuali.

[![11]][11]

La tabella seguente illustra le route noti per la sottoscrizione di Contoso della macchina virtuale. Prestare attenzione alla *gateway di rete virtuale* voci della tabella. La macchina virtuale vede le route per entrambe le reti locali.

[![12]][12]

La tabella seguente illustra le route noti per la sottoscrizione di Fabrikam della macchina virtuale. Prestare attenzione alla *gateway di rete virtuale* voci della tabella. La macchina virtuale vede le route per entrambe le reti locali.

[![13]][13]

>[!NOTE]
>In entrambi le sottoscrizioni di Fabrikam e/o di Contoso che è anche possibile impostare reti virtuali spoke all'hub rispettiva rete virtuale (una progettazione hub- spoke non è illustrata nei diagrammi di architettura in questo articolo). Le connessioni incrociate tra i gateway di rete virtuale dell'hub al circuito ExpressRoute consentirà anche la comunicazione tra est e Ovest hub e spoke.
>

## <a name="cross-connecting-on-premises-networks"></a>Tra la connessione di reti locali

Raggiungere globale ExpressRoute offre connettività tra reti locali connesse a circuiti ExpressRoute diversi. È possibile configurare raggiungere globali tra i circuiti Fabrikam ExpressRoute e Contoso. Poiché i circuiti ExpressRoute sono in sottoscrizioni diverse, è necessario creare e usare un'autorizzazione. Visualizzare [configurare ExpressRoute globale raggiungere] [ Configure Global Reach] articolo per indicazioni passo passo.

L'immagine seguente illustra l'architettura di rete dopo la configurazione globale raggiungere.

[![14]][14]

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo la configurazione globale raggiungere. La tabella di route presenti route appartenente a entrambe le reti locali. 

[![15]][15]

Nella tabella seguente mostra la tabella di route del peering privato di ExpressRoute di Fabrikam Inc dopo la configurazione globale raggiungere. La tabella di route presenti route appartenente a entrambe le reti locali.

[![16]][16]

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [domande frequenti sulla rete virtuale][VNet-FAQ]per eventuali altre domande su rete virtuale e il peering di rete virtuale. Visualizzare [domande frequenti su ExpressRoute] [ ER-FAQ] per qualsiasi domanda su ExpressRoute e virtuali la connettività di rete.

Copertura globale viene implementato in ogni singolo paese. Per verificare se Copertura globale è disponibile nei paesi desiderati, vedere [Copertura globale di ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scenario di applicazione"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabella di route Contoso ExpressRoute prima dell'unione"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "tabella di route Fabrikam ExpressRoute prima dell'unione"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM indirizza prima dell'unione"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM indirizza prima dell'unione"
[6]: ./media/cross-network-connectivity/vnet-peering.png "l'architettura dopo il peering reti virtuali"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "instrada Contoso VM dopo il peering reti virtuali"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "instrada Fabrikam VM dopo il peering reti virtuali"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "l'architettura dopo route Expressroute cross connection"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "tabella di route Contoso ExpressRoute dopo incrociata che si connette ExR e le reti virtuali"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "tabella di route Fabrikam ExpressRoute dopo incrociata che si connette ExR e le reti virtuali"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "che si connette ExR e le reti virtuali in più route Contoso VM dopo"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "che si connette ExR e le reti virtuali in più route di Fabrikam VM dopo"
[14]: ./media/cross-network-connectivity/globalreach.png "l'architettura dopo la configurazione di raggiungere globale"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "tabella di route Contoso ExpressRoute dopo raggiungere globale"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "tabella di route Fabrikam ExpressRoute dopo raggiungere globale"

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