---
title: Panoramica delle informazioni dettagliate in monitoraggio di Azure | Microsoft Docs
description: Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata in monitoraggio di Azure per applicazioni e servizi specifici. Questo articolo fornisce una breve descrizione di tutte le informazioni attualmente disponibili.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 3abf3d562b9ca4d6fc9907c065dbaf9a52d58c5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555354"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Panoramica delle informazioni dettagliate in monitoraggio di Azure
Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici. Archiviano i dati nella [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) e sfruttano altre funzionalità di monitoraggio di Azure per l'analisi e l'invio di avvisi, ma possono raccogliere dati aggiuntivi e fornire un'esperienza utente univoca nel portale di Azure. Accedere a Insights dalla sezione **Insights** del menu di monitoraggio di Azure nel portale di Azure.

Le sezioni seguenti forniscono una breve descrizione delle informazioni dettagliate attualmente disponibili in monitoraggio di Azure. Per informazioni dettagliate, vedere la documentazione dettagliata.

## <a name="application-insights"></a>Application Insights
Application Insights è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Funziona per le applicazioni in un'ampia gamma di piattaforme, tra cui .NET, node. js e Java EE, ospitate in locale, ibrido o qualsiasi cloud pubblico. Si integra anche con il processo DevOps e offre punti di connessione a un'ampia gamma di strumenti di sviluppo.

Vedere informazioni su [Application Insights](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per i contenitori
Monitoraggio di Azure per i contenitori monitora le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Vedere [Panoramica di monitoraggio di Azure per i contenitori](../insights/container-insights-overview.md).

![Monitoraggio di Azure per i contenitori](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitoraggio di Azure per i gruppi di risorse (anteprima)
Monitoraggio di Azure per i gruppi di risorse consente di valutare e diagnosticare eventuali problemi riscontrati dalle singole risorse, offrendo al contempo un contesto per l'integrità e le prestazioni del gruppo di risorse nel suo complesso.

Vedere [monitorare i gruppi di risorse con monitoraggio di Azure (anteprima)](../insights/resource-group-insights.md).

![Monitoraggio di Azure per i gruppi di risorse](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitoraggio di Azure per le macchine virtuali (anteprima)
Monitoraggio di Azure per le macchine virtuali consente di monitorare le macchine virtuali di Azure e i set di scalabilità di macchine virtuali su larga scala. Analizza le prestazioni e l'integrità delle macchine virtuali Windows e Linux, monitorando i processi e le dipendenze da altre risorse e processi esterni.

Vedere [che cos'è monitoraggio di Azure per le macchine virtuali?](vminsights-overview.md)

![Monitoraggio di Azure per le macchine virtuali](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Passaggi successivi
* Scopri di più sulla [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) sfruttando le informazioni dettagliate.
* Informazioni sulle diverse [origini dati usate da monitoraggio di Azure](../platform/data-sources.md) e sui diversi tipi di dati raccolti da ognuna delle informazioni dettagliate.
