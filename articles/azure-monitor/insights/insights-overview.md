---
title: Panoramica di Insights in Monitor di Azure - Documenti Microsoft
description: Le informazioni dettagliate offrono un'esperienza di monitoraggio personalizzata in Monitoraggio di Azure per applicazioni e servizi specifici. Questo articolo fornisce una breve descrizione di ognuna delle informazioni disponibili.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657249"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Panoramica di Insights in Monitoraggio di AzureOverview of Insights in Azure Monitor
Insights offre un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici. Archiviano i dati nella piattaforma dati di Monitoraggio di Azure e sfruttano altre funzionalità di Monitoraggio di Azure per l'analisi e gli avvisi, ma possono raccogliere dati aggiuntivi e fornire un'esperienza utente univoca nel portale di Azure.They store data in the [Azure Monitor data platform](../platform/data-platform.md) and leverage other Azure Monitor features for analysis and alerting but can collect additional data and provide a unique user experience in the Azure portal. Accedere alle informazioni dettagliate dalla sezione **Informazioni dettagliate** del menu Monitoraggio di Azure nel portale di Azure.Access insights from the Insights section of the Azure Monitor menu in the Azure portal.

The following sections provide a brief description of the insights that are currently available in Azure Monitor. Vedere la documentazione dettagliata per i dettagli su ciascuno di essi.

## <a name="application-insights"></a>Application Insights
Application Insights è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Funziona per applicazioni su un'ampia gamma di piattaforme, tra cui .NET, Node.js e Java EE, ospitati in locale, ibrido o qualsiasi cloud pubblico. Si integra inoltre con il processo DevOps e dispone di punti di connessione a una varietà di strumenti di sviluppo.

Vedere [Che cos'è Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Vedere [Panoramica di Monitoraggio di Azure per i contenitori.](../insights/container-insights-overview.md)

![Monitoraggio di Azure per contenitori](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitoraggio di Azure per i gruppi di risorse (anteprima)Azure Monitor for Resource Groups (preview)
Monitoraggio di Azure per i gruppi di risorse consente di valutare e diagnosticare eventuali problemi riscontrati dalle singole risorse, offrendo al contesto sull'integrità e le prestazioni dell'intero gruppo di risorse.

Vedere Monitorare i gruppi di [risorse con Monitoraggio di Azure (anteprima).](../insights/resource-group-insights.md)

![Azure Monitor for Resource Groups](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitoraggio di Azure per macchine virtuali (anteprima)Azure Monitor for VMs (preview)
Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni.

Vedere [Che cos'è Monitoraggio di Azure per le macchine virtuali?](vminsights-overview.md)

![Monitoraggio di Azure per le macchine virtuali](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Monitoraggio di Azure per reti (anteprima)Azure Monitor for Networks (preview)
[Monitoraggio di Azure per reti](network-insights-overview.md) offre una visualizzazione completa dell'integrità e delle metriche per tutte le risorse di rete. La funzionalità di ricerca avanzata consente di identificare le dipendenze delle risorse, consentendo scenari come l'identificazione delle risorse che ospitano il sito Web, semplicemente cercando il nome del sito Web.

![Monitoraggio di Azure per le reti](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla piattaforma dati di [Monitoraggio di Azure](../platform/data-platform.md) sfruttata dalle informazioni dettagliate.
* Informazioni sulle diverse [origini dati usate da Monitoraggio di Azure](../platform/data-sources.md) e sui diversi tipi di dati raccolti da ognuna delle informazioni dettagliate.
