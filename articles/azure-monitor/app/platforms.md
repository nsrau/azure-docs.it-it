---
title: 'Application Insights: linguaggi, piattaforme e integrazioni | Microsoft Docs'
description: Linguaggi, piattaforme e integrazioni disponibili per Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: ab573d1d9ca9d9db39135ea76e555bb0720d7b68
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878788"
---
# <a name="supported-languages"></a>Lingue supportate

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Piattaforme e framework supportati

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Strumentazione per applicazioni già distribuite (senza codice, basate su agenti)
* [VM di Azure e set di scalabilità di macchine virtuali di Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servizio app di Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - per app già attive](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Servizi cloud di Azure](../../azure-monitor/app/cloudservices.md), inclusi i ruoli Web e di lavoro
* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Strumentazione tramite codice (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [App di Windows universale](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ruoli di lavoro, servizi e applicazioni desktop di Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Framework di registrazione
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog o System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J o Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Monitoraggio di Azure](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Esportazione e analisi dei dati
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Analisi dei flussi](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDK non supportati
Si è consapevoli che esistono molti altri SDK supportati dalla community. Tuttavia, monitoraggio di Azure fornisce il supporto solo quando si usano gli SDK supportati elencati in questa pagina. Microsoft sta valutando costantemente le opportunità per espandere il supporto per altre lingue, quindi seguire la pagina degli [annunci di GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) per ricevere le ultime notizie sull'SDK. 
