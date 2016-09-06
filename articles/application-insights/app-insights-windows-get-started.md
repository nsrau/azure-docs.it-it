<properties
	pageTitle="Application Insights per app di Windows Phone e Windows Store | Microsoft Azure"
	description="Analizzare l'utilizzo e gli arresti anomali dell'app per dispositivi Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/26/2016"
	ms.author="awills"/>  

# Analisi per app di Windows Phone e Windows Store

Microsoft offre due soluzioni di sviluppo per i dispositivi: [HockeyApp](http://hockeyapp.net/), per l'analisi lato client, e [Application Insights](app-insights-overview.md), per il lato server.

[HockeyApp](http://hockeyapp.net/) è la soluzione DevOps mobile per la compilazione di app per dispositivi iOS, OS X, Android e Windows, nonché di app multipiattaforma basate su Xamarin, Cordova e Unity. Permette di distribuire le compilazioni ai beta tester, raccogliere dati sugli arresti anomali del sistema e ottenere commenti e metriche utente. L'integrazione con Visual Studio Team Services semplifica la distribuzione delle compilazioni e l'integrazione degli elementi di lavoro.

## Introduzione a HockeyApp

Passare a:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp per Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [Blog di HockeyApp](http://hockeyapp.net/blog/)
* Partecipando a [HockeyApp Preseason](http://hockeyapp.net/preseason/) è possibile accedere ai rilasci anticipati.

Se l'app ha un lato server, usare [Application Insights](app-insights-overview.md) per monitorare il lato server Web dell'app in [ASP.NET](app-insights-asp-net.md) o [J2EE](app-insights-java-get-started.md).


È anche possibile usare [Application Insights per app desktop Windows](app-insights-windows-desktop.md).

## Analisi, esportazione e accesso API ai dati di HockeyApp 

[Configurare un bridge HockeyApp](app-insights-hockeyapp-bridge-app.md) in Application Insights. Ciò consente di:

* Usare il linguaggio avanzato di query di [Analytics](app-insights-analytics.md) per la telemetria.
* [Esportare i dati di telemetria](app-insights-export-telemetry.md) nell'archivio BLOB di Azure.

## Passaggi successivi

* [Get started with HockeyApp for Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) (Introduzione a HockeyApp per Windows)

<!---HONumber=AcomDC_0831_2016-->