---
title: 'Monitorare le applicazioni Java in qualsiasi ambiente: monitoraggio di Azure Application Insights'
description: Application Performance Monitoring per le applicazioni Java in esecuzione in qualsiasi ambiente senza strumentazione dell'app. Traccia distribuita e mappa delle applicazioni.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 3e3d108603ad6210143deea58049ff7b230bb6fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85319704"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Monitoraggio di Azure per il monitoraggio di applicazioni Java non codificate Application Insights-anteprima pubblica

Il monitoraggio di applicazioni codeless Java è tutta una questione di semplicità: senza cambiare il codice, l'agente Java può essere abilitato solo con un paio di modifiche di configurazione.

 L'agente Java funziona in qualsiasi ambiente e consente di monitorare tutte le applicazioni Java. In altre parole, sia che si eseguano app Java in macchine virtuali, in locale, in AKS, in Windows, Linux, l'agente Java 3,0 monitorerà l'app.

L'aggiunta di Application Insights Java SDK all'applicazione non è più necessaria, in quanto l'agente 3,0 raccoglie le richieste, le dipendenze e i registri tutti autonomamente.

È comunque possibile inviare dati di telemetria personalizzati dall'applicazione. L'agente 3,0 lo registrerà e lo correla insieme a tutti i dati di telemetria di raccolta automatica.

## <a name="quickstart"></a>Guida introduttiva

**1. scaricare l'agente**

Scaricare [applicationinsights-Agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

**2. puntare la JVM all'agente**

Aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.5.jar` agli argomenti JVM dell'applicazione

Gli argomenti JVM tipici includono `-Xmx512m` e `-XX:+UseG1GC` . Quindi, se si conosce la posizione in cui aggiungere questi, si sa già dove aggiungere questa.

Per ulteriori informazioni sulla configurazione degli argomenti JVM dell'applicazione, vedere la pagina relativa all' [anteprima 3,0: suggerimenti per l'aggiornamento degli argomenti JVM](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. puntare l'agente alla risorsa Application Insights**

Se non si ha già una risorsa Application Insights, è possibile crearne una nuova attenendosi alla procedura descritta nella [Guida alla creazione di risorse](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Puntare l'agente alla risorsa Application Insights impostando una variabile di ambiente:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Oppure creando un file di configurazione denominato `ApplicationInsights.json` e inserendolo nella stessa directory di `applicationinsights-agent-3.0.0-PREVIEW.5.jar` , con il contenuto seguente:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

È possibile trovare la stringa di connessione nella risorsa Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Stringa di connessione Application Insights":::

**4.**

A questo punto, avviare l'applicazione e passare alla risorsa Application Insights nel portale di Azure per visualizzare i dati di monitoraggio.

> [!NOTE]
> Potrebbero essere necessari alcuni minuti per la visualizzazione dei dati di monitoraggio nel portale.


## <a name="configuration-options"></a>Opzioni di configurazione

Nel `ApplicationInsights.json` file è inoltre possibile configurare:

* Nome del ruolo Cloud
* Istanza del ruolo del cloud
* Acquisizione registro applicazioni
* Metriche JMX
* Micrometer
* Heartbeat
* campionamento
* Proxy HTTP
* Diagnostica automatica

Vedere i dettagli all' [anteprima pubblica 3,0: opzioni di configurazione](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Richieste, dipendenze, log e metriche per la raccolta di dati

### <a name="requests"></a>Requests

* Utenti JMS
* Consumatori Kafka
* Netty/webflux
* Servlets
* Pianificazione della molla

### <a name="dependencies-with-distributed-trace-propagation"></a>Dipendenze con propagazione di traccia distribuita

* Apache HttpClient e HttpAsyncClient
* gRPC
* Java. NET. HttpURLConnection
* JMS
* Kafka
* Client Netty
* OkHttp

### <a name="other-dependencies"></a>Altre dipendenze

* Cassandra
* JDBC
* MongoDB (asincrona e sincronizzazione)
* Redis (lattuga e Jedi)

### <a name="logs"></a>Log

* Java. util. Logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Metriche

* Micrometro (incluse le metriche dell'attuatore Spring boot)
* Metriche JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Invio di dati di telemetria personalizzati dall'applicazione

L'obiettivo di 3.0 + è quello di consentire l'invio di dati di telemetria personalizzati usando le API standard.

Sono supportati il micrometro, l'API OpenTelemetry e i Framework di registrazione più diffusi. Application Insights Java 3,0 acquisisce automaticamente i dati di telemetria e li correla insieme a tutti i dati di telemetria autoraccolti.

Per questo motivo, non si prevede di rilasciare un SDK con Application Insights 3,0 al momento.

Application Insights Java 3,0 è già in ascolto per la telemetria inviata al Application Insights Java SDK 2. x. Questa funzionalità è una parte importante della storia di aggiornamento per gli utenti esistenti di 2. x e colma un notevole gap nel supporto di telemetria personalizzato fino a quando l'API OpenTelemetry non è GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Invio di dati di telemetria personalizzati con Application Insights Java SDK 2. x

Aggiungere `applicationinsights-core-2.6.0.jar` all'applicazione (tutte le versioni 2. x sono supportate da Application Insights Java 3,0, ma è opportuno usare la versione più recente se è possibile scegliere):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Creare un TelemetryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usarlo per l'invio di dati di telemetria personalizzati.

### <a name="events"></a>Eventi

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metriche

È possibile inviare dati di telemetria delle metriche tramite [micrometro](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

In alternativa, è anche possibile usare Application Insights Java SDK 2. x:

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
È possibile inviare dati di telemetria dei log personalizzati tramite il Framework di registrazione preferito.

In alternativa, è anche possibile usare Application Insights Java SDK 2. x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Eccezioni
È possibile inviare dati di telemetria delle eccezioni personalizzati tramite il Framework di registrazione preferito.

In alternativa, è anche possibile usare Application Insights Java SDK 2. x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Aggiornamento da Application Insights Java SDK 2. x

Se si sta già usando Application Insights Java SDK 2. x nell'applicazione, non è necessario rimuoverlo. L'agente Java 3,0 lo rileva e acquisisce e mette in correlazione i dati di telemetria personalizzati che vengono inviati tramite Java SDK 2. x, evitando in tal modo la raccolta automatica eseguita da Java SDK 2. x per impedire l'acquisizione di duplicati.

> [!NOTE]
> Nota: Java SDK 2. x TelemetryInitializers e TelemetryProcessors non verranno eseguiti quando si usa l'agente 3,0.
