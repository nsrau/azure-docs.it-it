---
title: Come configurare gli avvisi sulle metriche di Gateway VPN di Azure
description: Procedura per configurare gli avvisi sulle metriche di Gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769467"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Impostare gli avvisi sulle metriche di Gateway VPN

Questo articolo consente di impostare gli avvisi per le metriche di Gateway VPN. Monitoraggio di Azure offre la possibilità di impostare gli avvisi per le risorse di Azure. Gli avvisi possono essere impostati per i gateway di rete virtuale di tipo "VPN".


|**Metrica**   | **Unità** | **Granularity** | **Descrizione** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuti| Media di utilizzo della larghezza di banda combinati di tutte le connessioni site-to-site nel gateway.     |
|**P2SBandwidth**| Byte/s  | 1 minuto  | Media di utilizzo della larghezza di banda combinati di tutte le connessioni da punto a sito nel gateway.    |
|**P2SConnectionCount**| Conteggio  | 1 minuto  | Conteggio di P2S connessioni nel gateway.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuti  | Utilizzo medio della larghezza di banda del tunnel creati nel gateway. |
|**TunnelEgressBytes** | Byte | 5 minuti | Traffico in uscita nei tunnel creati nel gateway.   |
|**TunnelEgressPackets** | Conteggio | 5 minuti | Numero di pacchetti in uscita nel tunnel creati nel gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Conteggio | 5 minuti | Eliminata il conteggio dei pacchetti in uscita tunnel causato dalla mancata corrispondenza di Servizi terminal. |
|**TunnelIngressBytes** | Byte | 5 minuti | Traffico in ingresso nel tunnel creati nel gateway.   |
|**TunnelIngressPackets** | Conteggio | 5 minuti | Numero di pacchetti in entrata nel tunnel creati nel gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in ingresso eliminati nel tunnel causato dalla mancata corrispondenza di Servizi terminal. |


## <a name="setup"></a>Configurare gli avvisi di monitoraggio di Azure basati sulle metriche tramite il portale

La procedura di esempio seguente creerà un avviso su un gateway per: <br>

**Metrica:** Larghezza di banda media di tunnel <br>
**Condizione:** della larghezza di banda > 10 byte al secondo <br>
**Window:** 5 minuti <br>
**Azione di avviso:** Email <br>



1. Passare alla risorsa di gateway di rete virtuale e selezionare "Avvisi" nella scheda monitoraggio, quindi creare una nuova regola di avviso o modificare una regola di avviso esistente.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selezionare il gateway VPN come risorsa.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selezionare")

3. Selezionare una metrica da configurare per l'avviso ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selezionare")
4. Configurare la logica del segnale. Esistono tre componenti per la logica del segnale:

    a. Dimensioni: Se la metrica dispone di dimensioni, è possono selezionare i valori di dimensione specifico in modo che l'avviso viene valutato solo i dati di tale dimensione. Questi sono facoltativi.<br>
    b. Condizione: L'operazione per valutare il valore della metrica.<br>
    c. Ora: Specificare la granularità dei dati delle metriche e il periodo di tempo per valutare l'avviso.<br>

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selezionare")

5. Per visualizzare le regole configurate, fare clic su "Manage alert rules" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selezionare")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi nei log di diagnostica del tunnel, vedere [come configurare gli avvisi in log di diagnostica di Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
