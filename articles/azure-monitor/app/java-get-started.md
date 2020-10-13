---
title: 'Guida introduttiva: analisi di app Web Java con applicazione Azure Insights'
description: 'Application Performance Monitoring per app Web Java con Application Insights. '
ms.topic: conceptual
author: lgayhardt
ms.custom: devx-track-java
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: f0583af05ae7d8e365b50610bfb812ac7764f223
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602466"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Guida introduttiva: Introduzione a Application Insights in un progetto Web Java


> [!IMPORTANT]
> L'approccio consigliato per il monitoraggio delle applicazioni Java consiste nell'usare la strumentazione automatica senza modificare il codice. Seguire le linee guida per [Application Insights agente Java 3,0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

In questa Guida introduttiva si usa Application Insights SDK per instrumentare la richiesta, tenere traccia delle dipendenze e raccogliere i contatori delle prestazioni, diagnosticare i problemi di prestazioni e le eccezioni e scrivere codice per tenere traccia delle operazioni eseguite dagli utenti con l'app.

Application Insights è un servizio di analisi estendibile per gli sviluppatori Web che semplifica la comprensione delle prestazioni e dell'uso dell'applicazione live. Application Insights supporta le app Java in esecuzione in Linux, Unix o Windows.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un'applicazione Java funzionante.

## <a name="get-an-application-insights-instrumentation-key"></a>Ottenere una chiave di strumentazione di Application Insights

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel portale di Azurecreare una risorsa di Application Insights. Impostare il tipo di applicazione nell'applicazione Web Java.

3. Ottenere la chiave di strumentazione della nuova risorsa. Questa chiave dovrà a breve essere incollata nel progetto di codice.

    ![Nella panoramica della nuova risorsa, fare clic su Proprietà e copiare la chiave di strumentazione](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Aggiungere SDK per Java di Application Insights al progetto

*Scegliere il tipo di progetto.*

# <a name="maven"></a>[Maven](#tab/maven)

Se il progetto è già configurato per usare Maven per la compilazione, unire il codice seguente al file di *pom.xml* .

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Se il progetto è già configurato per usare Gradle per la compilazione, unire il codice seguente al file *Build. Gradle* .

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Altri tipi](#tab/other)

Scaricare la [versione più recente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e copiare i file necessari nel progetto, sostituendo tutte le versioni precedenti.

---

### <a name="questions"></a>Domande
* *Qual è la relazione tra i `-web-auto` `-web` componenti, e `-core` ?*
  * `applicationinsights-web-auto` fornisce metriche che tengono traccia dei conteggi delle richieste di servlet HTTP e dei tempi di risposta, registrando automaticamente il filtro Application Insights servlet in fase di esecuzione.
  * `applicationinsights-web` fornisce anche metriche che tengono traccia dei conteggi delle richieste di servlet HTTP e dei tempi di risposta, ma richiede la registrazione manuale del filtro Application Insights servlet nell'applicazione.
  * `applicationinsights-core` fornisce solo l'API bare, ad esempio se l'applicazione non è basata su servlet.
  
* *Come si esegue l'aggiornamento alla versione più recente dell'SDK?*
  * Se si usa Gradle o Maven...
    * Aggiornare il file di compilazione per specificare la versione più recente.
  * Se le dipendenze vengono gestite manualmente...
    * Scaricare la versione più recente di [Application Insights SDK per Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) e sostituire le versioni precedenti. Le modifiche sono descritte nelle [note sulla versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="add-an-applicationinsightsxml-file"></a>Aggiungere un file di *ApplicationInsights.xml*
Aggiungere *ApplicationInsights.xml* alla cartella risorse nel progetto o assicurarsi che sia aggiunto al percorso della classe di distribuzione del progetto. Copiarvi il seguente file XML.

Sostituire la chiave di strumentazione con quella ottenuta dal portale di Azure.

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

Facoltativamente, il file di configurazione può trovarsi in qualsiasi posizione accessibile all'applicazione.  La proprietà `-Dapplicationinsights.configurationDirectory` di sistema specifica la directory che contiene *ApplicationInsights.xml*. Ad esempio, un file di configurazione con percorso `E:\myconfigs\appinsights\ApplicationInsights.xml` verrà configurato con la proprietà `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* La chiave di strumentazione viene inviata insieme a tutti gli elementi di dati di telemetria e indica ad Application Insights di visualizzarla nella risorsa.
* Il componente delle richieste HTTP è facoltativo. Invia automaticamente i dati di telemetria sulle richieste e tempi di risposta al portale.
* La correlazione di eventi è un'aggiunta al componente delle richieste HTTP. Assegna un identificatore a ogni richiesta ricevuta dal server. Aggiunge quindi questo identificatore come proprietà a ogni elemento di dati di telemetria come proprietà' Operation.Id '. Consente di correlare i dati di telemetria associati a ogni richiesta impostando un filtro in [Ricerca diagnostica][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Modi alternativi per impostare la chiave di strumentazione
Application Insights SDK cerca la chiave nell'ordine seguente.

1. Proprietà di sistema:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Variabile di ambiente: APPINSIGHTS_INSTRUMENTATIONKEY
3. File di configurazione: *ApplicationInsights.xml*

È anche possibile eseguirne l' [impostazione nel codice](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Aggiungi agente

[Installare l'agente Java](java-agent.md) per acquisire le chiamate http in uscita, le query JDBC, la registrazione delle applicazioni e una migliore denominazione delle operazioni.

## <a name="run-your-application"></a>Eseguire l'applicazione
Eseguire l'applicazione in modalità debug nel computer di distribuzione oppure pubblicarla nel server.

## <a name="view-your-telemetry-in-application-insights"></a>Visualizzare i dati di telemetria in Application Insights
Tornare alla risorsa di Application Insights nel [portale di Microsoft Azure](https://portal.azure.com).

Nel pannello di panoramica vengono visualizzati i dati delle richieste HTTP. Se non sono visualizzati, attendere alcuni secondi e quindi fare clic su Aggiorna.

![Screenshot dei dati di esempio della panoramica](./media/java-get-started/overview-graphs.png)

[Altre informazioni sulle metriche.][metrics]

Fare clic su qualsiasi grafico per visualizzare metriche aggregate più dettagliate.

![Riquadro Errori Application Insights con grafici](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Dati dell'istanza
Fare clic su un tipo di richiesta specifico per visualizzare le singole istanze.

![Esaminare una visualizzazione di esempio specifica](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: linguaggio di query avanzato
Quando si accumulano molti dati, è possibile eseguire query per aggregare i dati e per individuare istanze singole.  [Analisi](../log-query/log-query-overview.md) è uno strumento avanzato per ottenere informazioni sulle prestazioni e sull'utilizzo e ai fini della diagnostica.

![Esempio di Analytics](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Installare l'applicazione nel server
A questo punto è possibile pubblicare l'applicazione nel server, permettere agli utenti di utilizzarla e visualizzare la telemetria mostrata nel portale.

* Verificare che il firewall consenta all'applicazione di inviare i dati di telemetria a queste porte:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Se il traffico in uscita deve essere instradato attraverso un firewall, definire le proprietà di sistema `http.proxyHost` e `http.proxyPort`.

* Nei server Windows installare:

  * [Microsoft Visual C++ Redistributable Package](https://www.microsoft.com/download/details.aspx?id=40784)

    Questo componente abilita i contatori delle prestazioni.

## <a name="azure-app-service-aks-vms-config"></a>Servizio app Azure, AKS, configurazione VM

L'approccio migliore e più semplice per monitorare le applicazioni in esecuzione in uno dei provider di risorse di Azure consiste nell'usare Application Insights la strumentazione automatica tramite [Java 3,0 Agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).


## <a name="exceptions-and-request-failures"></a>Eccezioni e richieste non eseguite
Le eccezioni non gestite e gli errori delle richieste vengono raccolti automaticamente dal filtro Application Insights Web.

Per raccogliere dati in altre eccezioni, è possibile [inserire chiamate a trackexception () nel codice][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorare le chiamate al metodo e le dipendenze esterne
[Installare l'agente Java](java-agent.md) per registrare i metodi interni specificati e le chiamate effettuate tramite JDBC, con i dati relativi alle durate.

E per la denominazione automatica delle operazioni.

## <a name="w3c-distributed-tracing"></a>Analisi distribuita W3C

L'SDK di Java di Application Insights adesso supporta l'[analisi distribuita W3C](https://w3c.github.io/trace-context/).

La configurazione dell'SDK in ingresso viene illustrata più dettagliatamente nell'articolo sulla [correlazione](correlation.md).

La configurazione dell'SDK in uscita è definita nel file [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Contatori delle prestazioni
Aprire **esamina**, **metriche**, per visualizzare un intervallo di contatori delle prestazioni.

![Screenshot del riquadro metriche con i byte privati di processo selezionati](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personalizzare la raccolta del contatore delle prestazioni
Per disabilitare la raccolta del set standard di contatori delle prestazioni, aggiungere il codice seguente nel nodo radice del file di *ApplicationInsights.xml* :

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
* `type` (facoltativo): tipo di attributo dell'oggetto JMX:
  * Impostazione predefinita: un tipo semplice come int o long.
  * `composite`: i dati del contatore delle prestazioni sono nel formato 'Attribute.Data'
  * `tabular`: i dati del contatore delle prestazioni sono nel formato della riga di una tabella

#### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows
Ogni [contatore delle prestazioni Windows](/windows/win32/perfctrs/performance-counters-portal) è un membro di una categoria (nello stesso modo in cui un campo è un membro di una classe). Le categorie possono essere globali o possono disporre di istanze numerate o denominate.

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

* Per informazioni sulle attività svolte dagli utenti con l'applicazione, [tenere traccia di eventi e metriche personalizzate][api].
* Per facilitare la diagnosi dei problemi, [cercare eventi e log][diagnostic].

## <a name="availability-web-tests"></a>Test Web di disponibilità
Application Insights può testare il sito Web a intervalli regolari per verificare che funzioni e risponda correttamente.

[Altre informazioni su come configurare i test Web di disponibilità.][availability]

## <a name="questions-problems"></a>Domande? Problemi?
[Risoluzione dei problemi Java](java-troubleshoot.md)

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare chiamate alle dipendenze](java-agent.md)
* [Monitorare contatori delle prestazioni Unix](java-collectd.md)
* Aggiungere il [monitoraggio alle pagine Web](javascript.md) per monitorare i tempi di caricamento delle pagine, le chiamate AJAX e le eccezioni del browser.
* Scrivere [dati di telemetria personalizzati](./api-custom-events-metrics.md) per tenere traccia dell'uso nel browser o nel server.
* Usare  [Analytics](../log-query/log-query-overview.md) per eseguire query avanzate sui dati di telemetria dall'app
* Per altre informazioni, vedere [Azure for Java developers](/java/azure) (Azure per sviluppatori Java).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md
[usage]: javascript.md

