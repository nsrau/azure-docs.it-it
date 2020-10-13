---
title: Visualizzare le metriche del gateway VPN di Azure
description: Passaggi per visualizzare le metriche del gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 4b33af3e64726e124373f57920836bce145cd891
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89443178"
---
# <a name="view-vpn-gateway-metrics"></a>Visualizzare le metriche di Gateway VPN

È possibile monitorare i gateway VPN di Azure con monitoraggio di Azure. Questo articolo illustra le metriche disponibili tramite il portale. Le metriche sono leggere e possono supportare scenari quasi in tempo reale, rendendoli utili per gli avvisi e il rilevamento rapido dei problemi.


|**Metrica**   | **Unità** | **Granularità** | **Descrizione** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuti| Utilizzo medio della larghezza di banda combinata di tutte le connessioni da sito a sito sul gateway.     |
|**P2SBandwidth**| Byte/s  | 1 minuto  | Utilizzo medio della larghezza di banda combinata di tutte le connessioni da punto a sito sul gateway.    |
|**P2SConnectionCount**| Conteggio  | 1 minuto  | Conteggio delle connessioni da punto a sito sul gateway.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuti  | Utilizzo medio della larghezza di banda dei tunnel creati sul gateway. |
|**TunnelEgressBytes** | Byte | 5 minuti | Traffico in uscita nei tunnel creati sul gateway.   |
|**TunnelEgressPackets** | Conteggio | 5 minuti | Numero di pacchetti in uscita nei tunnel creati sul gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in uscita eliminati nei tunnel causati da una mancata corrispondenza del selettore di traffico. |
|**TunnelIngressBytes** | Byte | 5 minuti | Traffico in ingresso nei tunnel creati sul gateway.   |
|**TunnelIngressPackets** | Conteggio | 5 minuti | Numero di pacchetti in ingresso nei tunnel creati sul gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in ingresso eliminati nei tunnel causati da una mancata corrispondenza del selettore di traffico. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>La procedura seguente consente di individuare e visualizzare le metriche:

1. Passare alla risorsa gateway di rete virtuale nel portale
2. Selezionare **Panoramica** per visualizzare le metriche di ingresso e uscita del tunnel totali.

   ![Selezioni per la creazione di una regola di avviso](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Visualizzazione")

3. Per visualizzare le altre metriche elencate in precedenza. Fare clic sulla sezione **metrica** sotto la risorsa gateway di rete virtuale e selezionare la metrica nell'elenco a discesa.

   ![Il pulsante Seleziona e il gateway VPN nell'elenco delle risorse](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Select")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi sulle metriche del tunnel, vedere [configurare gli avvisi per le metriche del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Per configurare gli avvisi per i log delle risorse del tunnel, vedere [configurare gli avvisi nei log delle risorse del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
