---
title: Progettazione per la disponibilità elevata con Azure ExpressRoute | Microsoft Docs
description: Questa pagina offre indicazioni architetturali per la disponibilità elevata quando si usa Azure ExpressRoute.
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
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466656"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Progettazione per la disponibilità elevata con ExpressRoute

ExpressRoute è progettato per la disponibilità elevata fornire la connettività di rete privata di livello per le risorse di Microsoft gestore telefonico. In altre parole, non è presente alcun singolo punto di guasto nel percorso di ExpressRoute nella rete di Microsoft. Per ottimizzare la disponibilità, il cliente e il segmento di provider del servizio del circuito ExpressRoute deve anche essere progettato per garantire la disponibilità elevata. In questo articolo, primo è possibile esaminare alle considerazioni sull'architettura di rete per la creazione di connettività di rete affidabile tramite un circuito ExpressRoute e quindi ora esaminate le funzionalità di ottimizzazione che consentono di migliorare la disponibilità elevata del circuito ExpressRoute.


## <a name="architecture-considerations"></a>Considerazioni sull'architettura

La figura seguente illustra il modo consigliato per connettersi tramite un circuito ExpressRoute per l'ottimizzazione della disponibilità di un circuito ExpressRoute.

 [![1]][1]

Per la disponibilità elevata, è essenziale per mantenere la ridondanza del circuito ExpressRoute in tutta la rete end-to-end. In altre parole, è necessario gestire la ridondanza all'interno della rete locale e non deve compromettere la ridondanza all'interno della rete del provider del servizio. Mantenere la ridondanza minimo implica evitando singoli punti di errore di rete. Con ridondanza di alimentazione e raffreddamento a sé per la rete i dispositivi verranno inoltre migliorare la disponibilità elevata.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerazioni sulla progettazione di primo miglia livello fisico

 Se si interrompe sia le connessioni primarie e secondarie di un circuiti di ExpressRoute nella stessa cliente locale delle apparecchiature (CPE), si sta compromettere la disponibilità elevata all'interno della rete locale. Inoltre, se si configura entrambe le connessioni primarie e secondarie tramite la stessa porta di un CPE (terminando le due connessioni sotto sottointerfacce diverse oppure mediante l'unione di due connessioni all'interno della rete di partner), si impone al partner per compromettere la disponibilità elevata sul proprio segmento di rete nonché. Questo compromesso è illustrata nella figura seguente.

[![2]][2]

D'altra parte, se si interrompe il database primario e le connessioni secondarie di un circuiti ExpressRoute in posizioni geografiche diverse, quindi si potrebbero essere compromettere le prestazioni della connettività di rete. Se il traffico è attivamente con carico bilanciato tra il database primario e le connessioni secondarie vengono terminate in località geografiche diverse, potenziali differenza sostanziale nella latenza di rete tra i due percorsi comporta la rete non ottimali Prestazione. 

Per considerazioni di progettazione con ridondanza geografica, vedere [progettazione per il ripristino di emergenza con ExpressRoute][DR].

### <a name="active-active-connections"></a>Connessioni Active-active

Rete di Microsoft è configurato per funzionare le connessioni primarie e secondarie dei circuiti ExpressRoute in modalità attivo-attivo. Tuttavia, tramite i annunci della route, è possibile forzare le connessioni ridondanti di un circuito ExpressRoute per il funzionamento in modalità attiva-passiva. Annuncio BGP e route più specifici come anteposizione di As path sono le tecniche comuni utilizzate per rendere un percorso preferito rispetto a altro.

Per migliorare la disponibilità elevata, è consigliabile per il funzionamento di entrambe le connessioni di un circuito ExpressRoute in modalità attivo-attivo. Se si lascia le connessioni che operano in modalità attivo-attivo, Microsoft network caricherà per bilanciare il traffico tra le connessioni in base al flusso.

In esecuzione le connessioni primarie e secondarie di un circuito ExpressRoute in viso modalità attiva-passiva il rischio di entrambe le connessioni non superati dopo un errore nel percorso attivo. Le cause comuni di errore nel passaggio sono la mancanza di gestione attiva della connessione passivo e connessione passivo annuncio delle route non aggiornate.

In alternativa, esegue le connessioni primarie e secondarie di un circuito ExpressRoute in modalità attivo-attivo, comporta solo metà i flussi di esito negativo e il recupero reindirizzato, dopo un errore di connessione ExpressRoute. Di conseguenza, la modalità attivo-attivo in modo significativo consentiranno di migliorare il tempo medio di recupero (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT per il peering Microsoft 

Il peering Microsoft è progettato per la comunicazione tra gli endpoint pubblici. Comunemente, gli endpoint privati in locale sono indirizzo di rete convertito (esempio) con indirizzo IP pubblico in cui il cliente o la rete di partner prima che comunicano tramite peering Microsoft. Supponendo che si usano entrambe le connessioni primarie e secondarie in modalità attivo-attivo, dove e come si NAT ha un impatto sulla velocità di ripristino dopo un errore in una delle connessioni ExpressRoute. Nella figura riportata di seguito verranno illustrate due diverse opzioni NAT:

[![3]][3]

Nell'opzione 1, NAT viene applicato dopo la suddivisione del traffico tra le connessioni primarie e secondarie del circuito ExpressRoute. Per soddisfare i requisiti di NAT con stati, i pool NAT indipendenti vengono usati tra il database primario e i dispositivi secondari in modo che il traffico di ritorno potrebbe arrivare allo stesso dispositivo edge tramite il quale il flusso in uscita.

Nell'opzione 2, viene usato un pool NAT comune prima della suddivisione del traffico tra le connessioni primarie e secondarie del circuito ExpressRoute. È importante distinzione è importante che il pool NAT comune prima della suddivisione del traffico non ne implica l'introduzione di singolo punto di errore in questo modo compromettere la disponibilità elevata.

Con l'opzione 1, che segue un errore di connessione ExpressRoute, possibilità di raggiungere il pool NAT corrispondente viene interrotta. Pertanto, tutti i flussi di interruzione devono essere stabilita tramite TCP nuovamente o livello dell'applicazione dopo il timeout della finestra corrispondente. Se uno dei pool NAT non è utilizzato per front-end di un server in locale e se la connettività corrispondente dovesse avere esito negativo, non è possibile raggiungere i server in locale da Azure finché non viene risolto la connettività.

Mentre con l'opzione 2, il protocollo NAT è raggiungibile anche dopo un errore di connessione primaria o secondaria. Pertanto, il livello di rete stesso può reindirizzare il ripristino più veloce i pacchetti e della Guida in linea segue l'errore. 

> [!NOTE]
> Se si usa NAT opzione 1 (indipendente dal pool di NAT per le connessioni di ExpressRoute primari e secondari) e mapping di una porta di un indirizzo IP da uno dei pool di NAT a un server in locale, il server non sarà raggiungibile tramite ExpressRoute quando per un circuito corrispondente connessione non riesce.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Ottimizzazione delle funzionalità per il peering privato

In questa sezione, invia i tuoi commenti verifica facoltativa (a seconda della distribuzione di Azure e come sensibili per ' MTTR) le caratteristiche che consentono di migliorare la disponibilità elevata del circuito ExpressRoute. In particolare, è opportuno esaminare distribuzione compatibile con la zona di gateway di rete virtuale per ExpressRoute e il rilevamento di inoltro bidirezionale (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Gateway di rete virtuale di zona di disponibilità compatibili con ExpressRoute

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Se si opta per la distribuzione IaaS di Azure con ridondanza della zona, è anche possibile configurare i gateway di rete virtuale con ridondanza della zona che termina con il peering privato di ExpressRoute. Per altre informazioni, vedere [informazioni sui gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Migliorare il tempo di rilevamento di errori

ExpressRoute supporta BFD tramite peering privato. BFD riduce il tempo di rilevamento dell'errore attraverso la rete di livello 2 tra Microsoft Enterprise Edge (msee) e i vicini BGP sul lato locale da circa 3 minuti (impostazione predefinita) a meno di un secondo. Tempo di rilevamento errore rapido consente accelerando ripristino dagli errori. Per altre informazioni, vedere [BFD configurare tramite ExpressRoute][BFD].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare per la disponibilità elevata di una connettività del circuito ExpressRoute. Un punto peering del circuito ExpressRoute viene aggiunto a una posizione geografica e pertanto può essere interessato da un errore irreversibile che influisce sul percorso intero. 

Per considerazioni sulla progettazione compilare la connettività di rete con ridondanza geografica per backbone di Microsoft che può resistere agli errori irreversibili, che influiscono su un'intera area, vedere [progettazione per il ripristino di emergenza conpeeringprivatodiExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "soluzione consigliata per la connessione tramite ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal ultimo connettività miglia"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opzioni NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




