---
title: 'Azure ExpressRoute: Monitoring, Metrics, and Alerts'
description: Questa pagina fornisce informazioni sul monitoraggio di ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436904"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi di ExpressRoute

Questo articolo illustra il monitoraggio, le metriche e gli avvisi di ExpressRoute usando Monitoraggio di Azure. Monitoraggio di Azure è uno strumento centralizzato per tutte le metriche, gli avvisi, i log di diagnostica in Azure.
 
>[!NOTE]
>L'uso delle **metriche classiche** non è consigliato.
>

## <a name="expressroute-metrics"></a>Metriche ExpressRouteExpressRoute metrics

Per visualizzare **metriche**, passare alla pagina *Monitoraggio di Azure* e fare clic su *Metriche*. Per visualizzare le metriche **ExpressRoute,** filtrare in base *ai circuiti ExpressRoute*del tipo di risorsa. Per visualizzare le metriche **di copertura globale,** filtrare in base *ai circuiti ExpressRoute* del tipo di risorsa e selezionare una risorsa di circuito ExpressRoute con la copertura globale abilitata. Per visualizzare le metriche **di Direct ExpressRoute,** filtrare Il tipo di risorsa in base alle *porte ExpressRoute*. 

Dopo aver selezionato una metrica, verrà applicata l'aggregazione predefinita. Facoltativamente, è possibile applicare la divisione, che mostrerà la metrica con dimensioni diverse.

### <a name="available-metrics"></a>Metriche disponibili
|**Metrica**|**Category**|**Dimensioni(e)**|**Caratteristica(e)**|
| --- | --- | --- | --- |
|Disponibilità ARP|Disponibilità|<ui><li>Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering (Privato/Pubblico/Microsoft)</ui></li>|ExpressRoute|
|Disponibilità Bgp|Disponibilità|<ui><li> Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Traffico|<ui><li> Tipo di peering (ExpressRoute)Peering Type (ExpressRoute)</ui></li><ui><li>Collegamento (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Traffico| <ui><li>Tipo di peering (ExpressRoute)Peering Type (ExpressRoute)</ui></li><ui><li> Collegamento (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Traffico|<ui><li>Chiave del circuito con peered (chiave di servizio)</ui></li>|Copertura globale|
|GlobalReachBitsOutPerSecond|Traffico|<ui><li>Chiave del circuito con peered (chiave di servizio)</ui></li>|Copertura globale|
|Stato Amministratore|Connettività fisica|Collegamento|ExpressRoute Direct|
|LineProtocol (Protocollo Line)|Connettività fisica|Collegamento|ExpressRoute Direct|
|RxLightLevel|Connettività fisica|<ui><li>Link</ui></li><ui><li>corsia</ui></li>|ExpressRoute Direct|
|TxLightLevel (livello di illuminazione)|Connettività fisica|<ui><li>Link</ui></li><ui><li>corsia</ui></li>|ExpressRoute Direct|
>[!NOTE]
>L'utilizzo di *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* sarà visibile solo se viene stabilita almeno una connessione Global Reach.
>

## <a name="circuits-metrics"></a>Metriche dei circuiti

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bit In e Out - Metriche in tutti i peering

È possibile visualizzare le metriche in tutti i peering in un determinato circuito ExpressRoute.You can view metrics across all peerings on a given ExpressRoute circuit.

![metrica del circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bit In e Out - Metriche per peering

È possibile visualizzare le metriche in base al tipo di peering privato, pubblico e Microsoft in bit al secondo.

![metrica per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilità BGP - Divisa per peer  

È possibile visualizzare quasi la disponibilità in tempo reale di BGP tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione BGP primaria per il peering privato e la seconda sessione BGP inattiva per il peering privato. 

![Disponibilità BGP per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilità ARP - Suddivisione per peering  

È possibile visualizzare quasi la disponibilità in tempo reale di [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) tra peer e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione ARP di peering privato tra entrambi i peer, ma completa per il peering Microsoft tra peering. L'aggregazione predefinita (Average) è stata utilizzata in entrambi i peer.  

![Disponibilità ARP per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Metriche dirette ExpressRouteExpressRoute Direct Metrics

### <a name="admin-state---split-by-link"></a>Stato amministratore - Dividi per link
È possibile visualizzare lo stato di amministrazione per ogni collegamento della coppia di porte ExpressRoute Direct.You can view the admin state for each link of the ExpressRoute Direct port pair.

![er stato amministratore diretto](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bit in al secondo - Dividi per collegamento
È possibile visualizzare i bit in al secondo in entrambi i collegamenti della coppia di porte ExpressRoute Direct.You can view the bits in second in both links of the ExpressRoute Direct port pair. 

![er bit diretti in al secondo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bit out al secondo - Split by link
È anche possibile visualizzare i bit al secondo in entrambi i collegamenti della coppia di porte ExpressRoute Direct.You can also view the bits out per second across both links of the ExpressRoute Direct port pair. 

![er bit diretti al secondo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocollo di linea - Dividi per collegamento
È possibile visualizzare il protocollo di linea attraverso ogni collegamento della coppia di porte ExpressRoute Direct.You can view the line protocol across each link of the ExpressRoute Direct port pair.

![er protocollo di linea diretta](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Livello Luce - Dividi per link
È possibile visualizzare il livello di luce Rx (il livello di luce che riceve la porta Diretta ExpressRoute **)** per ogni porta. I livelli di luce Rx sani rientrano generalmente in un intervallo compreso tra -10 e 0 dBm

![er linea diretta Rx Livello luce](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx Livello Luce - Dividi per collegamento
È possibile visualizzare il livello di luce Tx (il livello di luce trasmesso dalla porta ExpressRoute Direct **)** per ogni porta. I livelli di luce Tx sani rientrano generalmente in un intervallo compreso tra -10 e 0 dBm

![er linea diretta Rx Livello luce](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connessioni gateway di ExpressRoute in bit al secondo

![connessioni gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Avvisi per le connessioni gateway di ExpressRoute

1. Per configurare gli avvisi, passare a **Monitoraggio di Azure**, quindi fare clic su **Avvisi**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Fare clic su **+Seleziona destinazione** e selezionare la risorsa di connessione gateway di ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definire i dettagli dell'avviso.

   ![gruppo di azioni](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definire e aggiungere il gruppo di azioni.

   ![aggiungi gruppo di azioni](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Avvisi basati su ciascun peering

 ![Dettagli](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurare avvisi per i log attività sui circuiti

In **Criteri di avviso** è possibile selezionare **Log attività** per il tipo di segnale e selezionare il segnale.

  ![altro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passaggi successivi

Configurare la connessione ExpressRoute.
  
  * [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
  * [Creare e modificare la configurazione di peering](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
