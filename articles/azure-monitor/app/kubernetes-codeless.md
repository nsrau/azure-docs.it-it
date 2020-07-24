---
title: Monitorare applicazioni nel servizio Azure Kubernetes (AKS) con Application Insights - Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure si integra perfettamente con l'applicazione in esecuzione su Kubernetes e consente di trovare i problemi con le app in tempi ridotti.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075306"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitoraggio dell'applicazione senza strumentazione per Kubernetes - Application Insights - Monitoraggio di Azure

> [!IMPORTANT]
>  Attualmente è possibile abilitare il monitoraggio per le app Java in esecuzione su Kubernetes senza instrumentare il codice. È sufficiente usare l'[agente autonomo Java](./java-in-process-agent.md). La soluzione per l'abilitazione del monitoraggio delle applicazioni è in corso di sviluppo per altri linguaggi, ma è già possibile usare i SDK per monitorare le app in esecuzione nel servizio Azure Kubernetes: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) e [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitoraggio delle applicazioni senza instrumentare il codice
Attualmente solo Java consente di abilitare il monitoraggio delle applicazioni senza instrumentare il codice. Per monitorare le applicazioni in altri linguaggi, usare i SDK. 

## <a name="java"></a>Java
Dopo che è stato abilitato, l'agente Java raccoglie automaticamente una grande quantità di richieste, dipendenze, log e metriche dalle librerie e dai framework più diffusi.

Seguire [le istruzioni dettagliate](./java-in-process-agent.md) per monitorare le app Java in esecuzione nelle app Kubernetes e in altri ambienti. 

## <a name="other-languages"></a>Altri linguaggi

Per le applicazioni in altri linguaggi è attualmente consigliabile usare i SDK:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Monitoraggio di Azure](../overview.md) e [Application Insights](./app-insights-overview.md)
* Panoramica di [Traccia distribuita](./distributed-tracing.md) e del contributo della [Mappa delle applicazioni](./app-map.md?tabs=net) all'attività aziendale
