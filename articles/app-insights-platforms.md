<properties 
	pageTitle="Application Insights: piattaforme" 
	description="Si può usare Application Insights con ...?" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: piattaforme

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### Si può usare Application Insights con ...?


## Linguaggi

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [Pagine Web JavaScript](app-insights-web-track-usage.md)
+ [App JavaScript per Windows Store](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Piattaforme

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [App Web e VM Azure](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Desktop di Windows](app-insights-windows-desktop.md)


## Framework di registrazione

+	[Log4Net, NLog o System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J o Logback](app-insights-java-trace-logs.md)


## Progetti

Visitare anche il [progetto SDK di Application Insights su GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>App JavaScript per Cordova e Windows Store

In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.

Selezionare **Online** ed eseguire la ricerca in Application Insights.

Installare **API Application Insights per le applicazioni JavaScript**.

Usare lo [script app Web](app-insights-web-track-usage.md) del lato client standard, ma con una modifica.

Quando si ottiene lo script dal portale di Application Insights, inserire una riga dopo la chiave di strumentazione:

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[App di Windows Store con JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->