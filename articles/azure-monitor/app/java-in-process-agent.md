---
title: Monitor Java applications on any environment - Azure Monitor Application Insights
description: Monitoraggio delle prestazioni delle applicazioni per le applicazioni Java in esecuzione in qualsiasi ambiente senza instrumentare l'app. Traccia distribuita e mappa dell'applicazione.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5a62be45320523ee0577d56eb557a4f87a58a1cc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886858"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Monitoraggio delle applicazioni non codice Java Monitoraggio di Azure Monitor Application Insights - anteprima pubblica

Il monitoraggio delle applicazioni codeless Java è incentrato sulla semplicità: non ci sono modifiche al codice, l'agente Java può essere abilitato attraverso solo un paio di modifiche di configurazione.

 L'agente Java funziona in qualsiasi ambiente e consente di monitorare tutte le applicazioni Java. In altre parole, se si eseguono le app Java in macchine virtuali, in locale, in AKS, in Windows, Linux - è il nome, l'agente Java 3.0 monitorerà l'app.

L'aggiunta di Application Insights Java SDK all'applicazione non è più necessaria, poiché l'agente 3.0 raccoglie automaticamente le richieste, le dipendenze e registra da solo.

È comunque possibile inviare dati di telemetria personalizzati dall'applicazione. L'agente 3.0 ne terrà traccia e lo correla insieme a tutti i dati di telemetria raccolti automaticamente.

## <a name="quickstart"></a>Guida introduttiva

**1. Scaricare l'agente**

Scaricare [applicationinsights-agent-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

**2. Puntare la JVM all'agente**

Aggiungi `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.2.jar` agli argomenti JVM dell'applicazione

Gli argomenti JVM tipici includono `-Xmx512m` e `-XX:+UseG1GC`. Quindi, se sai dove sommarli, allora sai già dove aggiungere questo.

Per ulteriori informazioni sulla configurazione degli argomenti JVM dell'applicazione, vedere [3.0 Preview: Suggerimenti per l'aggiornamento](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)degli argomenti JVM .

**3. Puntare l'agente alla risorsa di Application Insights**

Se non si dispone già di una risorsa di Application Insights, è possibile crearne una nuova seguendo i passaggi della [guida alla creazione delle risorse.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)
Creare un file `ApplicationInsights.json`di configurazione denominato , `applicationinsights-agent-3.0.0-PREVIEW.2.jar`quindi inserirlo nella stessa directory di , con il seguente contenuto:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

È possibile trovare la stringa di connessione nella risorsa Application Insights:You can find your connection string in your Application Insights resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights Connection String":::

**4. Questo è tutto!**

Avviare ora l'applicazione e passare alla risorsa Application Insights nel portale di Azure per visualizzare i dati di monitoraggio.

> [!NOTE]
> La visualizzazione dei dati di monitoraggio nel portale potrebbe richiedere alcuni minuti.


## <a name="configuration-options"></a>Opzioni di configurazione

Nel `ApplicationInsights.json` file, è inoltre possibile configurare:

* Nome del ruolo cloud
* Istanza del ruolo del cloud
* Acquisizione del log dell'applicazione
* Metriche JMX
* Micrometer
* Heartbeat
* campionamento
* HTTP Proxy
* Autodiagnosi

Vedere i dettagli in [anteprima pubblica 3.0: Opzioni di configurazione](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Richieste, dipendenze, log e metriche raccolte automaticamente

### <a name="requests"></a>Requests

* Consumatori JMS
* Consumatori Kafka
* Netty/WebFlux
* Servlets
* Pianificazione della molla

### <a name="dependencies-with-distributed-trace-propagation"></a>Dipendenze con propagazione della traccia distribuita

* Apache HttpClient e HttpAsyncClient
* gRPC
* Impossibile convertire java.net.HttpURLConnection
* JMS
* Kafka
* Client Netty
* OkHttp

### <a name="other-dependencies"></a>Altre dipendenze

* Cassandra
* JDBC
* MongoDB (async e sincronizzazione)
* Redis (Lattuga e Jedis)

### <a name="logs"></a>Log

* Java.util.logging
* Log4j (informazioni in cui si logo)
* SLF4J/Logback

### <a name="metrics"></a>Metriche

* Micrometer
* Metriche JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Invio di dati di telemetria personalizzati dall'applicazioneSending custom telemetry from your application

Il nostro obiettivo in 3.0 è quello di consentire di inviare i dati di telemetria personalizzati utilizzando le API standard.

Supportiamo Micrometer, openTelemetry API e i framework di registrazione più diffusi. Application Insights Java 3.0 acquisirà automaticamente i dati di telemetria e li correla insieme a tutti i dati di telemetria raccolti automaticamente.

Per questo motivo, non è in programma di rilasciare un SDK con Application Insights 3.0 in questo momento.

Application Insights Java 3.0 è già in ascolto dei dati di telemetria inviati a Application Insights Java SDK 2.x. Questa funzionalità è una parte importante della storia di aggiornamento per gli utenti 2.x esistenti e riempie un'importante lacuna nel supporto di telemetria personalizzato fino a quando l'API OpenTelemetry è GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Invio di dati di telemetria personalizzati tramite Application Insights Java SDK 2.xSending custom telemetry using Application Insights Java SDK 2.x

Aggiungi `applicationinsights-core-2.6.0.jar` alla tua applicazione (tutte le versioni 2.x sono supportate da Application Insights Java 3.0, ma vale la pena usare la più recente se hai una scelta):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Creare un TelemetryClient:Create a TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usarlo per l'invio di dati di telemetria personalizzati.

### <a name="events"></a>Events

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metriche

È possibile inviare dati di telemetria metrici tramite [Micrometer:You](https://micrometer.io)can send metric telemetry via Micrometer :

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

In alternativa, è anche possibile usare Application Insights Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Dependencies

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Log
È possibile inviare dati di telemetria del log personalizzati tramite il framework di registrazione preferito.

In alternativa, è anche possibile usare Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Eccezioni
È possibile inviare dati di telemetria delle eccezioni personalizzati tramite il framework di registrazione preferito.

In alternativa, è anche possibile usare Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Aggiornamento da Application Insights Java SDK 2.x

Se si usa già Application Insights Java SDK 2.x nell'applicazione, non è necessario rimuoverlo. L'agente Java 3.0 lo rileverà e acquisirà e correlerà tutti i dati di telemetria personalizzati inviati tramite Java SDK 2.x, eliminando qualsiasi raccolta automatica eseguita da Java SDK 2.x per impedire l'acquisizione di duplicati.

> [!NOTE]
> Nota: Java SDK 2.x TelemetryInitializers e TelemetryProcessors non verranno eseguiti quando si utilizza l'agente 3.0.
