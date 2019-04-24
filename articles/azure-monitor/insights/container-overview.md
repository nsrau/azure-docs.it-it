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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: d137576b4beb5cf36dce99ffb1869049f37b60b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494644"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Panoramica del monitoraggio dei contenitori in Azure
Con Azure, è possibile monitorare e gestire in modo efficace i carichi di lavoro distribuiti nei contenitori di Azure che eseguono Kubernetes o Docker. È importante conoscere le prestazioni dei contenitori con più applicazioni di microservizi per offrire un servizio Reliable Services su larga scala e supportare il piano di monitoraggio. Questo articolo fornisce una breve panoramica delle funzionalità di gestione e monitoraggio in Azure per poter determinare quelle più appropriate a seconda dei requisiti.

Usando [Monitoraggio di Azure per contenitori](container-insights-overview.md), è possibile visualizzare immediatamente le prestazioni e l'integrità dell'infrastruttura di contenitori Linux ed esaminare rapidamente i problemi. I dati di telemetria vengono archiviati in un'area di lavoro Log Analytics e integrati nel portale di Azure, in cui è possibile esplorare, filtrare e segmentare i dati aggregati con i dashboard per tenere sotto controllo carico, prestazioni e integrità.  

Per i contenitori in esecuzione all'esterno del servizio Azure Kubernetes ospitato, la [soluzione per contenitori Windows e Docker](../../azure-monitor/insights/containers.md) di Log Analytics consente di visualizzare e gestire gli host dei contenitori Windows e Docker. Dall'area di lavoro Log Analytics è possibile visualizzare dettagli sull'inventario, prestazioni ed eventi dei nodi e dei contenitori dell'ambiente. È possibile visualizzare informazioni dettagliate di controllo che illustrano i comandi usati con i contenitori e risolvere i problemi relativi ai contenitori visualizzando e cercando i log centralizzati senza dover accedere in remoto agli host Docker o Windows.

Per eseguire il monitoraggio olistico o end-to-end dell'applicazione, ogni dipendenza, che si tratti di una risorsa di Azure o locale, deve essere monitorata con Monitoraggio di Azure o con Log Analytics.  Il livello dell'applicazione deve essere incluso per aggiungere un ulteriore livello di conoscenza dell'integrità, a livello sia di piattaforma che di applicazione con Application Insights. A livello di piattaforma, sono disponibili gli SDK di Application Insights per [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/) e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Per le applicazioni di microservizi, è supportata [Java](../../azure-monitor/app/java-get-started.md), [Node. js](../../azure-monitor/learn/nodejs-quick-start.md), [.NET](../../azure-monitor/app/asp-net.md), [.NET Core](../../azure-monitor/app/asp-net-core.md), oltre a numerose altre [linguaggi o Framework](../../azure-monitor/app/platforms.md). 

In caso contrario, resteranno non identificati problemi che potrebbero influire sulla disponibilità dell'applicazione e le destinazioni a livello di servizio non saranno rispettate.  
