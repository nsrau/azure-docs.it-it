---
title: 'Azure ExpressRoute: monitoraggio, metriche e avvisi'
description: Informazioni sul monitoraggio, le metriche e gli avvisi di Azure ExpressRoute con monitoraggio di Azure, un punto di interruzione per tutte le metriche, avvisi e log di diagnostica in Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: d92b5685722b8a37de3945caa1305a76b3cabb8a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206238"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi di ExpressRoute

Questo articolo illustra il monitoraggio, le metriche e gli avvisi di ExpressRoute usando Monitoraggio di Azure. Monitoraggio di Azure è uno strumento centralizzato per tutte le metriche, gli avvisi, i log di diagnostica in Azure.
 
>[!NOTE]
>L'uso delle **metriche classiche** non è consigliato.
>

## <a name="expressroute-metrics"></a>Metriche ExpressRoute

Per visualizzare le **metriche**, passare alla pagina *monitoraggio di Azure* e fare clic su *metriche*. Per visualizzare le metriche di **ExpressRoute** , filtrare in base al tipo di risorsa *circuiti ExpressRoute*. Per visualizzare **copertura globale** metriche, filtrare in base al tipo di risorsa *circuiti ExpressRoute* e selezionare una risorsa del circuito ExpressRoute con copertura globale abilitata. Per visualizzare le metriche **dirette di ExpressRoute** , filtrare il tipo di risorsa per le *porte ExpressRoute*. 

Una volta selezionata la metrica, verrà applicata l'aggregazione predefinita. Facoltativamente, è possibile applicare la suddivisione, che mostrerà la metrica con dimensioni diverse.

### <a name="available-metrics"></a>Metriche disponibili

|**Metrica**|**Categoria**|**Dimensione/i**|**Funzionalità**|
| --- | --- | --- | --- |
|Disponibilità ARP|Disponibilità|<ui><li>Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering (privato/pubblico/Microsoft)</ui></li>|ExpressRoute|
|Bgp Availability (Disponibilità BGP)|Disponibilità|<ui><li> Peer (router ExpressRoute primario/secondario)</ui></li><ui><li> Tipo di peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Traffico|<ui><li> Tipo di peering (ExpressRoute)</ui></li><ui><li>Collegamento (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Traffico| <ui><li>Tipo di peering (ExpressRoute)</ui></li><ui><li> Collegamento (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|Utilizzo CPU|Prestazioni| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|Pacchetti al secondo|Prestazioni| <ui><li>Istanza</ui></li>|Gateway di rete virtuale ExpressRoute|
|GlobalReachBitsInPerSecond|Traffico|<ui><li>Skey del circuito con peering (chiave del servizio)</ui></li>|Copertura globale|
|GlobalReachBitsOutPerSecond|Traffico|<ui><li>Skey del circuito con peering (chiave del servizio)</ui></li>|Copertura globale|
|AdminState|Connettività fisica|Collegamento|ExpressRoute Direct|
|LineProtocol|Connettività fisica|Collegamento|ExpressRoute Direct|
|RxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Connettività fisica|<ui><li>Collegamento</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>L'uso di *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* sarà visibile solo se viene stabilita almeno una connessione copertura globale.
>

## <a name="circuits-metrics"></a>Metriche circuiti

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in e out-metriche in tutti i peering

È possibile visualizzare le metriche in tutti i peering in un circuito ExpressRoute specifico.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metriche del circuito":::

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS in e out-metriche per peering

È possibile visualizzare le metriche in base al tipo di peering privato, pubblico e Microsoft in bit al secondo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metriche del circuito":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilità BGP-suddivisione per peer  

È possibile visualizzare la disponibilità quasi in tempo reale di BGP tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione BGP primaria per il peering privato e la seconda sessione BGP per il peering privato. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="metriche del circuito":::

### <a name="arp-availability---split-by-peering"></a>Disponibilità ARP-suddivisione per peering  

È possibile visualizzare la disponibilità quasi in tempo reale di [ARP](./expressroute-troubleshooting-arp-resource-manager.md) tra peering e peer (router ExpressRoute primari e secondari). Questo dashboard mostra la sessione ARP per il peering privato in entrambi i peer, ma è stata completata per il peering Microsoft nei peering. L'aggregazione predefinita (media) è stata utilizzata in entrambi i peer.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="metriche del circuito":::

## <a name="expressroute-direct-metrics"></a>Metriche dirette ExpressRoute

### <a name="admin-state---split-by-link"></a>Stato amministratore-divisione per collegamento

È possibile visualizzare lo stato amministratore per ogni collegamento della coppia di porte dirette ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="metriche del circuito":::

### <a name="bits-in-per-second---split-by-link"></a>Bit in al secondo-divisione per collegamento

È possibile visualizzare i bit in al secondo in entrambi i collegamenti della coppia di porte dirette ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="metriche del circuito":::

### <a name="bits-out-per-second---split-by-link"></a>BITS in uscita al secondo-divisione per collegamento

È anche possibile visualizzare i bit al secondo in entrambi i collegamenti della coppia di porte dirette ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="metriche del circuito":::

### <a name="line-protocol---split-by-link"></a>Protocollo linea-divisione per collegamento

È possibile visualizzare il protocollo della linea attraverso ogni collegamento della coppia di porte dirette ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="metriche del circuito":::

### <a name="rx-light-level---split-by-link"></a>Livello chiaro RX-divisione per collegamento

È possibile visualizzare il livello di luce RX (il livello di luce che la porta diretta di ExpressRoute sta **ricevendo**) per ogni porta. I livelli di luce RX integri in genere rientrano in un intervallo compreso tra-10 e 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="metriche del circuito":::

### <a name="tx-light-level---split-by-link"></a>Livello chiaro TX-divisione per collegamento

È possibile visualizzare il livello di luce del TX (il livello di luce che la porta diretta ExpressRoute **trasmette**) per ogni porta. I livelli Light TX integri in genere rientrano in un intervallo compreso tra-10 e 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="metriche del circuito":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metriche del gateway di rete virtuale ExpressRoute

### <a name="cpu-utilization---split-instance"></a>Utilizzo CPU-istanza divisa

È possibile visualizzare l'utilizzo della CPU delle istanze del gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="metriche del circuito":::

### <a name="packets-per-second---split-by-instance"></a>Pacchetti al secondo-divisione per istanza

È possibile visualizzare i pacchetti al secondo attraversando il gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="metriche del circuito":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Connessioni gateway di ExpressRoute in bit al secondo

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="metriche del circuito":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Avvisi per le connessioni gateway di ExpressRoute

1. Per configurare gli avvisi, passare a **monitoraggio di Azure**e quindi selezionare **avvisi**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="metriche del circuito":::
2. Fare clic su **+Seleziona destinazione** e selezionare la risorsa di connessione gateway di ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="metriche del circuito":::
3. Definire i dettagli dell'avviso.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="metriche del circuito":::
4. Definire e aggiungere il gruppo di azioni.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="metriche del circuito":::

## <a name="alerts-based-on-each-peering"></a>Avvisi basati su ciascun peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="metriche del circuito":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurare avvisi per i log attività sui circuiti

In **Criteri di avviso** è possibile selezionare **Log attività** per il tipo di segnale e selezionare il segnale.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="metriche del circuito":::

## <a name="additional-metrics-in-log-analytics"></a>Metrica aggiuntiva in Log Analytics

È anche possibile visualizzare le metriche di ExpressRoute passando alla risorsa del circuito ExpressRoute e selezionando la scheda *log* . Per le metriche di cui si esegue la query, l'output conterrà le colonne riportate di seguito.

|**Colonna**|**Tipo**|**Descrizione**|
| --- | --- | --- |
|TimeGrain|string|PT1M (i valori delle metriche vengono inseriti ogni minuto)|
|Conteggio|real|In genere uguale a 2 (ogni MSEE inserisce un singolo valore di metrica ogni minuto)|
|Minima|real|Il valore minimo dei due valori di metrica inseriti dai due MSEE|
|Massimo|real|Massimo dei due valori di metrica inseriti dai due MSEE|
|Media|real|Uguale a (minimo + massimo)/2|
|Totale|real|Somma dei due valori di metrica da entrambi MSEE (il valore principale da concentrare per la metrica sottoposta a query)|
  
## <a name="next-steps"></a>Passaggi successivi

Configurare la connessione ExpressRoute.
  
* [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
* [Creare e modificare configurazioni di peering](expressroute-howto-routing-arm.md)
* [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)