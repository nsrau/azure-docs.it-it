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
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 825f51742a65ead545f7c7fcb0a53d1a82a08b1a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992807"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Developer Analytics: linguaggi, piattaforme e integrazioni
Di seguito sono riportate implementazioni di [Application Insights](../../azure-monitor/app/app-insights-overview.md) già illustrate, incluse alcune di terze parti.

## <a name="languages---officially-supported-by-application-insights-team"></a>Linguaggi: supportati ufficialmente dal team di Application Insights
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Pagine Web JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Linguaggi: supportati dalla community
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Altro](#projects)

## <a name="platforms-and-frameworks"></a>Piattaforme e framework
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET - per app già attive](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Servizio app di Azure](../../azure-monitor/app/azure-web-apps.md)
* 
  [Servizi cloud di Azure](../../azure-monitor/app/cloudservices.md)&#151;che includono sia i ruoli Web che i ruoli di lavoro
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](../../azure-monitor/app/java-get-started.md)
* [J2EE - per app già attive](../../azure-monitor/app/java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [App di Windows universale](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Ruoli di lavoro, servizi e applicazioni desktop di Windows](../../azure-monitor/app/windows-desktop.md)
* [Altro](#projects)

## <a name="logging-frameworks"></a>Framework di registrazione
* [Log4Net, NLog o System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J o Logback](../../azure-monitor/app/java-trace-logs.md)
* [Registrazione semantica (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - si integra con il [blocco applicazione per la registrazione semantica](https://msdn.microsoft.com/library/dn440729.aspx)
* [Test di carico basato su cloud](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Monitoraggio di Azure](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Sistemi di gestione del contenuto
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Esportazione e analisi dei dati
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Analisi dei flussi](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Creare un SDK personalizzato
Se non esiste ancora un SDK per il linguaggio o la piattaforma usata, è possibile crearne uno. A tale scopo, esaminare il codice degli SDK esistenti incluso nel [progetto di Application Insights SDK su GitHub](https://github.com/Microsoft/AppInsights-Home).
