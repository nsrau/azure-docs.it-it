---
title: Impostare avvisi sulle metriche di Gateway VPN di Azure
description: Procedura per configurare gli avvisi sulle metriche di Gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605224"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Impostare avvisi sulle metriche di Gateway VPN

Questo articolo consente di impostare avvisi sulle metriche di Gateway VPN di Azure. Monitoraggio di Azure offre la possibilità di impostare gli avvisi per le risorse di Azure. È possibile impostare gli avvisi per i gateway di rete virtuale di tipo "VPN".


|**Metrica**   | **Unità** | **Granularity** | **Descrizione** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/sec  | 5 minuti| Media di utilizzo della larghezza di banda combinati di tutte le connessioni site-to-site nel gateway.     |
|**P2SBandwidth**| Bytes/sec  | 1 minuto  | Media di utilizzo della larghezza di banda combinati di tutte le connessioni da punto a sito nel gateway.    |
|**P2SConnectionCount**| Conteggio  | 1 minuto  | Numero di connessioni da punto a sito nel gateway.   |
|**TunnelAverageBandwidth** | Bytes/sec    | 5 minuti  | Utilizzo medio della larghezza di banda del tunnel creati nel gateway. |
|**TunnelEgressBytes** | Byte | 5 minuti | Traffico in uscita nei tunnel creati nel gateway.   |
|**TunnelEgressPackets** | Conteggio | 5 minuti | Numero di pacchetti in uscita nel tunnel creati nel gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Conteggio | 5 minuti | Eliminata il conteggio dei pacchetti in uscita tunnel causato dalla mancata corrispondenza del selettore di traffico. |
|**TunnelIngressBytes** | Byte | 5 minuti | Traffico in ingresso nel tunnel creati nel gateway.   |
|**TunnelIngressPackets** | Conteggio | 5 minuti | Numero di pacchetti in entrata nel tunnel creati nel gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in ingresso eliminati nel tunnel causato dalla mancata corrispondenza del selettore di traffico. |


## <a name="setup"></a>Impostare gli avvisi di monitoraggio di Azure basati sulle metriche tramite il portale di Azure

Nell'esempio seguente creerà un avviso su un gateway per:

- **Metrica:** TunnelAverageBandwidth
- **Condizione:** Larghezza di banda > 10 byte al secondo
- **Window:** 5 minuti
- **Azione di avviso:** Email



1. Passare alla risorsa del gateway di rete virtuale e selezionare **Alerts** dal **Monitoring** scheda. Creare una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selezionare il gateway VPN come risorsa.

   ![Il pulsante di selezione e il gateway VPN nell'elenco di risorse](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selezionare")

3. Selezionare una metrica da configurare per l'avviso.

   ![Selezionato metrica nell'elenco delle metriche](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selezionare")
4. Configurare la logica del segnale. Esistono tre componenti:

    a. **Dimensioni**: Se la metrica dispone di dimensioni, è possibile selezionare i valori della dimensione specifica in modo che l'avviso viene valutato solo i dati di tale dimensione. Questi sono facoltativi.

    b. **Condizione**: Questa è l'operazione per valutare il valore della metrica.

    c. **Tempo**: Specificare la granularità dei dati delle metriche e il periodo di tempo per valutare l'avviso.

   ![Dettagli per la configurazione per la logica di segnale](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selezionare")

5. Per visualizzare le regole configurate, selezionare **gestire le regole di avviso**.

   ![Pulsante per la gestione delle regole di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selezionare")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi sui log di diagnostica del tunnel, vedere [consente di impostare avvisi sui log di diagnostica di Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
