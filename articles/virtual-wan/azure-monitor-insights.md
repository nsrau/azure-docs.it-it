---
title: Monitoraggio della rete WAN virtuale con Azure monitor Insights
description: Questo articolo illustra come monitorare la rete WAN virtuale di Azure con Azure monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 052fd0ea7619d566e78806580ee7b39e49cc85d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448611"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure monitor Insights per la rete WAN virtuale (anteprima)

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) per la rete WAN virtuale di Azure offre agli utenti e agli operatori la possibilità di visualizzare lo stato e lo stato di una rete WAN virtuale, presentata tramite una mappa topologica con individuazione automatica. Lo stato delle risorse e le sovrimpressioni sullo stato sulla mappa offrono una visualizzazione snapshot dell'integrità complessiva della rete WAN virtuale. È possibile esplorare le risorse sulla mappa tramite l'accesso con un solo clic alle pagine di configurazione delle risorse del portale WAN virtuale.

Le metriche a livello di risorsa WAN virtuale vengono raccolte e presentate tramite una cartella di lavoro di metriche WAN virtuale pre-assemblata. La cartella di lavoro Mostra le metriche a livello di WAN virtuale, Hub, gateway e connessione. Questo articolo illustra la procedura per usare Azure monitor Insights per la rete WAN virtuale per visualizzare la topologia WAN virtuale e le metriche in un'unica posizione.

> [!NOTE]
> L'opzione di menu **Insights** nel portale WAN virtuale è attualmente in fase di implementazione. Quando si esegue il rollup di questo menu, è possibile accedere alla cartella di lavoro della topologia WAN virtuale e alle metriche usando monitoraggio di Azure per le reti. Per altre informazioni, vedere [monitoraggio di Azure per le reti](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Prima di iniziare

Per completare i passaggi descritti in questo articolo, è necessario disporre di una rete WAN virtuale con uno o più hub. Per creare una rete WAN virtuale e un hub, seguire i passaggi descritti in questi articoli:

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan)
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Visualizza topologia VWAN

Passare a **portale di Azure**  >  **WAN virtuale**. Nel menu di **monitoraggio** nel riquadro sinistro selezionare **Insights (anteprima)**. Viene visualizzata la visualizzazione **Insights** . Mostra la mappa delle dipendenze WAN virtuale e la mini cartella di lavoro di **metrica** di alto livello.

**Figura 1: monitorare il menu > Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Nella visualizzazione **Insights** è possibile visualizzare le risorse della rete WAN virtuale individuate. Queste risorse includono Hub, gateway, firewall, connessioni e reti virtuali spoke, appliance virtuali di terze parti e rami in una rete WAN virtuale end-to-end. Per un esempio, vedere la **Figura 2**.

Lo stato e lo stato delle risorse sono codificati a colori e sovrapposti alle icone delle risorse nella mappa. Le metriche WAN virtuali di alto livello, come le capacità dell'hub e l'utilizzo del gateway, vengono visualizzate sul lato destro della finestra in una mini cartella di lavoro.

**Figura 2: visualizzazione Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima)." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Visualizzazione dipendenze

La visualizzazione delle **dipendenze** per la rete WAN virtuale consente di visualizzare la vista interconnessa di tutte le risorse WAN virtuali organizzate in modo ampio in un'architettura hub-spoke.

**Figura 3: visualizzazione delle dipendenze di VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima)." lightbox="./media/azure-monitor-insights/dependency-map.png":::

La mappa della vista **dipendenze** Visualizza le risorse seguenti come un grafo connesso:

* Hub WAN virtuali tra le diverse aree di Azure.
* Reti virtuali spoke che sono direttamente connesse all'hub.
* VPN e siti di Azure ExpressRoute Branch e utenti P2S connessi a ogni hub tramite le rispettive connessioni ExpressRoute, S2S e P2S e i gateway di rete virtuale.
* Firewall di Azure (inclusi i proxy firewall di terze parti) distribuiti in un hub (hub protetto).
* Appliance virtuali di terze parti (appliance virtuali di rete) distribuite in reti virtuali spoke.

La mappa delle dipendenze Visualizza anche reti virtuali connesse indirettamente (reti virtuali con peering con reti virtuali spoke virtuali).

La mappa delle dipendenze consente di spostarsi agevolmente sulle impostazioni di configurazione di ogni risorsa. Ad esempio, è possibile passare il mouse sulla risorsa hub per visualizzare la configurazione di risorsa di base, ad esempio l'area hub e il prefisso Hub. Fare clic con il pulsante destro del mouse per accedere alla pagina portale di Azure della risorsa Hub.

**Figura 4: passare a informazioni specifiche della risorsa**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima).":::

La barra di ricerca e filtro nella visualizzazione **dipendenze** fornisce un modo semplice per eseguire ricerche nel grafico. Diversi filtri consentono di limitare la ricerca a un percorso e a uno stato specifici.

**Figura 5: ricerca e filtro**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima)." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Metriche dettagliate

È possibile selezionare **Visualizza metriche dettagliate** per accedere alla pagina **metrica** dettagliata. La pagina **metrica** è un dashboard preconfigurato con schede separate. Queste schede forniscono informazioni dettagliate sulla capacità, sulle prestazioni e sull'utilizzo delle risorse WAN virtuali a livello di hub e di WAN virtuale e a livello di singole connessioni.

**Figura 6: dashboard dettagliato delle metriche**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Screenshot che mostra la visualizzazione Insights (anteprima)." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).
* Per una descrizione completa di tutte le metriche WAN virtuali, vedere la pagina relativa alle [metriche e ai log WAN virtuali](logs-metrics.md).
