---
title: 'Azure ExpressRoute: Progettazione per la disponibilità elevataAzure ExpressRoute: Designing for high availability'
description: Questa pagina fornisce consigli sull'architettura per la disponibilità elevata durante l'uso di Azure ExpressRoute.This page provides architectural recommendations for high availability while using Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076688"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Progettazione per la disponibilità elevata con ExpressRouteDesigning for high availability with ExpressRoute

ExpressRoute è progettato per la disponibilità elevata per fornire connettività di rete privata di livello carrier alle risorse Microsoft.ExpressRoute is designed for high availability to provide carrier grade private network connectivity to Microsoft resources. In altre parole, non esiste un singolo punto di errore nel percorso ExpressRoute all'interno della rete Microsoft.In other words, there is no single point of failure in the ExpressRoute path within Microsoft network. Per ottimizzare la disponibilità, anche il cliente e il segmento del provider di servizi del circuito ExpressRoute devono essere progettati per la disponibilità elevata. In questo articolo esaminiamo innanzitutto le considerazioni sull'architettura di rete per la creazione di una connettività di rete affidabile usando un ExpressRoute, quindi esaminiamo le funzionalità di ottimizzazione che consentono di migliorare la disponibilità elevata del circuito ExpressRoute.In this article, first let's look into network architecture considerations for building robust network connectivity using an ExpressRoute, then let's look into the fine-tuning features that help you to improve the high availability of your ExpressRoute circuit.


## <a name="architecture-considerations"></a>Considerazioni sull'architettura

La figura seguente illustra il modo consigliato per connettersi usando un circuito ExpressRoute per massimizzare la disponibilità di un circuito ExpressRoute.The following figure illustrates the recommended way to connect using an ExpressRoute circuit for maximizing the availability of an ExpressRoute circuit.

 [![1]][1 : (IN] viò:

Per la disponibilità elevata, è essenziale mantenere la ridondanza del circuito ExpressRoute in tutta la rete end-to-end. In altre parole, è necessario mantenere la ridondanza all'interno della rete locale e non compromettere la ridondanza all'interno della rete del provider di servizi. Mantenere la ridondanza al minimo implica evitare errori di rete singoli. Avere alimentazione e raffreddamento ridondanti per i dispositivi di rete migliorerà ulteriormente l'elevata disponibilità.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerazioni sulla progettazione di layer fisici del primo miglio

 Se si terminano le connessioni primarie e secondarie di un circuito ExpressRoute nella stessa apparecchiatura locale del cliente (CPE), si compromette la disponibilità elevata all'interno della rete locale. Inoltre, se si configurano le connessioni primarie e secondarie tramite la stessa porta di un CPE (terminando le due connessioni in sottointerfacce diverse o unendo le due connessioni all'interno della rete partner), si sta forzando il partner per compromettere l'elevata disponibilità anche sul segmento di rete. Questo compromesso è illustrato nella figura seguente.

[![2]][2]

D'altra parte, se si terminano le connessioni primarie e secondarie di un circuito ExpressRoute in posizioni geografiche diverse, è possibile compromettere le prestazioni di rete della connettività. Se il traffico viene attivamente bilanciato tra le connessioni primarie e secondarie terminate in posizioni geografiche diverse, la differenza sostanziale potenziale nella latenza di rete tra i due percorsi comporterebbe una rete non ottimale Prestazione. 

Per considerazioni sulla progettazione con ridondanza geografica, vedere Progettazione per il ripristino di [emergenza con ExpressRoute.][DR]

### <a name="active-active-connections"></a>Connessioni attivo-attivo

La rete Microsoft è configurata per gestire le connessioni primarie e secondarie dei circuiti ExpressRoute in modalità attivo-attivo. Tuttavia, tramite gli annunci di route, è possibile forzare le connessioni ridondanti di un circuito ExpressRoute a funzionare in modalità attivo-passivo. Pubblicità di route più specifiche e percorso BGP AS anteporre sono le tecniche comuni utilizzate per rendere un percorso preferito rispetto all'altro.

Per migliorare la disponibilità elevata, è consigliabile gestire entrambe le connessioni di un circuito ExpressRoute in modalità attivo-attivo. Se si consente alle connessioni di operare in modalità attivo-attivo, la rete Microsoft consentirà di bilanciare il carico del traffico tra le connessioni in base al flusso.

L'esecuzione delle connessioni primarie e secondarie di un circuito ExpressRoute in modalità attivo-passiva deve affrontare il rischio di entrambe le connessioni non riuscite in seguito a un errore nel percorso attivo. Le cause più comuni di fallimento nel passaggio sono la mancanza di gestione attiva della connessione passiva e la connessione passiva che annuncia route non aggiornate.

In alternativa, l'esecuzione delle connessioni primarie e secondarie di un circuito ExpressRoute in modalità attivo-attivo comporta solo circa la metà dei flussi con esito negativo e il reindirizzamento in seguito a un errore di connessione ExpressRoute.Alternatively, running the primary and secondary connections of an ExpressRoute circuit in active-active mode, results in circa la metà dei flussi in esito negativo e il reindirizzamento, in seguito a un errore di connessione ExpressRoute. Pertanto, la modalità attivo-attivo contribuirà in modo significativo a migliorare il tempo medio di recupero (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT per il peering Microsoft 

Il peering Microsoft è progettato per la comunicazione tra endpoint pubblici. Pertanto, in genere, gli endpoint privati locali sono NATed (Network Address Translated) con IP pubblico nella rete cliente o partner prima di comunicare tramite peering Microsoft. Supponendo di usare le connessioni primarie e secondarie in modalità attivo-attivo, dove e come NAT ha un impatto sulla velocità di ripristino in seguito a un errore in una delle connessioni ExpressRoute.Assuming you use both the primary and secondary connections in active-active mode, where and how you NAT has an impact on how quickly you recover following a failure in one of the ExpressRoute connections. Nella figura seguente sono illustrate due diverse opzioni NAT:

[![3]][3]

Nell'opzione 1 NAT viene applicato dopo la divisione del traffico tra le connessioni primarie e secondarie di ExpressRoute. Per soddisfare i requisiti con stato di NAT, vengono utilizzati pool NAT indipendenti tra i dispositivi primario e secondario in modo che il traffico di ritorno arrivi allo stesso dispositivo perimetrale attraverso il quale il flusso è in uscita.

Nell'opzione 2 viene usato un pool NAT comune prima di suddividere il traffico tra le connessioni primarie e secondarie di ExpressRoute.In the option 2, a common NAT pool is used before splitting the traffic between the primary and secondary connections of the ExpressRoute. È importante fare la distinzione che il pool NAT comune prima di dividere il traffico non significa introdurre un singolo punto di errore, compromettendo così la disponibilità elevata.

Con l'opzione 1, in seguito a un errore di connessione ExpressRoute, la possibilità di raggiungere il pool NAT corrispondente viene interrotta. Pertanto, tutti i flussi interrotti devono essere ristabiliti da TCP o dal livello dell'applicazione dopo il timeout della finestra corrispondente. Se uno dei pool NAT viene usato per frontend uno dei server locali e se la connettività corrispondente dovesse non riuscire, i server locali non possono essere raggiunti da Azure finché la connettività non viene corretta.

Mentre con l'opzione 2, il NAT è raggiungibile anche dopo un errore di connessione primario o secondario. Pertanto, il livello di rete stesso può reindirizzare i pacchetti e consentire un ripristino più rapido in seguito all'errore. 

> [!NOTE]
> Se si usa l'opzione NAT 1 (pool NAT indipendenti per le connessioni ExpressRoute primarie e secondarie) e si esegue il mapping di una porta di un indirizzo IP da uno dei pool NAT a un server locale, il server non sarà raggiungibile tramite il circuito ExpressRoute quando il file connessione non riesce.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Ottimizzazione delle funzionalità per il peering privato

In questa sezione viene esaminato le funzionalità facoltative (a seconda della distribuzione di Azure e della sensibilità di MTTR) che consentono di migliorare la disponibilità elevata del circuito ExpressRoute.In this section, let let review optional (depending on your Azure deployment and as sensitive you're to MTTR) features that help improve high availability of your ExpressRoute circuit. In particolare, esaminiamo la distribuzione in grado di riconoscere le zone dei gateway di rete virtuale ExpressRoute e il rilevamento dell'inoltro bidirezionale (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Gateway di rete virtuale ExpressRoute in grado di riconoscere la zona di disponibilitàAvailability zone aware ExpressRoute virtual network gateways

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si opta per la distribuzione di Azure IaaS con ridondanza di zona, è anche possibile configurare gateway di rete virtuale con ridondanza di zona che terminano il peering privato ExpressRoute.If you opt for zone-redundant Azure IaaS deployment, you may also want to configure zone-redundant virtual network gateways that terminate ExpressRoute private peering. Per altre informazioni, vedere Informazioni sui gateway di rete virtuale con ridondanza di zona nelle zone di disponibilità di Azure.To learn further, see [About zone-redundant virtual network gateways in Azure Availability zones][zone redundant vgw]. Per configurare un gateway di rete virtuale con ridondanza di zona, vedere Creare un gateway di rete virtuale con ridondanza di zona nelle zone di disponibilità di Azure.To configure zone-redundant virtual network gateway, see Create a [zone-redundant virtual network gateway in Azure Availability zones.][conf zone redundant vgw]

### <a name="improving-failure-detection-time"></a>Miglioramento del tempo di rilevamento degli errori

ExpressRoute supporta BFD tramite peering privato. BFD riduce il tempo di rilevamento del guasto sulla rete di livello 2 tra Microsoft Enterprise Edge (MSE) e i relativi vicini BGP sul lato locale da circa 3 minuti (impostazione predefinita) a meno di un secondo. Il tempo di rilevamento rapido degli errori consente di accelerare il ripristino degli errori. Per altre informazioni, vedere [Configurare BFD su ExpressRoute.][BFD]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare la disponibilità elevata di una connettività del circuito ExpressRoute.In this article, we discussed how to design for high availability of an ExpressRoute circuit connectivity. Un punto di peering del circuito ExpressRoute viene bloccato a una posizione geografica e pertanto potrebbe essere influenzato da un errore irreversibile che influisce sull'intera posizione. 

Per considerazioni di progettazione per creare connettività di rete con ridondanza geografica alla backbone Microsoft in grado di resistere a errori irreversibili, che influiscono su un'intera area, vedere Progettazione per il ripristino di [emergenza con peering privato ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Modo consigliato per connettersi usando ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Connettività subottimale dell'ultimo miglio"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Opzioni NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




