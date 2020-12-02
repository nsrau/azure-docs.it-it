---
title: Monitoraggio di Azure Application Insights Java
description: Application Performance Monitoring per le applicazioni Java in esecuzione in qualsiasi ambiente senza richiedere la modifica del codice. Traccia distribuita e mappa delle applicazioni.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 7046e4a1aeeda5e537208c79858c95c79e188348
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437202"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Monitoraggio di Azure per il monitoraggio di applicazioni Java non codificate Application Insights

Il monitoraggio di applicazioni codeless Java è tutta una questione di semplicità: senza cambiare il codice, l'agente Java può essere abilitato solo con un paio di modifiche di configurazione.

 L'agente Java funziona in qualsiasi ambiente e consente di monitorare tutte le applicazioni Java. In altre parole, sia che si eseguano app Java in macchine virtuali, in locale, in AKS, in Windows, Linux, l'agente Java 3,0 monitorerà l'app.

L'aggiunta di Application Insights Java SDK all'applicazione non è più necessaria, in quanto l'agente 3,0 raccoglie automaticamente le richieste, le dipendenze e i registri autonomamente.

È comunque possibile inviare dati di telemetria personalizzati dall'applicazione. L'agente 3,0 li registrerà e li correla insieme a tutti i dati di telemetria raccolti automaticamente.

L'agente 3,0 supporta Java 8 e versioni successive.

## <a name="quickstart"></a>Avvio rapido

**1. scaricare l'agente**

> [!WARNING]
> **Se si esegue l'aggiornamento da 3,0 Preview**
>
> Controllare attentamente tutte le [Opzioni di configurazione](./java-standalone-config.md) , in quanto la struttura JSON è stata modificata completamente, oltre al nome del file stesso, che è andato in minuscolo.

Scaricare [applicationinsights-Agent-3.0.0. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar)

**2. puntare la JVM all'agente**

Aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` agli argomenti JVM dell'applicazione

Gli argomenti JVM tipici includono `-Xmx512m` e `-XX:+UseG1GC` . Quindi, se si conosce la posizione in cui aggiungere questi, si sa già dove aggiungere questa.

Per ulteriori informazioni sulla configurazione degli argomenti JVM dell'applicazione, vedere [Suggerimenti per l'aggiornamento degli argomenti JVM](./java-standalone-arguments.md).

**3. puntare l'agente alla risorsa Application Insights**

Se non si ha già una risorsa Application Insights, è possibile crearne una nuova attenendosi alla procedura descritta nella [Guida alla creazione di risorse](./create-new-resource.md).

Puntare l'agente alla risorsa Application Insights impostando una variabile di ambiente:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Oppure creando un file di configurazione denominato `applicationinsights.json` e inserendolo nella stessa directory di `applicationinsights-agent-3.0.0.jar` , con il contenuto seguente:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

È possibile trovare la stringa di connessione nella risorsa Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Stringa di connessione Application Insights":::

**4.**

A questo punto, avviare l'applicazione e passare alla risorsa Application Insights nel portale di Azure per visualizzare i dati di monitoraggio.

> [!NOTE]
> Potrebbero essere necessari alcuni minuti per la visualizzazione dei dati di monitoraggio nel portale.


## <a name="configuration-options"></a>Opzioni di configurazione

Nel `applicationinsights.json` file è inoltre possibile configurare:

* Nome del ruolo Cloud
* Istanza del ruolo del cloud
* campionamento
* Metriche JMX
* Dimensioni personalizzate
* Processori di telemetria (anteprima)
* Registrazione automatica
* Metriche del micrometro raccolte automaticamente (incluse le metriche dell'attuatore Spring boot)
* Heartbeat
* Proxy HTTP
* Diagnostica automatica

Per i dettagli completi, vedere [Opzioni di configurazione](./java-standalone-config.md) .

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Richieste, dipendenze, log e metriche raccolte automaticamente

### <a name="requests"></a>Requests

* Utenti JMS
* Consumatori Kafka
* Netty/webflux
* Servlet
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
* Log4j (incluse le proprietà MDC)
* SLF4J/Logback (incluse le proprietà MDC)

### <a name="metrics"></a>Metriche

* Micrometro (incluse le metriche dell'attuatore Spring boot)
* Metriche JMX

## <a name="send-custom-telemetry-from-your-application"></a>Inviare dati di telemetria personalizzati dall'applicazione

L'obiettivo di 3.0 + è quello di consentire l'invio di dati di telemetria personalizzati usando le API standard.

Sono supportati il micrometro, i Framework di registrazione più diffusi e il Application Insights Java 2. x SDK fino a questo momento.
Application Insights Java 3,0 acquisisce automaticamente i dati di telemetria inviati tramite queste API e li correla con i dati di telemetria raccolti automaticamente.

### <a name="supported-custom-telemetry"></a>Telemetria personalizzata supportata

La tabella seguente rappresenta i tipi di telemetria personalizzati attualmente supportati che è possibile abilitare per integrare l'agente Java 3,0. Per riepilogare, le metriche personalizzate sono supportate tramite il micrometro, le eccezioni personalizzate e le tracce possono essere abilitate tramite i Framework di registrazione e tutti i tipi di dati di telemetria personalizzati sono supportati tramite il [Application Insights Java 2. x SDK](#send-custom-telemetry-using-application-insights-java-2x-sdk).

|                     | Micrometer | Log4j, logback, JUL | 2. x SDK |
|---------------------|------------|---------------------|---------|
| **Eventi personalizzati**   |            |                     |  Sì    |
| **Metriche personalizzate**  |  Sì       |                     |  Sì    |
| **Dipendenze**    |            |                     |  Sì    |
| **Eccezioni**      |            |  Sì                |  Sì    |
| **Visualizzazioni pagina**      |            |                     |  Sì    |
| **Richieste**        |            |                     |  Sì    |
| **Tracce**          |            |  Sì                |  Sì    |

Al momento non si prevede di rilasciare un SDK con Application Insights 3,0.

Application Insights Java 3,0 è già in ascolto per la telemetria inviata al Application Insights Java 2. x SDK. Questa funzionalità è una parte importante della storia di aggiornamento per gli utenti esistenti di 2. x e colma un notevole gap nel supporto di telemetria personalizzato fino a quando l'API OpenTelemetry non è GA.

### <a name="send-custom-metrics-using-micrometer"></a>Inviare metriche personalizzate con micrometro

Aggiungere un micrometro all'applicazione:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Usare il [registro globale](https://micrometer.io/docs/concepts#_global_registry) del micrometro per creare un contatore:

```java
static final Counter counter = Metrics.counter("test_counter");
```

e usarlo per registrare le metriche:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Inviare tracce ed eccezioni personalizzate usando il Framework di registrazione preferito

Log4j, Logback e Java. util. Logging sono instrumentati automaticamente e la registrazione eseguita tramite questi framework di registrazione viene raccolta automaticamente come dati di telemetria di traccia ed eccezioni.

Per impostazione predefinita, la registrazione viene raccolta solo quando tale registrazione viene eseguita a livello di informazioni o superiore.
Vedere le [Opzioni di configurazione](./java-standalone-config.md#auto-collected-logging) per informazioni su come modificare questo livello.

Se si vuole allineare dimensioni personalizzate ai log, è possibile usare [log4j 1 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)o [Logback MDC](http://logback.qos.ch/manual/mdc.html)e Application Insights Java 3,0 acquisisce automaticamente le proprietà di MDC come dimensioni personalizzate nei dati di telemetria delle tracce e delle eccezioni.

### <a name="send-custom-telemetry-using-application-insights-java-2x-sdk"></a>Inviare dati di telemetria personalizzati con Application Insights Java 2. x SDK

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
static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usarlo per inviare dati di telemetria personalizzati:

##### <a name="events"></a>Eventi

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metriche

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Dependencies

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

##### <a name="logs"></a>Log

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Eccezioni

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```
