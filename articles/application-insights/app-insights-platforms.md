---
title: 'Application Insights: linguaggi, piattaforme e integrazioni | Microsoft Docs'
description: Linguaggi, piattaforme e integrazioni disponibili per Application Insights
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: douge
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 42507475b5d15c4704e6bcb3d56dc00c91006655
ms.lasthandoff: 03/31/2017


---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Developer Analytics: linguaggi, piattaforme e integrazioni
Di seguito sono riportate implementazioni di [Application Insights](app-insights-overview.md) già illustrate, incluse alcune di terze parti.

## <a name="languages"></a>Lingue
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Pagine Web JavaScript](app-insights-web-track-usage.md)
* [Objective-C](https://github.com/Microsoft/ApplicationInsights-iOS)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Altro](#projects)

## <a name="platforms-and-frameworks"></a>Piattaforme e framework
* [Angular](https://www.npmjs.com/package/angular-applicationinsights)
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET - per app già attive](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET 5](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
* [App Web di Azure](app-insights-azure-web-apps.md)
* [Servizi cloud di Azure](app-insights-cloudservices.md)&#151;che includono sia i ruoli Web che i ruoli di lavoro
* [CRM Online da Microsoft Dynamics](app-insights-sample-mscrm.md)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE - per app già attive](app-insights-java-live.md)
* [App Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [App di Windows Universale](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [App Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [App Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Ruoli di lavoro, servizi e applicazioni desktop di Windows](app-insights-windows-desktop.md)
* [Altro](#projects)

## <a name="logging-frameworks"></a>Framework di registrazione
* [Log4Net, NLog o System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J o Logback](app-insights-java-trace-logs.md)
* [Registrazione semantica (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - si integra con il [blocco applicazione per la registrazione semantica](https://msdn.microsoft.com/library/dn440729.aspx)
* [Test di carico basato su cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics di OMS](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Sistemi di gestione del contenuto
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://orchardazureappinsights.codeplex.com)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Esportazione e analisi dei dati
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Analisi dei flussi](app-insights-export-power-bi.md)

## <a name="projects"></a> Creare un SDK personalizzato
Se non esiste ancora un SDK per il linguaggio o la piattaforma usata, è possibile crearne uno. A tale scopo, esaminare il codice degli SDK esistenti incluso nel [progetto di Application Insights SDK su GitHub](https://github.com/Microsoft/AppInsights-Home).

