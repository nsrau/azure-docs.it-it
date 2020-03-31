---
title: 'Application Insights: linguaggi, piattaforme e integrazioni Documenti Microsoft'
description: Lingue, piattaforme e integrazioni disponibili per Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136724"
---
# <a name="supported-languages"></a>Lingue supportate

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Javascript](../../azure-monitor/app/javascript.md)
* [Nodo.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Piattaforme e framework supportati

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Strumentazione per applicazioni già distribuite (senza codice, basata su agenti)Instrumentation for already-deployed applications (codeless, agent-based)
* [Set di scalabilità di macchine virtuali di Azure VM e AzureAzure VM and Azure virtual machine scale sets](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Servizio app di Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - per app già attive](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Servizi cloud di Azure,](../../azure-monitor/app/cloudservices.md)inclusi i ruoli Web e di lavoro
* [Funzioni di AzureAzure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Strumentazione tramite codice (SDK)Instrumentation through code (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Nodo.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
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
* [Analisi di flusso](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>SDK non supportati
Siamo consapevoli dell'esistenza di diversi altri SDK supportati dalla community. Tuttavia, Monitoraggio di Azure fornisce supporto solo quando si usano gli SDK supportati elencati in questa pagina. Stiamo costantemente valutando le opportunità per espandere il nostro supporto per altre lingue, quindi segui la nostra pagina [Annunci GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) per ricevere le ultime notizie SDK. 
