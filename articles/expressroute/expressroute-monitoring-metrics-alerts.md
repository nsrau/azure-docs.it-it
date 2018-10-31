---
title: Monitoraggio, metriche e avvisi di Microsoft Azure ExpressRoute | Microsoft Docs
description: Questa pagina fornisce informazioni sul monitoraggio di ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: fcdaf10ad30cee0e7b79304ff21d67560522875c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377798"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoraggio, metriche e avvisi di ExpressRoute

 Questo articolo illustra il monitoraggio, le metriche e gli avvisi di ExpressRoute. Monitoraggio di Azure è uno strumento centralizzato per tutte le metriche, gli avvisi, i log di diagnostica in Azure.

## <a name="circuit-metrics"></a>Metrica del circuito

Per passare a **Metrica**, fare clic sulla pagina di ExpressRoute relativa al circuito che si intende monitorare. In **Monitoraggio** è disponibile l'opzione **Metrica**. Selezionare BitsInPerSecond o BitsOutPerSecond e l'aggregazione. Facoltativamente, è possibile applicare una divisione che mostri le metriche per ogni tipo di peering.

![metrica del circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Metrica per peering

È possibile visualizzare le metriche in base al tipo di peering privato, pubblico e Microsoft in bit al secondo.

![metrica per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
* Configurare la connessione ExpressRoute.
  
  * [Creare e modificare un circuito](expressroute-howto-circuit-arm.md)
  * [Creare e modificare la configurazione di peering](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
