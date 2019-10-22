---
title: Progettazione per la disponibilità elevata con Azure ExpressRoute | Microsoft Docs
description: Questa pagina fornisce consigli sull'architettura per la disponibilità elevata durante l'uso di Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "67466656"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Progettazione per la disponibilità elevata con ExpressRoute

ExpressRoute è progettato per garantire un'elevata disponibilità per la connettività di rete privata di livello vettore alle risorse Microsoft. In altre parole, non esiste un singolo punto di errore nel percorso ExpressRoute all'interno della rete Microsoft. Per ottimizzare la disponibilità, è necessario progettare anche il segmento Customer e provider di servizi del circuito ExpressRoute per la disponibilità elevata. In questo articolo vengono esaminate prima di tutto le considerazioni sull'architettura di rete per la creazione di una connettività di rete affidabile usando un ExpressRoute, quindi si osserveranno le funzionalità di ottimizzazione che consentono di migliorare la disponibilità elevata del circuito ExpressRoute.


## <a name="architecture-considerations"></a>Considerazioni sull'architettura

La figura seguente illustra la modalità consigliata per connettersi usando un circuito ExpressRoute per massimizzare la disponibilità di un circuito ExpressRoute.

 [![1]][1]

Per la disponibilità elevata, è essenziale mantenere la ridondanza del circuito ExpressRoute in tutta la rete end-to-end. In altre parole, è necessario mantenere la ridondanza all'interno della rete locale e non deve compromettere la ridondanza all'interno della rete del provider di servizi. Il mantenimento della ridondanza minima implica la prevenzione del singolo punto di errore di rete. Il risparmio di energia e il raffreddamento ridondante per i dispositivi di rete miglioreranno ulteriormente la disponibilità elevata.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerazioni sulla progettazione del livello fisico del primo miglio

 Se si terminano sia le connessioni primarie che secondarie di un circuito ExpressRoute nello stesso sistema CPE (Customer locale Equipment), si sta compromettendo la disponibilità elevata nella rete locale. Inoltre, se si configurano le connessioni primarie e secondarie tramite la stessa porta di un CPE (terminando le due connessioni in diverse sottointerfacce o unendo le due connessioni all'interno della rete partner), il partner verrà forzato per compromettere la disponibilità elevata anche sul segmento di rete. Questa compromissione è illustrata nella figura seguente.

[![2]][2]

D'altra parte, se si terminano le connessioni primarie e secondarie di un circuito ExpressRoute in posizioni geografiche diverse, si potrebbero compromettere le prestazioni di rete della connettività. Se il traffico viene attivamente sottoposta a bilanciamento del carico tra le connessioni primarie e secondarie interrotte in posizioni geografiche diverse, la potenziale differenza sostanziale nella latenza di rete tra i due percorsi risulterebbe una rete non ottimale prestazioni. 

Per considerazioni sulla progettazione con ridondanza geografica, vedere [progettazione per il ripristino di emergenza con ExpressRoute][DR].

### <a name="active-active-connections"></a>Connessioni attive-attive

Microsoft Network è configurato per il funzionamento delle connessioni primarie e secondarie dei circuiti ExpressRoute in modalità Active-Active. Tuttavia, tramite gli annunci di route, è possibile forzare le connessioni ridondanti di un circuito ExpressRoute in modo che funzionino in modalità attivo/passivo. L'annuncio di route più specifiche e BGP come percorso anteposto sono le tecniche comuni utilizzate per rendere un percorso preferito rispetto all'altro.

Per migliorare la disponibilità elevata, è consigliabile usare entrambe le connessioni di un circuito ExpressRoute in modalità Active-Active. Se si lascia che le connessioni funzionino in modalità attivo-attivo, Microsoft Network caricherà il carico del traffico tra le connessioni in base ai singoli flussi.

L'esecuzione delle connessioni primarie e secondarie di un circuito ExpressRoute in modalità attivo/passivo affronta il rischio che entrambe le connessioni abbiano esito negativo in seguito a un errore nel percorso attivo. Le cause più comuni di errore durante il trasferimento sono la mancanza di una gestione attiva della connessione passiva e la connessione passiva che annuncia le route non aggiornate.

In alternativa, l'esecuzione delle connessioni primarie e secondarie di un circuito ExpressRoute in modalità attivo-attivo comporta solo la metà dei flussi che non riescono e vengono reindirizzati, a seguito di un errore di connessione ExpressRoute. Quindi, la modalità Active-Active consente di migliorare significativamente il tempo medio di recupero (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT per il peering Microsoft 

Il peering Microsoft è progettato per la comunicazione tra endpoint pubblici. Quindi, in genere, gli endpoint privati locali sono tradotti come indirizzo di rete (NATed) con IP pubblico nella rete del cliente o del partner prima che comunicano tramite peering Microsoft. Supponendo di usare le connessioni primarie e secondarie in modalità attivo-attivo, dove e come si ha un effetto NAT sulla velocità di ripristino in seguito a un errore in una delle connessioni ExpressRoute. Nella figura seguente sono illustrate due diverse opzioni NAT:

[![3]][3]

Nell'opzione 1, NAT viene applicato dopo la suddivisione del traffico tra le connessioni primarie e secondarie di ExpressRoute. Per soddisfare i requisiti con stato di NAT, i pool NAT indipendenti vengono usati tra i dispositivi primari e secondari, in modo che il traffico di ritorno raggiunga lo stesso dispositivo perimetrale attraverso il quale il flusso uscita.

Nell'opzione 2 viene usato un pool NAT comune prima di suddividere il traffico tra le connessioni primarie e secondarie di ExpressRoute. È importante distinguere che il pool NAT comune prima di suddividere il traffico non implica l'introduzione di un singolo punto di errore, compromettendo così la disponibilità elevata.

Con l'opzione 1, in seguito a un errore di connessione ExpressRoute, la possibilità di raggiungere il pool NAT corrispondente è interruppe. Pertanto, tutti i flussi interrotti devono essere ristabiliti tramite il livello TCP o dell'applicazione dopo il timeout della finestra corrispondente. Se uno dei pool NAT viene usato per il front-end di uno dei server locali e se la connettività corrispondente ha esito negativo, non è possibile raggiungere i server locali da Azure fino a quando la connettività non è fissa.

Mentre con l'opzione 2, il NAT è raggiungibile anche dopo un errore di connessione primaria o secondaria. Pertanto, il livello di rete stesso può reindirizzare i pacchetti e contribuire a un ripristino più rapido dopo l'errore. 

> [!NOTE]
> Se si usa l'opzione NAT 1 (pool NAT indipendenti per le connessioni ExpressRoute primarie e secondarie) e si esegue il mapping di una porta di un indirizzo IP da uno del pool NAT a un server locale, il server non sarà raggiungibile tramite il circuito ExpressRoute quando il corrispondente connessione non riuscita.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Ottimizzazione delle funzionalità per il peering privato

In questa sezione vengono esaminate le funzionalità facoltative (a seconda della distribuzione di Azure e del modo in cui si è MTTR) che consentono di migliorare la disponibilità elevata del circuito ExpressRoute. In particolare, esaminiamo la distribuzione in grado di riconoscere le zone dei gateway di rete virtuale ExpressRoute e il rilevamento di inoltri bidirezionale (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>I gateway di rete virtuale ExpressRoute compatibili con la zona di disponibilità

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si opta per la distribuzione di Azure IaaS con ridondanza della zona, è anche possibile configurare i gateway di rete virtuale con ridondanza della zona che terminano il peering privato di ExpressRoute. Per ulteriori informazioni, vedere [informazioni sui gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure][zone redundant vgw]. Per configurare il gateway di rete virtuale con ridondanza della zona, vedere [creare un gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Miglioramento del tempo di rilevamento degli errori

ExpressRoute supporta BFD sul peering privato. BFD riduce il tempo di rilevamento dell'errore sulla rete di livello 2 tra Microsoft Enterprise Edge (MSEE) e i relativi adiacenti BGP sul lato locale da circa 3 minuti (impostazione predefinita) a meno di un secondo. Il tempo di rilevamento rapido degli errori consente di velocizzare il ripristino degli errori. Per altre informazioni, vedere [configurare BFD su ExpressRoute][BFD].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare per la disponibilità elevata di una connettività del circuito ExpressRoute. Un punto di peering del circuito ExpressRoute è aggiunto a una posizione geografica e pertanto può essere influenzato da un errore irreversibile che influisca sull'intera posizione. 

Per considerazioni sulla progettazione per la creazione di connettività di rete con ridondanza geografica a backbone Microsoft che possa resistere a errori irreversibili, che influiscano su un'intera area, vedere [progettazione per il ripristino di emergenza con peering privato ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "metodo consigliato per la connessione tramite ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "connettività Last Mile non ottimale"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opzioni NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




