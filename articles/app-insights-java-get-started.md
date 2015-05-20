<properties 
	pageTitle="Introduzione ad Application Insights in un progetto Web Java" 
	description="Monitoraggio delle prestazioni e l'uso del sito Web Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
# Introduzione ad Application Insights in un progetto Web Java

*Application Insights è disponibile in Anteprima.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Mediante l'aggiunta di Application Insights per Visual Studio al progetto, è possibile rilevare e diagnosticare i problemi di prestazioni e le eccezioni.


![dati di esempio](./media/app-insights-java-track-http-requests/5-results.png)

È possibile inoltre impostare i [test Web][availability] per monitorare la disponibilità dell'applicazione e inserire [codice nelle pagine Web][track] per comprendere i modelli di uso.

Sono necessari gli elementi seguenti:

* Oracle JRE 1.6 o versione successiva o Zulu JRE 1.6 o versione successiva
* Una sottoscrizione a [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).


## 1\. Ottenere una chiave di strumentazione di Application Insights

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com)
2. Creare una nuova risorsa di Application Insights

    ![Fare clic su + e scegliere Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Impostare il tipo di applicazione nell'applicazione Web Java.

    ![Inserire un nome, scegliere l'app Web Java e fare clic su Crea](./media/app-insights-java-get-started/02-create.png)
4. Ottenere la chiave di strumentazione della nuova risorsa. Dopo poco sarà necessario incollarla in questo progetto del codice.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-get-started/03-key.png)

## 2\. Aggiungere SDK per Java di Application Insights al progetto

*Scegliere il modo più appropriato per il progetto.*

#### Se si sta creando un progetto Web dinamico in Eclipse...

Usare il [plug-in di Application Insights SDK per Java][eclipse].

#### Se si usa Maven...

Se il progetto è già stato configurato per usare Maven per la compilazione, unire il frammento di codice seguente al file pom.xml.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[0.9,)</version>
      </dependency>
    </dependencies>


* *Errori di convalida di compilazione o checksum? Provare a usare una versione specifica:* `<version>0.9.3</version>`

#### Se si usa Gradle...

Se il progetto è già stato configurato per usare Gradle per la compilazione, unire il frammento di codice seguente al file build.gradle.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
      // or applicationinsights-core for bare API
    }

* *Errori di convalida di compilazione o checksum? Provare a usare una versione specifica:* `version:'0.9.3'`

#### In caso contrario...

Aggiungere manualmente SDK:

1. Scaricare le [librerie di Azure per Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html)
2. Estrarre i file binari seguenti dal file ZIP e aggiungerli al progetto:
 * applicationinsights-core
 * applicationinsights-web
 * commons-codec
 * commons-io
 * commons-lang
 * commons-logging
 * guava
 * httpclient
 * httpcore
 * jsr305


*Qual è la relazione tra i componenti `-core` e `-web`?*

`applicationinsights-core` fornisce l'API con telemetria non automatica. `applicationinsights-web` fornisce le metriche rilevando i conteggi delle richieste HTTP e i tempi di risposta.


## 3\. Aggiungere un file XML di Application Insights

Aggiungere il file ApplicationInsights.xml alla cartella resources del progetto. Copiarvi il seguente file XML.

Sostituire la chiave di strumentazione recuperata dal portale di Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* La chiave di strumentazione viene inviata insieme a tutti gli elementi di dati di telemetria e indica ad Application Insights di visualizzarla nella risorsa.
* Il componente delle richieste HTTP è facoltativo. Invia automaticamente i dati di telemetria sulle richieste e tempi di risposta al portale.
* La correlazione di eventi è un'aggiunta al componente delle richieste HTTP. Assegna un identificatore a ogni richiesta ricevuta dal server e lo aggiunge come proprietà per ogni elemento di dati di telemetria come la proprietà 'Operation.Id'. Consente di correlare i dati di telemetria associati a ogni richiesta impostando un filtro in [Ricerca diagnostica][diagnostic].

## 4\. Aggiungere un filtro HTTP

L'ultimo passaggio di configurazione consente al componente delle richieste HTTP di registrare ogni richiesta Web. Non necessario se si desidera l'API.

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

#### Se si usa MVC 3.1 o versione successiva

Modificare questi elementi per includere il pacchetto di Application Insights:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Se si usa Struts 2

Aggiungere questa voce al file di configurazione Struts \(in genere denominato struts.xml o struts-default.xml\):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

Se si dispone di intercettori definiti in uno stack predefinito, l'intercettore può semplicemente essere aggiunto a tale stack.

## 5\. Visualizzare i dati di telemetria in Application Insights

Eseguire l'applicazione.

Tornare alla risorsa di Application Insights in Microsoft Azure.

Nel pannello Panoramica verranno visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![dati di esempio](./media/app-insights-java-track-http-requests/5-results.png)
 

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate.

![](./media/app-insights-java-track-http-requests/6-barchart.png)

 

E quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.
 
![](./media/app-insights-java-track-http-requests/7-instance.png)



[Altre informazioni sulle metriche.][metrics]

#### Calcolo automatico del nome dell'indirizzo

Application Insights presuppone che il formato delle richieste HTTP per le applicazioni MVC sia: `VERB controller/action`


Ad esempio, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` verranno raggruppati in `GET Home/Product`.

In questo modo le aggregazioni significative delle richieste, ad esempio il numero di richieste e il tempo medio di esecuzione per le richieste.

## 5\. Contatori delle prestazioni

Fare clic sul riquadro Server e verrà visualizzato un intervallo di contatori delle prestazioni.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Personalizzazione della raccolta di contatori delle prestazioni

Per disabilitare la raccolta del set standard di contatori delle prestazioni, aggiungere il seguente frammento al di sotto del nodo principale del file ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Raccolta dei contatori di prestazioni aggiuntive

È possibile specificare altri contatori di prestazioni da raccogliere.

#### Contatori JMX \(esposti da Java Virtual Machine\)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName`: il nome visualizzato nel portale di Application Insights.
*	`objectName`: il nome dell'oggetto JMX.
*	`attribute`: l'attributo del nome dell'oggetto JMX da recuperare
*	`type` \(facoltativo\): il tipo di attributo dell'oggetto JMX:
 *	Impostazione predefinita: un tipo semplice come int o long.
 *	`composite`: i dati del contatore delle prestazioni sono nel formato 'Attribute.Data'
 *	`tabular`: i dati del contatore delle prestazioni sono nel formato della riga di una tabella



#### Contatori delle prestazioni Windows \(a 64 bit\) 

Ogni [contatore delle prestazioni Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) è un membro di una categoria \(nello stesso modo in cui un campo è un membro di una classe\). Le categorie possono essere globali o possono disporre di istanze numerate o denominate.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName: il nome visualizzato nel portale di Application Insights
*	categoryName: la categoria del contatore delle prestazioni \(oggetto prestazioni\) a cui è associato questo contatore delle prestazioni
*	counterName: il nome del contatore delle prestazioni
*	instanceName: il nome dell'istanza di categoria del contatore delle prestazioni o una stringa vuota \(""\), se la categoria contiene una singola istanza. Se categoryName è il processo e il contatore delle prestazioni di cui raccogliere i dati proviene dal processo JVM corrente su cui è in esecuzione l'app, specificare `"__SELF__"`.

I contatori delle prestazioni sono visibili come metriche personalizzate in [Esplora metriche][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


## 6\. Acquisire le tracce dei log

È possibile usare Application Insights per analizzare approfonditamente i log Log4J, Logback o altri framework di registrazione. È possibile correlare i log con le richieste HTTP e altri dati di telemetria. [Informazioni][javalogs].

## 7\. Inviare i propri dati di telemetria

Ora che è stato installato SDK, è possibile usare l'API per inviare i propri dati di telemetria.

* [Tenere traccia di eventi personalizzati e metriche][track] per informazioni sulle attività svolte dagli utenti con l'applicazione.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.


Inoltre, è possibile offrire maggiori funzionalità di Application Insights all'applicazione:

* [Aggiungere dati di telemetria al client Web][usage] per monitorare le visualizzazioni pagina e le metriche utente di base.
* [Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.


## Domande? Problemi?

[Risoluzione dei problemi Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->