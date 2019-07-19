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
ms.date: 07/06/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: db79f2b1d3857aac2f71d2e18a3949f068b746eb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990065"
---
# <a name="languages---officially-supported-by-application-insights-team"></a>Lingue: supportato ufficialmente dal team Application Insights

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Pagine Web JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="community-sdks"></a>SDK della community

Sono disponibili diversi SDK della community applicazione Azure Insights, molti dei quali sono stati originariamente creati da Microsoft. Gli SDK della community non sono ufficialmente gestiti da Microsoft. Non è possibile fornire supporto per un SDK non incluso nell'elenco ufficialmente supportato. Questi SDK sono considerati sperimentali e non sono consigliati per l'uso in ambiente di produzione.

## <a name="platforms-and-frameworks"></a>Piattaforme e framework
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
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
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
