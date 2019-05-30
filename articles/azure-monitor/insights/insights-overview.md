---
title: Panoramica di Insights in Monitoraggio di Azure | Microsoft Docs
description: Insights offrono un'esperienza di monitoraggio personalizzata in Monitoraggio di Azure per servizi e applicazioni specifiche. Questo articolo fornisce una breve descrizione di ognuna delle informazioni che sono attualmente disponibili.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247230"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Panoramica di Insights in Monitoraggio di Azure
Insights forniscono un'esperienza di monitoraggio personalizzata per i servizi e applicazioni specifiche. Archiviano i dati nel [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) e sfruttare altre funzionalità di monitoraggio di Azure per l'analisi e avvisi, ma potrebbe raccogliere dati aggiuntivi e offrire un'esperienza utente univoco nel portale di Azure. Accedere a informazioni approfondite dal **Insights** sezione del menu di monitoraggio di Azure nel portale di Azure.

Le sezioni seguenti forniscono una breve descrizione delle informazioni che sono attualmente disponibili in Monitoraggio di Azure. Vedere la documentazione dettagliata per informazioni dettagliate su ognuno.

## <a name="application-insights"></a>Application Insights
Application Insights è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Funziona per le applicazioni su una vasta gamma di piattaforme, tra cui .NET, Node. js e Java EE, ospitato in locale, ibrido o qualsiasi cloud pubblico. Inoltre si integra con il processo DevOps e offre punti di connessione a un'ampia gamma di strumenti di sviluppo.

Visualizzare [What ' s Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Visualizzare [monitoraggio di Azure sui contenitori](../insights/container-insights-overview.md).

![Monitoraggio di Azure per contenitori](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitoraggio di Azure per i gruppi di risorse (anteprima)
Monitoraggio di Azure per i gruppi di risorse consente di valutare e diagnosticare eventuali problemi di singole risorse, offrendo al contesto per quanto riguarda l'integrità e le prestazioni del gruppo di risorse nel suo complesso.

Visualizzare [monitorare i gruppi di risorse con monitoraggio di Azure (anteprima)](../insights/resource-group-insights.md).

![Monitoraggio di Azure per i gruppi di risorse](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitoraggio di Azure per le macchine virtuali (anteprima)
Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni.

Vedere [che cos'è il monitoraggio di Azure per le macchine virtuali?](vminsights-overview.md)

![Monitoraggio di Azure per le macchine virtuali](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) sfruttate da insights.
* Informazioni sulle diverse [origini dati utilizzate da monitoraggio di Azure](../platform/data-sources.md) e i diversi tipi di dati raccolti da ognuna delle informazioni.
