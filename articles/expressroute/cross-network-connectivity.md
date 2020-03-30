---
title: Connettività tra reti di AzureAzure cross-network connectivity
description: Questa pagina descrive uno scenario di applicazione per la connettività tra reti e la soluzione basata sulle funzionalità di rete di Azure.This page describes an application scenario for cross network connectivity and solution based on Azure networking features.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644257"
---
# <a name="cross-network-connectivity"></a>Connettività tra reti

Fabrikam Inc. è ampiamente presente negli Stati Uniti orientali, dove ha una distribuzione di Azure. Fabrikam stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute. Analogamente, Contoso Ltd. ha una presenza e una distribuzione di Azure negli Stati Uniti occidentali. Contoso stabilisce la connettività back-end tra la distribuzione locale e quella di Azure tramite ExpressRoute.  

acquisisce Contoso Ltd. In seguito alla fusione, Fabrikam desidera interconnettere le reti. Lo scenario viene illustrato nell'immagine seguente:

 [![1]][1 : (IN] viò:

Le frecce tratteggiate al centro della figura precedente indicano le interconnessioni di rete desiderate. In particolare, sono desiderati tre tipi di connessioni tra connessioni: 1) Fabrikam e Contoso VNets cross connect, 2) Cross regional e VNets cross-connects (ovvero, connessione della rete locale Fabrikam alla rete virtuale contoso e connessione di Contoso rete locale a Fabrikam VNet) e 3) Fabrikam e Contoso connessione incrociata di rete locale. 

Nella tabella seguente viene illustrata la tabella delle route del peering privato di ExpressRoute di Contoso Ltd., prima della fusione.

[![2]][2]

Nella tabella seguente vengono illustrate le route effettive di una macchina virtuale nella sottoscrizione Contoso, prima della fusione. Per la tabella, la macchina virtuale nella rete virtuale è a conoscenza dello spazio di indirizzi della rete virtuale e della rete locale Contoso, oltre a quelle predefinite. 

[![4]][DEL pes)]

Nella tabella seguente viene illustrata la tabella di route del peering privato di ExpressRoute di Fabrikam Inc., prima della fusione.

[![3]][3]

Nella tabella seguente vengono illustrate le route effettive di una macchina virtuale nella sottoscrizione Fabrikam, prima della fusione. Per la tabella, la macchina virtuale nella rete virtuale è a conoscenza dello spazio di indirizzi della rete virtuale e della rete locale Fabrikam, oltre a quelle predefinite.

[![5]][5]

In questo articolo, esaminiamo passo dopo passo e discutono come ottenere le connessioni incrociate desiderate usando le seguenti funzionalità di rete di Azure:

* [Peering di rete virtualeVirtual network peering][Virtual network peering] 
* [Connessione ExpressRoute della rete virtualeVirtual network ExpressRoute connection][connection]
* [Copertura globale][Global Reach] 

## <a name="cross-connecting-vnets"></a>Collegamenti incrociati

Il peering di rete virtuale (peering VNet) offre le prestazioni di rete ottimali e ottimali quando si connettono due reti virtuali. Il peering della rete virtuale supporta il peering di due reti virtuali sia all'interno della stessa area di Azure (comunemente denominato peering della rete virtuale) che in due aree di Azure diverse (comunemente denominate peering della rete virtuale globale). 

È possibile configurare il peering della rete virtuale globale tra le reti virtuali nelle sottoscrizioni Contoso e Fabrikam Azure.Let's configure Global VNet peering between the VNets in Contoso and Fabrikam Azure subscriptions. Per informazioni su come creare il peering della rete virtuale tra due reti virtuali, vedere [L'articolo Creare un peering di rete virtuale.][Configure VNet peering]

Nella figura seguente viene illustrata l'architettura di rete dopo la configurazione del peering della rete virtuale globale.

[![6]][6]

The following table shows the routes known to the Contoso subscription VM. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

[![7]][7]

The following table shows the routes known to the Fabrikam subscription VM. Prestare attenzione all'ultima voce della tabella. Questa voce è il risultato della connessione incrociata delle reti virtuali.

[![8]][8]

Il peering vNet collega direttamente due reti virtuali (vedere che non sono presenti alcun hop successivo per la voce *VNetGlobalPeering* nelle due tabelle precedenti)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross connecting VNets alle reti locali

È possibile connettere un circuito ExpressRoute a più reti virtuali. Vedere Limiti di [sottoscrizione e servizio][Subscription limits] per il numero massimo di reti virtuali che possono essere connesse a un circuito ExpressRoute.See Subscription and service limits for the maximum number of virtual networks that can be connected to an ExpressRoute circuit. 

Connettere il circuito Fabrikam ExpressRoute alla rete virtuale di sottoscrizione Contoso e, in modo analogo, il circuito Contoso ExpressRoute alla rete virtuale di sottoscrizione Fabrikam per abilitare la connettività incrociata tra le reti virtuali e le reti locali. Per connettere una rete virtuale a un circuito ExpressRoute in una sottoscrizione diversa, è necessario creare e usare un'autorizzazione.  Vedere l'articolo: [Connettere una rete virtuale a un circuito ExpressRoute][Connect-ER-VNet].

Nella figura seguente viene illustrata l'architettura di rete dopo la configurazione della connettività incrociata ExpressRoute alle reti virtuali.

[![9]][9]

Nella tabella seguente viene illustrata la tabella di route del peering privato ExpressRoute di Contoso Ltd., dopo aver connesso le reti virtuali alle reti locali tramite ExpressRoute. Verificare che la tabella di route conda route appartenenti a entrambe le reti virtuali.

[![10]][10]

Nella tabella seguente viene illustrata la tabella di route del peering privato ExpressRoute di Fabrikam Inc., dopo aver connesso le reti virtuali alle reti locali tramite ExpressRoute. Verificare che la tabella di route conda route appartenenti a entrambe le reti virtuali.

[![11]][11]

The following table shows the routes known to the Contoso subscription VM. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La macchina virtuale vede le route per entrambe le reti locali.

[![12]][12]

The following table shows the routes known to the Fabrikam subscription VM. Prestare attenzione alle voci del *gateway di rete virtuale* della tabella. La macchina virtuale vede le route per entrambe le reti locali.

[![13]][13]

>[!NOTE]
>Nelle sottoscrizioni Fabrikam e/o Contoso è inoltre possibile disporre di reti virtuali spoke per la rispettiva rete virtuale hub (una progettazione hub e spoke non è illustrata nei diagrammi dell'architettura in questo articolo). Le connessioni incrociate tra i gateway della rete virtuale dell'hub a ExpressRoute consentiranno anche la comunicazione tra hub e spoke est e occidentali.
>

## <a name="cross-connecting-on-premises-networks"></a>Connessione incrociata di reti locali

La copertura globale ExpressRoute fornisce connettività tra reti locali connesse a circuiti ExpressRoute diversi. Configurare la copertura globale tra i circuiti Contoso e Fabrikam ExpressRoute.Let's configure Global Reach between Contoso and Fabrikam ExpressRoute circuits. Poiché i circuiti ExpressRoute si trovano in sottoscrizioni diverse, è necessario creare e usare un'autorizzazione. Vedere [l'articolo Configurare la copertura globale ExpressRoute][Configure Global Reach] per istruzioni dettagliate.

L'immagine seguente mostra l'architettura di rete dopo la configurazione della copertura globale.

[![14]][14]

Nella tabella seguente viene illustrata la tabella di route del peering privato di ExpressRoute di Contoso Ltd., dopo la configurazione della copertura globale. Verificare che la tabella di route disponga di route appartenenti a entrambe le reti locali. 

[![15]][15]

Nella tabella seguente viene illustrata la tabella di route del peering privato expressRoute di Fabrikam Inc., dopo la configurazione della copertura globale. Verificare che la tabella di route disponga di route appartenenti a entrambe le reti locali.

[![16]][16]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori domande sulla rete virtuale e sul peering della rete virtuale, vedere Domande frequenti sulla [rete virtuale,][VNet-FAQ]per ulteriori domande sulla rete virtuale e sul peering della rete virtuale. Vedere Domande frequenti su [ExpressRoute][ER-FAQ] per ulteriori domande su ExpressRoute e sulla connettività di rete virtuale.

La copertura globale viene implementata in base al paese o all'area geografica. Per verificare se la copertura globale è disponibile nei paesi o nelle aree geografiche desiderata, vedere [Copertura globale ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scenario di applicazione"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Tabella di route Contoso ExpressRoute prima della fusione"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Tabella di route Fabrikam ExpressRoute prima della fusione"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM instrada prima della fusione"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM instrada prima della fusione"
[6]: ./media/cross-network-connectivity/vnet-peering.png "L'architettura dopo" il peering della rete virtuale
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.pngContoso VM instrada dopo il "peering della rete virtuale7 Contoso VM routes after VNet peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.pngRoute della macchina virtuale Fabrikam dopo il "peering della rete virtuale8 Fabrikam VM routes after VNet peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "L'architettura dopo la connessione incrociata ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.pngTabella di "route ExpressRoute Contoso dopo la connessione incrociata" di exR e reti virtuali
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.pngTabella di "route Fabrikam ExpressRoute dopo la connessione incrociata" di exR e reti virtuali
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso VM instrada dopo la connessione incrociata" di ExR e reti virtuali
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.pngRoute della macchina virtuale Fabrikam dopo la "connessione incrociata di ExR e reti virtuali"
[14]: ./media/cross-network-connectivity/globalreach.png "L'architettura dopo aver configurato Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Tabella di route Contoso ExpressRoute dopo La copertura globale"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Tabella di route Fabrikam ExpressRoute dopo la copertura globale"

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