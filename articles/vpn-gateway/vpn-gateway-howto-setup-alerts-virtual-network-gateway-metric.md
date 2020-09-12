---
title: Configurare gli avvisi per le metriche del gateway VPN di Azure
description: Informazioni su come usare la portale di Azure per configurare gli avvisi di monitoraggio di Azure in base alle metriche per i gateway VPN di rete virtuale.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435658"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurare gli avvisi per le metriche del gateway VPN

Questo articolo consente di configurare gli avvisi per le metriche del gateway VPN di Azure. Monitoraggio di Azure offre la possibilità di configurare gli avvisi per le risorse di Azure. È possibile configurare gli avvisi per i gateway di rete virtuale del tipo "VPN".


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


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurare gli avvisi di monitoraggio di Azure in base alle metriche usando il portale di Azure

L'esempio seguente illustra come creare un avviso su un gateway per:

- **Metrica:** TunnelAverageBandwidth
- **Condizione:** Larghezza di banda > 10 byte al secondo
- **Finestra:** 5 minuti
- **Azione avviso:** Posta elettronica



1. Passare alla risorsa gateway di rete virtuale e selezionare **avvisi** dalla scheda **monitoraggio** . Creare quindi una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selezionare il gateway VPN come risorsa.

   ![Il pulsante Seleziona e il gateway VPN nell'elenco delle risorse](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. Selezionare una metrica da configurare per l'avviso.

   ![Metrica selezionata nell'elenco delle metriche](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. Configurare la logica del segnale. Sono disponibili tre componenti:

    a. **Dimensioni**: se la metrica ha dimensioni, è possibile selezionare valori di dimensione specifici in modo che l'avviso valuti solo i dati di tale dimensione. Si tratta di un parametro facoltativo.

    b. **Condizione**: questa operazione consente di valutare il valore della metrica.

    c. **Time**: specificare la granularità dei dati della metrica e il periodo di tempo per valutare l'avviso.

   ![Dettagli per la configurazione della logica del segnale](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. Per visualizzare le regole configurate, selezionare **Gestisci regole di avviso**.

   ![Pulsante per la gestione delle regole di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi per i log delle risorse del tunnel, vedere [configurare gli avvisi nei log delle risorse del gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
