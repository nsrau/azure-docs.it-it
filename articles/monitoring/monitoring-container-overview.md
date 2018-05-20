---
title: Panoramica del monitoraggio dei contenitori di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dei diversi metodi disponibili in Azure per monitorare i contenitori in Azure e conoscere rapidamente l'integrità e la disponibilità di un cluster.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Panoramica del monitoraggio dei contenitori in Azure
Con Azure, è possibile monitorare e gestire in modo efficace i carichi di lavoro distribuiti nei contenitori di Azure che eseguono Kubernetes o Docker. È importante conoscere le prestazioni dei contenitori con più applicazioni di microservizi per offrire un servizio Reliable Services su larga scala e supportare il piano di monitoraggio. Questo articolo fornisce una breve panoramica delle funzionalità di gestione e monitoraggio in Azure per poter determinare quelle più appropriate a seconda dei requisiti.

Usando l'[integrità dei contenitori di Monitoraggio di Azure](monitoring-container-health.md), è possibile visualizzare immediatamente le prestazioni e l'integrità dell'infrastruttura di contenitori Linux ed esaminare rapidamente i problemi. I dati di telemetria vengono archiviati in un'area di lavoro di Log Analytics e integrati nel portale di Azure, in cui è possibile esplorare, filtrare e segmentare i dati aggregati con i dashboard per tenere sotto controllo carico, prestazioni e integrità.  

Per i contenitori in esecuzione all'esterno di Azure Kubernetes Service ospitato, la [soluzione per contenitori Windows e Docker](../log-analytics/log-analytics-containers.md) di Log Analytics consente di visualizzare e gestire gli host dei contenitori Windows e Docker. Dall'area di lavoro di Log Analytics è possibile visualizzare dettagli sull'inventario, prestazioni ed eventi dei nodi e dei contenitori dell'ambiente. È possibile visualizzare informazioni dettagliate di controllo che illustrano i comandi usati con i contenitori e risolvere i problemi relativi ai contenitori visualizzando e cercando i log centralizzati senza dover accedere in remoto agli host Docker o Windows.

Per eseguire il monitoraggio olistico o end-to-end dell'applicazione, ogni dipendenza, che si tratti di una risorsa di Azure o locale, deve essere monitorata con Monitoraggio di Azure o con Log Analytics.  Il livello dell'applicazione deve essere incluso per aggiungere un ulteriore livello di conoscenza dell'integrità, a livello sia di piattaforma che di applicazione con Application Insights. A livello di piattaforma, sono disponibili gli SDK di Application Insights per [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/) e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Per le applicazione di microservizi, è disponibile il supporto non solo per [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md) e [.Net Core](../application-insights/app-insights-asp-net-core.md), ma anche per diversi altri [linguaggi/framework](../application-insights/app-insights-platforms.md). 

In caso contrario, resteranno non identificati problemi che potrebbero influire sulla disponibilità dell'applicazione e le destinazioni a livello di servizio non saranno rispettate.  
