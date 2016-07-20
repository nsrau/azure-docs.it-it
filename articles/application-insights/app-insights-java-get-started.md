<properties
	pageTitle="Analisi di app Web Java con Application Insights | Microsoft Azure"
	description="Monitorare le prestazioni e l'uso del sito Web Java con Application Insights. "
	services="application-insights"
    documentationCenter="java"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/12/2016"
	ms.author="awills"/>

# Introduzione ad Application Insights in un progetto Web Java

*Application Insights è disponibile in Anteprima.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) è un servizio di analisi estendibile che semplifica la comprensione delle prestazioni e dell'utilizzo dell'applicazione live. È possibile usarlo per [rilevare e diagnosticare i problemi di prestazioni e le eccezioni](app-insights-detect-triage-diagnose.md) e per [scrivere il codice][api]per rilevare l'interazione degli utenti con l'app.

![dati di esempio](./media/app-insights-java-get-started/5-results.png)

Application Insights supporta le app Java in esecuzione in Linux, Unix o Windows.

Sono necessari gli elementi seguenti:

* Oracle JRE 1.6 o versione successiva o Zulu JRE 1.6 o versione successiva
* Una sottoscrizione a [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

*Se è disponibile un'app Web già attiva, è possibile seguire la procedura alternativa per [aggiungere l'SDK al server Web in fase di esecuzione](app-insights-java-live.md). Tale alternativa evita di ricompilare il codice, ma non si ottiene l'opzione per scrivere codice per tenere traccia delle attività dell'utente.*


## 1\. Ottenere una chiave di strumentazione di Application Insights

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).
2. Creare una nuova risorsa di Application Insights.

    ![Fare clic su + e scegliere Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Impostare il tipo di applicazione nell'applicazione Web Java.

    ![Inserire un nome, scegliere l'app Web Java e fare clic su Crea](./media/app-insights-java-get-started/02-create.png)
4. Ottenere la chiave di strumentazione della nuova risorsa. Dopo poco sarà necessario incollarla in questo progetto del codice.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/app-insights-java-get-started/03-key.png)

## 2\. Aggiungere SDK per Java di Application Insights al progetto

*Scegliere il modo più appropriato per il progetto.*

#### Se si usa Eclipse per creare un progetto Web dinamico o Maven:

Usare il [plug-in di Application Insights SDK per Java][eclipse].

#### Se si usa Maven...

Se il progetto è già stato configurato per usare Maven per la compilazione, unire il codice seguente al file pom.xml.

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
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *Errori di convalida checksum o compilazione?* Provare a usare una versione specifica, ad esempio `<version>1.0.n</version>`. La versione più recente è disponibile nelle [note sulla versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) o negli [elementi Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *È necessario eseguire l'aggiornamento a un nuovo SDK?* Aggiornare le dipendenze del progetto.

#### Se si usa Gradle...

Se il progetto è già stato configurato per usare Gradle per la compilazione, unire il codice seguente al file build.gradle.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *Errori di convalida checksum o compilazione? Provare a usare una versione specifica, come* `version:'1.0.n'`. *La versione più recente è disponibile nelle [note sulla versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).*
* *Per eseguire l'aggiornamento a un nuovo SDK*
 * Aggiornare le dipendenze del progetto.

#### In caso contrario...

Aggiungere manualmente SDK:

1. Scaricare [Application Insights SDK per Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html).
2. Estrarre i file binari dal file ZIP e aggiungerli al progetto.

### Domande...

* *Qual è la relazione tra i componenti `-core` e `-web` nel file ZIP?*

 * `applicationinsights-core` fornisce semplicemente l'API. Sarà sempre necessario.
 * `applicationinsights-web` fornisce le metriche che consentono di tenere traccia del numero e dei tempi di risposta delle richieste HTTP. È possibile escluderlo se non si vuole che questi dati di telemetria vengano raccolti automaticamente. Ad esempio se si preferisce scrivere dati personalizzati.

* *Per aggiornare il SDK, quando si pubblicano le modifiche*
 * Scaricare la versione più recente di [Application Insights SDK per Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.zip) e sostituire le versioni precedenti.
 * Le modifiche sono descritte nelle [note sulla versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).



## 3\. Aggiungere un file XML di Application Insights

Aggiungere ApplicationInsights.xml alla cartella resources del progetto oppure verificare che sia stato aggiunto al percorso della classe di distribuzione del progetto. Copiarvi il seguente file XML.

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

### Modi alternativi per impostare la chiave di strumentazione

Application Insights SDK cerca la chiave nell'ordine seguente.

1. Proprietà di sistema: -DAPPLICATION\_INSIGHTS\_IKEY=ikey
2. Variabile di ambiente: APPLICATION\_INSIGHTS\_IKEY
3. File di configurazione: ApplicationInsights.xml

È anche possibile eseguirne l'[impostazione nel codice](app-insights-api-custom-events-metrics.md#ikey):

    telemetryClient.InstrumentationKey = "...";


## 4\. Aggiungere un filtro HTTP

L'ultimo passaggio di configurazione consente al componente delle richieste HTTP di registrare ogni richiesta Web. Non necessario se si desidera l'API.

Individuare e aprire il file web.xml nel progetto e unire il codice seguente al di sotto del nodo app-web, in cui sono configurati i filtri dell'applicazione.

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

Aggiungere questa voce al file di configurazione Struts (in genere denominato struts.xml o struts-default.xml):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

Se si dispone di intercettori definiti in uno stack predefinito, l'intercettore può semplicemente essere aggiunto a tale stack.



## 5\. Eseguire l'applicazione

Eseguire l'applicazione in modalità debug nel computer di distribuzione oppure pubblicarla nel server.

## 6\. Visualizzare i dati di telemetria in Application Insights


Tornare alla risorsa di Application Insights nel [portale di Microsoft Azure](https://portal.azure.com).

Nel pannello Panoramica verranno visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![dati di esempio](./media/app-insights-java-get-started/5-results.png)

[Altre informazioni sulle metriche.][metrics]

Fare clic su qualsiasi grafico per visualizzare metriche aggregate più dettagliate.

![](./media/app-insights-java-get-started/6-barchart.png)

> Application Insights presuppone che il formato delle richieste HTTP per le applicazioni MVC sia `VERB controller/action`. Ad esempio, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` verranno raggruppati in `GET Home/Product`. In questo modo le aggregazioni significative delle richieste, ad esempio il numero di richieste e il tempo medio di esecuzione per le richieste.


### Dati dell'istanza 

Fare clic su un tipo di richiesta specifico per visualizzare le singole istanze.

In Application Insights vengono visualizzati due tipi di dati, ovvero i dati aggregati, archiviati e visualizzati come medie, conteggi e somme, e i dati di istanza, ovvero singoli report di richieste HTTP, eccezioni, visualizzazioni di pagina o eventi personalizzati.

Quando si visualizzano le proprietà di una richiesta, è possibile visualizzare gli eventi di telemetria associati, ad esempio le richieste e le eccezioni.

![](./media/app-insights-java-get-started/7-instance.png)


### Analytics: linguaggio di query avanzato

Quando si accumulano molti dati, è possibile eseguire query per aggregare i dati e per individuare istanze singole. [Analytics]() è uno strumento avanzato per ottenere informazioni sulle prestazioni e sull'utilizzo e ai fini della diagnostica.

![Esempio di Analytics](./media/app-insights-java-get-started/025.png)


## 7\. Installare l'applicazione nel server

A questo punto è possibile pubblicare l'applicazione nel server, permettere agli utenti di utilizzarla e visualizzare la telemetria mostrata nel portale.

* Verificare che il firewall consenta all'applicazione di inviare i dati di telemetria a queste porte:

 * dc.services.visualstudio.com:443
 * f5.services.visualstudio.com:443


* Nei server Windows installare:

 * [Microsoft Visual C++ Redistributable Package](http://www.microsoft.com/download/details.aspx?id=40784)

    (Ciò abilita i contatori delle prestazioni).

## Eccezioni e richieste non eseguite

Vengono raccolte automaticamente le eccezioni non gestite:

![Scorrere verso il basso e fare clic sul riquadro Errori](./media/app-insights-java-get-started/21-exceptions.png)

Per raccogliere dati su altre eccezioni, sono disponibili due opzioni:

* [Inserire chiamate a trackException() nel codice][apiexceptions].
* [Installare l'agente Java nel server](app-insights-java-agent.md). È possibile specificare i metodi da controllare.


## Monitorare le chiamate al metodo e le dipendenze esterne

[Installare l'agente Java](app-insights-java-agent.md) per registrare i metodi interni specificati e le chiamate effettuate tramite JDBC, con i dati relativi alle durate.


## Contatori delle prestazioni

Fare clic sul riquadro **Server** e verrà visualizzato un intervallo di contatori delle prestazioni.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Personalizzare la raccolta del contatore delle prestazioni

Per disabilitare la raccolta del set standard di contatori delle prestazioni, aggiungere il seguente codice al di sotto del nodo principale del file ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Raccogliere dei contatori di prestazioni aggiuntive

È possibile specificare altri contatori di prestazioni da raccogliere.

#### Contatori JMX (esposti da Java Virtual Machine)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName`: il nome visualizzato nel portale di Application Insights.
*	`objectName`: il nome dell'oggetto JMX.
*	`attribute`: l'attributo del nome dell'oggetto JMX da recuperare
*	`type` (facoltativo): il tipo di attributo dell'oggetto JMX:
 *	Impostazione predefinita: un tipo semplice come int o long.
 *	`composite`: i dati del contatore delle prestazioni sono nel formato 'Attribute.Data'
 *	`tabular`: i dati del contatore delle prestazioni sono nel formato della riga di una tabella



#### Contatori delle prestazioni di Windows

Ogni [contatore delle prestazioni Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) è un membro di una categoria (nello stesso modo in cui un campo è un membro di una classe). Le categorie possono essere globali o possono disporre di istanze numerate o denominate.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName: il nome visualizzato nel portale di Application Insights.
*	categoryName: la categoria del contatore delle prestazioni (oggetto prestazioni) a cui è associato questo contatore delle prestazioni.
*	counterName: il nome del contatore delle prestazioni.
*	instanceName: il nome dell'istanza di categoria del contatore delle prestazioni o una stringa vuota (""), se la categoria contiene una singola istanza. Se categoryName è il processo e il contatore delle prestazioni di cui raccogliere i dati proviene dal processo JVM corrente su cui è in esecuzione l'app, specificare `"__SELF__"`.

I contatori delle prestazioni sono visibili come metriche personalizzate in [Esplora metriche][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Contatori delle prestazioni Unix

* [Installare collectd con il plug-in di Application Insights](app-insights-java-collectd.md) per ottenere una vasta gamma di dati di sistema e di rete.

## Ottenere i dati relativi a utenti e sessioni

I dati di telemetria vengono normalmente inviati dal server Web. Per un quadro completo a 360 gradi dell'applicazione, è però possibile aggiungere altre funzionalità di monitoraggio:

* [Aggiungere la telemetria alle pagine Web][usage] per monitorare le visualizzazioni pagina e le metriche utente.
* [Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.

## Acquisire le tracce dei log

È possibile usare Application Insights per analizzare approfonditamente i log Log4J, Logback o altri framework di registrazione. È possibile correlare i log con le richieste HTTP e altri dati di telemetria. [Informazioni][javalogs].

## Inviare i propri dati di telemetria

Ora che è stato installato SDK, è possibile usare l'API per inviare i propri dati di telemetria.

* [Tenere traccia di eventi personalizzati e metriche][api] per informazioni sulle attività svolte dagli utenti con l'applicazione.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.


## Test Web di disponibilità

Application Insights può testare il sito Web a intervalli regolari per verificare che funzioni e risponda correttamente. [Per eseguire la configurazione][availability], scorrere verso il basso e fare clic su Disponibilità.

![Scorrere verso il basso, fare clic su Disponibilità, quindi su Aggiungi test Web](./media/app-insights-java-get-started/31-config-web-test.png)

Se il sito è inattivo, si otterranno grafici dei tempi di risposta, nonché notifiche di posta elettronica.

![Esempio di test Web](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Altre informazioni sui test Web di disponibilità.][availability]






## Domande? Problemi?

[Risoluzione dei problemi Java](app-insights-java-troubleshoot.md)

## Passaggi successivi

Per altre informazioni, vedere il [Centro per sviluppatori Java](/develop/java/).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0713_2016-->