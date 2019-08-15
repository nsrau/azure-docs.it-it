---
title: Analisi di app Web Java con applicazione Azure Insights (2.5.0-BETA) | Microsoft Docs
description: 'Application Performance Monitoring per app Web Java con Application Insights (2.5.0-BETA). '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: lagayhar
ms.openlocfilehash: 17ad99d372bbca1d82c5c3701751da1b009764ee
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967834"
---
# <a name="get-started-with-application-insights-in-a-java-web-project-250-beta"></a>Introduzione a Application Insights in un progetto Web Java (2.5.0-BETA)

[Application Insights](https://azure.microsoft.com/services/application-insights/) è un servizio di analisi estendibile per gli sviluppatori Web che semplifica la comprensione delle prestazioni e dell'uso dell'applicazione live. È possibile usarlo per [instrumentare automaticamente le richieste, tenere traccia delle dipendenze e raccogliere i contatori delle prestazioni](auto-collect-dependencies.md#java), diagnosticare problemi di prestazioni ed eccezioni e [scrivere codice][api] per tenere traccia delle operazioni eseguite dagli utenti con l'app. 

![Screenshot dei dati di esempio della panoramica](./media/java-get-started/overview-graphs.png)

Application Insights supporta le app Java in esecuzione in Linux, Unix o Windows.

È necessario:

* Java 7 o versione successiva
* Una sottoscrizione a [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Ottenere una chiave di strumentazione di Application Insights
1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).
2. Creare una risorsa di Application Insights. Impostare il tipo di applicazione nell'applicazione Web Java.

3. Ottenere la chiave di strumentazione della nuova risorsa. Questa chiave dovrà a breve essere incollata nel progetto di codice.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Aggiungere SDK per Java di Application Insights al progetto
*Scegliere il modo più appropriato per il progetto.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Se si usa Maven: <a name="maven-setup" />
Se il progetto è già stato configurato per usare Maven per la compilazione, unire il codice seguente al file pom.xml.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0-BETA</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Se si usa Gradle: <a name="gradle-setup" />
Se il progetto è già stato configurato per usare Gradle per la compilazione, unire il codice seguente al file build.gradle.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0-BETA'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>In caso contrario, se le dipendenze vengono gestite manualmente:
Scaricare la [versione più recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copiare i file necessari nel progetto, sostituendo tutte le versioni precedenti.

### <a name="questions"></a>Domande...
* *Qual è la relazione tra i `-web-auto`componenti `-web` , `-core` e?*
  * `applicationinsights-web-auto`fornisce metriche che tengono traccia dei conteggi delle richieste di servlet HTTP e dei tempi di risposta, registrando automaticamente il filtro Application Insights servlet in fase di esecuzione.
  * `applicationinsights-web`fornisce anche metriche che tengono traccia dei conteggi delle richieste di servlet HTTP e dei tempi di risposta, ma richiede la registrazione manuale del filtro Application Insights servlet nell'applicazione.
  * `applicationinsights-core`fornisce solo l'API bare, ad esempio se l'applicazione non è basata su servlet.
  
* *Come si esegue l'aggiornamento alla versione più recente dell'SDK?*
  * Se si usa Gradle o Maven:
    * Aggiornare il file di compilazione per specificare la versione più recente.
  * Se le dipendenze vengono gestite manualmente:
    * Scaricare la versione più recente di [Application Insights SDK per Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e sostituire le versioni precedenti. Le modifiche sono descritte nelle [note sulla versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Aggiungere un file ApplicationInsights.xml
Aggiungere ApplicationInsights.xml alla cartella resources del progetto oppure verificare che sia stato aggiunto al percorso della classe di distribuzione del progetto. Copiarvi il seguente file XML.

Sostituire la chiave di strumentazione recuperata dal portale di Azure.

```XML
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
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Il file di configurazione può risiedere in qualsiasi percorso accessibile all'applicazione.  La proprietà di sistema `-Dapplicationinsights.configurationDirectory` specifica la directory contenente il file ApplicationInsights.xml. Ad esempio, un file di configurazione con percorso `E:\myconfigs\appinsights\ApplicationInsights.xml` verrà configurato con la proprietà `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* La chiave di strumentazione viene inviata insieme a tutti gli elementi di dati di telemetria e indica ad Application Insights di visualizzarla nella risorsa.
* Il componente delle richieste HTTP è facoltativo. Invia automaticamente i dati di telemetria sulle richieste e tempi di risposta al portale.
* La correlazione di eventi è un'aggiunta al componente delle richieste HTTP. Assegna un identificatore a ogni richiesta ricevuta dal server e lo aggiunge come proprietà a ogni elemento di telemetria, come proprietà "Operation.Id". Consente di correlare i dati di telemetria associati a ogni richiesta impostando un filtro in [Ricerca diagnostica][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Modi alternativi per impostare la chiave di strumentazione
Application Insights SDK cerca la chiave nell'ordine seguente.

1. Proprietà di sistema:-DAPPINSIGHTS_INSTRUMENTATIONKEY = iKey
2. Variabile di ambiente: APPINSIGHTS_INSTRUMENTATIONKEY
3. File di configurazione: ApplicationInsights.xml

È anche possibile eseguirne l' [impostazione nel codice](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Aggiungi agente

[Installare l'agente Java](java-agent-25-beta.md) per acquisire le chiamate http in uscita, le query JDBC, la registrazione delle applicazioni e una migliore denominazione delle operazioni.

## <a name="5-run-your-application"></a>5. Eseguire l'applicazione
Eseguire l'applicazione in modalità debug nel computer di distribuzione oppure pubblicarla nel server.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Visualizzare i dati di telemetria in Application Insights
Tornare alla risorsa di Application Insights nel [portale di Microsoft Azure](https://portal.azure.com).

Nel pannello di panoramica vengono visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![Screenshot dei dati di esempio della panoramica](./media/java-get-started/overview-graphs.png)

[Altre informazioni sulle metriche.][metrics]

Fare clic su qualsiasi grafico per visualizzare metriche aggregate più dettagliate.

![Riquadro Errori Application Insights con grafici](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0-BETA operation naming provided by agent]
-->

### <a name="instance-data"></a>Dati dell'istanza
Fare clic su un tipo di richiesta specifico per visualizzare le singole istanze.

![Esaminare una visualizzazione di esempio specifica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analisi: Linguaggio di query avanzato
Quando si accumulano molti dati, è possibile eseguire query per aggregare i dati e per individuare istanze singole.  [Analisi](../../azure-monitor/app/analytics.md) è uno strumento avanzato per ottenere informazioni sulle prestazioni e sull'utilizzo e ai fini della diagnostica.

![Esempio di Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installare l'applicazione nel server
A questo punto è possibile pubblicare l'applicazione nel server, permettere agli utenti di utilizzarla e visualizzare la telemetria mostrata nel portale.

* Verificare che il firewall consenta all'applicazione di inviare i dati di telemetria a queste porte:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se il traffico in uscita deve essere instradato attraverso un firewall, definire le proprietà di sistema `http.proxyHost` e `http.proxyPort`.

* Nei server Windows installare:

  * [Microsoft Visual C++ Redistributable Package](https://www.microsoft.com/download/details.aspx?id=40784)

    Questo componente abilita i contatori delle prestazioni.

## <a name="azure-app-service-config-spring-boot"></a>Configurazione del servizio app Azure (Spring boot)

Le app Spring boot eseguite in Windows richiedono una configurazione aggiuntiva per l'esecuzione nei servizi app Azure. Modificare **Web. config** e aggiungere quanto segue:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Eccezioni e richieste non eseguite
Le eccezioni non gestite e gli errori delle richieste vengono raccolti automaticamente dal filtro Application Insights Web.

Per raccogliere dati in altre eccezioni, è possibile [inserire chiamate a trackexception () nel codice][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorare le chiamate al metodo e le dipendenze esterne
[Installare l'agente Java](java-agent.md) per registrare i metodi interni specificati e le chiamate effettuate tramite JDBC, con i dati relativi alle durate.

E per la denominazione automatica delle operazioni.

## <a name="w3c-distributed-tracing"></a>Analisi distribuita W3C

L'SDK di Java di Application Insights adesso supporta l'[analisi distribuita W3C](https://w3c.github.io/trace-context/).

La configurazione dell'SDK in ingresso viene illustrata più dettagliatamente nell'articolo sulla [correlazione](correlation.md#w3c-distributed-tracing).

La configurazione dell'SDK in uscita è definita nel file [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Contatori delle prestazioni
Aprire **esamina**, **metriche**, per visualizzare un intervallo di contatori delle prestazioni.

![Screenshot del riquadro metriche con i byte privati di processo selezionati](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizzare la raccolta del contatore delle prestazioni
Per disabilitare la raccolta del set standard di contatori delle prestazioni, aggiungere il seguente codice al di sotto del nodo principale del file ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Raccogliere dei contatori di prestazioni aggiuntive
È possibile specificare altri contatori di prestazioni da raccogliere.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Contatori JMX (esposti da Java Virtual Machine)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` : il nome visualizzato nel portale di Application Insights.
* `objectName` : il nome dell'oggetto JMX.
* `attribute` : l'attributo del nome dell'oggetto JMX da recuperare
* `type` (facoltativo): il tipo di attributo dell'oggetto JMX:
  * Impostazione predefinita: un tipo semplice come int o long.
  * `composite`: i dati del contatore delle prestazioni sono nel formato 'Attribute.Data'
  * `tabular`: i dati del contatore delle prestazioni sono nel formato della riga di una tabella

#### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows
Ogni [contatore delle prestazioni Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) è un membro di una categoria (nello stesso modo in cui un campo è un membro di una classe). Le categorie possono essere globali o possono disporre di istanze numerate o denominate.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName: il nome visualizzato nel portale di Application Insights.
* categoryName: la categoria del contatore delle prestazioni (oggetto prestazioni) a cui è associato questo contatore delle prestazioni.
* counterName: il nome del contatore delle prestazioni.
* instanceName: il nome dell'istanza di categoria del contatore delle prestazioni o una stringa vuota (""), se la categoria contiene una singola istanza. Se categoryName è il processo e il contatore delle prestazioni di cui raccogliere i dati proviene dal processo JVM corrente su cui è in esecuzione l'app, specificare `"__SELF__"`.

### <a name="unix-performance-counters"></a>Contatori delle prestazioni Unix
* [Installare collectd con il plug-in di Application Insights](java-collectd.md) per ottenere una vasta gamma di dati di sistema e di rete.

## <a name="get-user-and-session-data"></a>Ottenere i dati relativi a utenti e sessioni
I dati di telemetria vengono normalmente inviati dal server Web. Per un quadro completo a 360 gradi dell'applicazione, è però possibile aggiungere altre funzionalità di monitoraggio:

* [Aggiungere la telemetria alle pagine Web][usage] per monitorare le visualizzazioni pagina e le metriche utente.
* [Configurare i test Web][availability] in modo da assicurarsi che l'applicazione sia disponibile e reattiva.

## <a name="send-your-own-telemetry"></a>Inviare i propri dati di telemetria
Ora che è stato installato SDK, è possibile usare l'API per inviare i propri dati di telemetria.

* [Tenere traccia di eventi e metriche personalizzati][api] per informazioni sulle operazioni svolte dagli utenti con l'applicazione.
* [Cercare eventi e log][diagnostic] per facilitare la diagnosi dei problemi.

## <a name="availability-web-tests"></a>Test Web di disponibilità
Application Insights può testare il sito Web a intervalli regolari per verificare che funzioni e risponda correttamente.

[Altre informazioni su come configurare i test Web di disponibilità.][availability]

## <a name="questions-problems"></a>Domande? Problemi?
[Risoluzione dei problemi Java](java-troubleshoot.md)

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare chiamate alle dipendenze](java-agent.md)
* [Monitorare contatori delle prestazioni Unix](java-collectd.md)
* Aggiungere il [monitoraggio alle pagine Web](javascript.md) per monitorare i tempi di caricamento delle pagine, le chiamate AJAX e le eccezioni del browser.
* Scrivere [dati di telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md) per tenere traccia dell'uso nel browser o nel server.
* Usare [Analytics](../../azure-monitor/app/analytics.md) per eseguire query avanzate sui dati di telemetria dall'app
* Per altre informazioni, vedere [Azure for Java developers](/java/azure) (Azure per sviluppatori Java).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs-25-beta.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
