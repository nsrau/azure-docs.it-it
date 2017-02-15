---
title: Monitoraggio delle prestazioni per app Web per dispositivi mobili con Developer Analytics | Documentazione Microsoft
description: Monitoraggio delle prestazioni e dell&quot;utilizzo delle applicazioni per sviluppatori di app per dispositivi mobili. , desktop, servizio Web e app back-end con HockeyApp e Application Insights.
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 4d394ff22e57d162c5adf39c492ca76f3d1f9d3a


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Analisi di dispositivi mobili con HockeyApp e Application Insights
Monitorare le prestazioni e l'utilizzo delle app per dispositivi mobili e desktop beta test e distribuite con [HockeyApp](https://hockeyapp.net/). Monitorare le app back-end e di servizio Web di supporto con [Azure Application Insights](app-insights-overview.md). Analizzare i dati provenienti da app sia client che server in Analytics e visualizzare i grafici affiancati in un dashboard di Azure.

Microsoft Developer Analytics offre due soluzioni per il monitoraggio delle prestazioni delle applicazioni:

* **HockeyApp per app per dispositivi mobili** e desktop.
  * Distribuire le versioni di test a utenti selezionati.
  * Analisi degli arresti anomali.
  * Dati di telemetria personalizzati per l'analisi dell'utilizzo.
* **Application Insights per servizi e siti Web** e applicazioni back-end.
  * Metriche e avvisi di prestazioni e utilizzo.
  * Segnalazione di eccezioni e traccia diagnostica.
  * Ricerca diagnostica con filtri e collegamenti di telemetria correlati.

Entrambe le soluzioni offrono:

* **[Linguaggio di query analitico](app-insights-analytics.md)** avanzato per la diagnosi e l'analisi.
* **[Esportazione dei dati](app-insights-export-telemetry.md)** nella propria risorsa di archiviazione.
* Visualizzazione **dashboard integrato** di grafici e tabelle analitici.

## <a name="monitor-your-app-components"></a>Monitorare i componenti dell'app
Seguire le istruzioni di queste pagine per installare l'SDK nel codice e iniziare a tenere traccia dell'app.

### <a name="web-apps---application-insights"></a>App Web: Application Insights
* [App Web ASP.NET](app-insights-asp-net.md) 
* [App Web Java](app-insights-java-get-started.md)
* [App Web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Servizi cloud di Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>App per dispositivi mobili: HockeyApp
* [App iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [App Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [App Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [App Windows Universale](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [App Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [App Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Per le app desktop di Windows, è consigliabile usare HockeyApp, ma è anche possibile [inviare dati di telemetria da un'app di Windows ad Application Insights](app-insights-windows-desktop.md), ad esempio per provare a usare Application Insights.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analisi ed esportazione per la telemetria di HockeyApp
È possibile esaminare la telemetria dei log e personalizzata di HockeyApp con le funzionalità di analisi ed esportazione continua di Application Insights [configurando un bridge](app-insights-hockeyapp-bridge-app.md).




<!--HONumber=Nov16_HO3-->


