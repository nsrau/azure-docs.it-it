---
title: Monitoraggio della rete WAN virtuale di Azure con Azure monitor Insights
description: Informazioni sul monitoraggio della rete WAN virtuale con Azure monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136259"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure monitor Insights per la rete WAN virtuale (anteprima)

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) per la rete WAN virtuale offre agli utenti e agli operatori la possibilità di visualizzare lo stato e lo stato della rete WAN virtuale, presentata tramite una mappa topologica con individuazione automatica. Lo stato e lo stato delle risorse sono sovrapposti sulla mappa per offrire una visualizzazione snapshot dell'integrità complessiva della rete WAN virtuale. Lo spostamento delle risorse è abilitato sulla mappa tramite l'accesso con un solo clic alle pagine di configurazione delle risorse del portale WAN virtuale.

Le metriche a livello di risorsa WAN virtuale vengono raccolte e presentate tramite una cartella di lavoro di metriche WAN virtuale pre-assemblata che mostra le metriche a livello di WAN virtuale, Hub, gateway e connessione. Questo articolo illustra la procedura per usare Azure monitor Insights per la rete WAN virtuale per visualizzare la topologia WAN virtuale e le metriche in un'unica posizione.

> [!NOTE]
> Il menu Insights è in fase di implementazione per questa versione di anteprima.
>

## <a name="before-you-begin"></a>Prima di iniziare

Nei passaggi descritti in questo articolo si presume che sia già stata distribuita una rete WAN virtuale con uno o più hub. Per creare una nuova rete WAN virtuale e un nuovo hub, attenersi ai passaggi descritti negli articoli seguenti:

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan)
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Visualizza topologia VWAN

Nella **rete WAN virtuale portale di Azure >**, dal menu **monitoraggio** a sinistra selezionare **Insights (anteprima)**. Viene visualizzata la **visualizzazione Insights**, che mostra la mappa delle dipendenze WAN virtuale e la minima cartella di lavoro di metrica di alto livello.

**Figura 1: menu di monitoraggio-Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Figura" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Nella visualizzazione **Insights** è possibile visualizzare le risorse della rete WAN virtuale individuate con individuazione automatica, ad esempio hub, gateway, firewall, connessioni e reti virtuali spoke, appliance virtuali di terze parti e rami in una rete WAN virtuale end-to-end, come illustrato nella **Figura 2**.

Lo stato **e lo stato** **delle risorse** sono codificati a colori e sovrapposti alle icone delle risorse nella mappa. Le metriche di alto livello di WAN virtuali, ad esempio le capacità dell'hub e l'utilizzo del gateway, vengono visualizzate a destra tramite una mini cartella di lavoro.

**Figura 2: visualizzazione Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Figura" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Visualizzazione dipendenze

La visualizzazione delle **dipendenze** per la rete WAN virtuale consente di visualizzare la vista interconnessa di tutte le risorse WAN virtuali organizzate in modo esteso in un'architettura hub-spoke.

**Figura 3: visualizzazione delle dipendenze di VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Mappa delle dipendenze" lightbox="./media/azure-monitor-insights/dependency-map.png":::

La mappa della vista dipendenze Visualizza le risorse seguenti come un grafo connesso:

* Hub WAN virtuali tra le diverse aree di Azure.
* Spoke reti virtuali che sono direttamente connessi all'hub.
* Siti di filiali VPN e ExpressRoute e utenti P2S connessi a ogni hub tramite le rispettive connessioni ExpressRoute, S2S e P2S e i gateway di rete virtuale.
* Firewall di Azure (inclusi i proxy del firewall di terze parti) distribuiti in un hub (hub protetto).
* Appliance di rete virtuale di terze parti distribuite in una reti virtuali spoke.

La mappa delle dipendenze Visualizza anche le reti virtuali connesse indirettamente (VNet con peering con una WAN virtuale spoke reti virtuali).

La mappa delle dipendenze consente di spostarsi agevolmente sulle impostazioni di configurazione di ogni risorsa. Ad esempio, è possibile passare il mouse sulla risorsa hub per visualizzare la configurazione di risorsa di base, ad esempio l'area hub e il prefisso Hub. Fare clic con il pulsante destro del mouse per accedere alla pagina portale di Azure della risorsa Hub.

**Figura 4: passare a informazioni specifiche della risorsa**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="informazioni sulle risorse":::

La barra di ricerca e di filtro nella visualizzazione dipendenze offre un modo semplice per eseguire ricerche nel grafico. Filtri diversi consentono di restringere la ricerca a un percorso e a uno stato specifici.

**Figura 5: ricerca e filtro**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="barra di ricerca e filtro" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Metriche dettagliate

È possibile selezionare **Visualizza metriche dettagliate** per accedere alla pagina **metrica** dettagliata. La pagina metrica è un dashboard preconfigurato con schede separate che forniscono informazioni utili sulla capacità, sulle prestazioni e sull'utilizzo delle risorse WAN virtuali a livello di WAN virtuale, a livello di hub e a singole connessioni.

**Figura 6: dashboard dettagliato delle metriche**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="metriche dettagliate" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle metriche in monitoraggio di Azure, vedere [metriche in monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).
* Per una descrizione completa di tutte le metriche WAN virtuali, vedere la pagina relativa alle [metriche e ai log WAN virtuali](logs-metrics.md).
