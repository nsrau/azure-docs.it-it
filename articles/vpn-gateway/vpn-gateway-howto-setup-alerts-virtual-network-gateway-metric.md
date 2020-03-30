---
title: Configurare avvisi nelle metriche del gateway VPN di AzureSet up alerts on Azure VPN Gateway metrics
description: Passaggi per configurare gli avvisi nelle metriche del gateway VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605224"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurare avvisi sulle metriche del gateway VPNSet up alerts on VPN Gateway metrics

Questo articolo illustra come configurare gli avvisi nelle metriche del gateway VPN di Azure.This article helps you set up alerts on Azure VPN Gateway metrics. Monitoraggio di Azure offre la possibilità di configurare avvisi per le risorse di Azure.Azure Monitor provides the ability to set up alerts for Azure resources. È possibile impostare avvisi per i gateway di rete virtuale di tipo "VPN".


|**Metrica**   | **Unità** | **Granularità** | **Descrizione** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/i  | 5 minuti| Utilizzo medio combinato della larghezza di banda di tutte le connessioni da sito a sito nel gateway.     |
|**P2SBandwidth**| Byte/i  | 1 minuto  | Utilizzo medio combinato della larghezza di banda di tutte le connessioni da punto a sito nel gateway.    |
|**P2SConnectionCount**| Conteggio  | 1 minuto  | Numero di connessioni da punto a sito nel gateway.   |
|**TunnelAverageBandwidth** | Byte/i    | 5 minuti  | Utilizzo medio della larghezza di banda dei tunnel creati nel gateway. |
|**TunnelEgressBytes** | Byte | 5 minuti | Traffico in uscita su tunnel creati sul gateway.   |
|**TunnelEgressPackets** | Conteggio | 5 minuti | Numero di pacchetti in uscita nei tunnel creati sul gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in uscita caduti sui tunnel causati da mancata corrispondenza del selettore di traffico. |
|**TunnelIngressBytes** | Byte | 5 minuti | Traffico in ingresso su tunnel creati nel gateway.   |
|**TunnelIngressPackets** | Conteggio | 5 minuti | Numero di pacchetti in ingresso nei tunnel creati nel gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Conteggio | 5 minuti | Numero di pacchetti in ingresso caduti su tunnel causati da mancata corrispondenza del selettore di traffico. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurare gli avvisi di Monitoraggio di Azure in base alle metriche tramite il portale di AzureSet up Azure Monitor alerts based on metrics by using the Azure portal

I passaggi di esempio seguenti creeranno un avviso in un gateway per:The following example steps will create an alert on a gateway for:

- **Metrica:** Larghezza di banda TunnelMedia
- **Condizione:** Larghezza di banda > 10 byte/secondo
- **Finestra:** 5 minuti
- **Azione di avviso:** Posta elettronica



1. Passare alla risorsa gateway di rete virtuale e selezionare Avvisi nella scheda **Monitoraggio.Go** to the virtual network gateway resource and select **Alerts** from the Monitoring tab. Creare quindi una nuova regola di avviso o modificare una regola di avviso esistente.

   ![Selezioni per la creazione di una regola di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selezionare il gateway VPN come risorsa.

   ![Il pulsante Seleziona e il gateway VPN nell'elenco delle risorse](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. Selezionare una metrica da configurare per l'avviso.

   ![Metrica selezionata nell'elenco delle metriche](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. Configurare la logica del segnale. Ci sono tre componenti ad esso:

    a. **Dimensioni**: se la metrica contiene dimensioni, è possibile selezionare valori di dimensione specifici in modo che l'avviso valuti solo i dati di tale dimensione. Questi sono facoltativi.

    b. **Condizione**: Questa è l'operazione per valutare il valore della metrica.

    c. **Ora:** specificare la granularità dei dati delle metriche e il periodo di tempo per valutare l'avviso.

   ![Dettagli per la configurazione della logica del segnaleDetails for configuring signal logic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. Per visualizzare le regole configurate, selezionare **Gestisci regole di avviso**.

   ![Pulsante per la gestione delle regole di avviso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>Passaggi successivi

Per configurare gli avvisi nei log di diagnostica del tunnel, vedere [Impostare avvisi nei log di diagnostica del gateway VPN.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)
