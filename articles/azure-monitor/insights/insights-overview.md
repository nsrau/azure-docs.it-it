---
title: Panoramica delle informazioni dettagliate in Monitoraggio di Azure | Microsoft Docs
description: Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata in Monitoraggio di Azure per applicazioni e servizi specifici. Questo articolo fornisce una breve descrizione di tutte le informazioni dettagliate attualmente disponibili.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: ecd66500fdf968a773d3bc4f3d93363bfe86c763
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836040"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Panoramica delle informazioni dettagliate in Monitoraggio di Azure
Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata per determinati servizi e applicazioni. Archiviano i dati nella [piattaforma dati di Monitoraggio di Azure](../platform/data-platform.md) e sfruttano altre funzionalità di Monitoraggio di Azure per l'analisi e la creazione di avvisi, ma possono raccogliere dati aggiuntivi e offrire un'esperienza utente univoca nel portale di Azure. Accedere alle informazioni dettagliate dalla sezione **Informazioni dettagliate** del menu Monitoraggio di Azure nel portale di Azure.


Le sezioni seguenti forniscono una breve descrizione delle informazioni dettagliate attualmente disponibili in Monitoraggio di Azure. Per maggiori informazioni, vedere la documentazione dettagliata.

## <a name="application-insights"></a>Application Insights
Application Insights è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Funziona per le applicazioni su un'ampia gamma di piattaforme, tra cui .NET, Node.js e Java EE, ospitate in locale, ibride o in un cloud pubblico. Si integra anche con il processo DevOps e offre punti di connessione per diversi altri strumenti di sviluppo.

Vedere [Informazioni su Application Insights](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Vedere [Panoramica di Monitoraggio di Azure per contenitori](../insights/container-insights-overview.md).

![Monitoraggio di Azure per contenitori](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitoraggio di Azure per i gruppi di risorse (anteprima)
Monitoraggio di Azure per i gruppi di risorse consente di analizzare e diagnosticare i problemi riscontrati dalle singole risorse, offrendo al contempo un contesto dell'integrità e delle prestazioni del gruppo di risorse nel suo complesso.

Vedere [Monitorare i gruppi di risorse con Monitoraggio di Azure (anteprima)](../insights/resource-group-insights.md).

![Monitoraggio di Azure per i gruppi di risorse](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitoraggio di Azure per le macchine virtuali (anteprima)
Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni.

Vedere [Descrizione di Monitoraggio di Azure per le macchine virtuali](vminsights-overview.md)

![Monitoraggio di Azure per le macchine virtuali](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Monitoraggio di Azure per reti (anteprima)
[Monitoraggio di Azure per reti](network-insights-overview.md) offre una panoramica completa dell'integrità e delle metriche di tutte le risorse di rete. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, in modo da poter, ad esempio, identificare le risorse che ospitano il sito Web semplicemente cercando il nome del sito Web.

![Monitoraggio di Azure per le reti](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Passaggi successivi
* Leggere altre informazioni sulla [piattaforma dati di Monitoraggio di Azure](../platform/data-platform.md) usata per le informazioni dettagliate.
* Scoprire di più sulle varie [origini dati usate da Monitoraggio di Azure](../platform/data-sources.md) e sui tipi di dati raccolti per le diverse informazioni dettagliate.
