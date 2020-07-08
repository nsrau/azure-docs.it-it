---
title: Monitorare applicazioni nel servizio Azure Kubernetes (AKS) con Application Insights - Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure si integra perfettamente con l'applicazione in esecuzione su Kubernetes e consente di trovare i problemi con le app in tempi ridotti.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773762"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Monitoraggio dell'applicazione senza strumentazione per Kubernetes - Application Insights - Monitoraggio di Azure

> [!IMPORTANT]
>  Attualmente è possibile abilitare il monitoraggio per le app Java in esecuzione su Kubernetes senza instrumentare il codice. È sufficiente usare l'[agente autonomo Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). La soluzione per l'abilitazione del monitoraggio delle applicazioni è in corso di sviluppo per altri linguaggi, ma è già possibile usare i SDK per monitorare le app in esecuzione nel servizio Azure Kubernetes: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) e [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitoraggio delle applicazioni senza instrumentare il codice
Attualmente solo Java consente di abilitare il monitoraggio delle applicazioni senza instrumentare il codice. Per monitorare le applicazioni in altri linguaggi, usare i SDK. 

## <a name="java"></a>Java
Dopo che è stato abilitato, l'agente Java raccoglie automaticamente una grande quantità di richieste, dipendenze, log e metriche dalle librerie e dai framework più diffusi.

Seguire [le istruzioni dettagliate](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) per monitorare le app Java in esecuzione nelle app Kubernetes e in altri ambienti. 

## <a name="other-languages"></a>Altri linguaggi

Per le applicazioni in altri linguaggi è attualmente consigliabile usare i SDK:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Panoramica di [Traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) e del contributo della [Mappa delle applicazioni](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) all'attività aziendale