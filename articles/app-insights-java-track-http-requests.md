<properties 
	pageTitle="Tenere traccia delle richieste HTTP in un'applicazione Web Java" 
	description="Application Insights consente di misurare le prestazioni dell'applicazione Web Java" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# Tenere traccia delle richieste HTTP in un'applicazione Web Java

Se si esegue un'applicazione Web Java, è possibile visualizzare le informazioni sulle richieste HTTP inviate all'applicazione, ad esempio le risorse richieste, le richieste non riuscite e i tempi di risposta, tutte nel portale di Application Insights.

Installare [Application Insights SDK per Java][java], se questa operazione non è già stata eseguita.


## Aggiungere i file binari al progetto

*Scegliere il modo più appropriato per il progetto.*

### Se si usa Maven...

Se il progetto è già stato configurato per usare Maven per la compilazione, unire il frammento di codice seguente al file pom.xml.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <version>[0.9,)</version>
      </dependency>
    </dependencies>

### Se si usa Gradle...

Se il progetto è già stato configurato per usare Gradle per la compilazione, unire il frammento di codice seguente al file build.gradle.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
    }

## Aggiungere il filtro HTTP di Application Insights al progetto

Individuare e aprire il file web.xml nel progetto e unire il frammento di codice seguente al di sotto del nodo app-web, in cui sono configurati i filtri dell'applicazione.

Per ottenere risultati più accurati, il filtro deve essere mappato prima di tutti gli altri filtri.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## Aggiungere al progetto i moduli HTTP

Individuare e aprire il file ApplicationInsights.xml nel progetto e unire il seguente frammento di codice al di sotto dell'elemento <TelemetryModules>.

Se non esiste alcun elemento <TelemetryModules> in questo file, aggiungerne uno al di sotto dell'elemento <ApplicationInsights>.

    <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
    </TelemetryModules>

## Aggiungere gli inizializzatori di telemetria per la correlazione di eventi

Grazie alla correlazione di eventi, è possibile eseguire associazioni tra una richiesta HTTP e tutti gli eventi di telemetria inviati durante l'elaborazione delle richieste usando una proprietà di ID operazione associata a ognuno di questi eventi di telemetria. In tal modo è possibile esplorare una richiesta HTTP con tutti gli eventi chiamati da questa richiesta e viene facilitata la diagnosi e la risoluzione dei problemi.

Individuare e aprire il file ApplicationInsights.xml nel progetto e unire il seguente frammento di codice al di sotto dell'elemento <TelemetryInitializers>.

Se non esiste alcun elemento < TelemetryInitializers> in questo file, aggiungerne uno al di sotto dell'elemento <ApplicationInsights>.

    <TelemetryInitializers>
      <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    </TelemetryInitializers>


## Visualizzare le informazioni sulle richieste in Application Insights

Eseguire l'applicazione.

Tornare alla risorsa di Application Insights in Microsoft Azure.

Nel pannello Panoramica verranno visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![](./media/app-insights-java-track-http-requests/5-results.png)
 

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate.

![](./media/app-insights-java-track-http-requests/6-barchart.png)


[Altre informazioni sulle metriche.][metrics]

 

E quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.
 
![](./media/app-insights-java-track-http-requests/7-instance.png)




## Passaggi successivi

* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.
* [Acquisire le tracce di Log4J o Logback][javalogs]



<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


<!--HONumber=54-->