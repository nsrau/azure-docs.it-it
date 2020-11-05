---
title: Monitoraggio di Azure Application Insights Java
description: Application Performance Monitoring per le applicazioni Java in esecuzione in qualsiasi ambiente senza richiedere la modifica del codice. Traccia distribuita e mappa delle applicazioni.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 07be6a4ff08700ee9407fbf39946b7c24abbc01a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377038"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Monitoraggio di Azure per il monitoraggio di applicazioni Java non codificate Application Insights

Il monitoraggio di applicazioni codeless Java è tutta una questione di semplicità: senza cambiare il codice, l'agente Java può essere abilitato solo con un paio di modifiche di configurazione.

 L'agente Java funziona in qualsiasi ambiente e consente di monitorare tutte le applicazioni Java. In altre parole, sia che si eseguano app Java in macchine virtuali, in locale, in AKS, in Windows, Linux, l'agente Java 3,0 monitorerà l'app.

L'aggiunta di Application Insights Java SDK all'applicazione non è più necessaria, in quanto l'agente 3,0 raccoglie le richieste, le dipendenze e i registri tutti autonomamente.

È comunque possibile inviare dati di telemetria personalizzati dall'applicazione. L'agente 3,0 lo registrerà e lo correla insieme a tutti i dati di telemetria di raccolta automatica.

L'agente 3,0 supporta Java 8 e versioni successive.

## <a name="quickstart"></a>Guida introduttiva

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
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Oppure creando un file di configurazione denominato `applicationinsights.json` e inserendolo nella stessa directory di `applicationinsights-agent-3.0.0.jar` , con il contenuto seguente:

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

Nel `applicationinsights.json` file è inoltre possibile configurare:

* Nome del ruolo Cloud
* Istanza del ruolo del cloud
* campionamento
* Metriche JMX
* Dimensioni personalizzate
* Processori di telemetria
* Registrazione automatica
* Metriche del micrometro raccolte automaticamente (incluse le metriche dell'attuatore Spring boot)
* Heartbeat
* Proxy HTTP
* Diagnostica automatica

Per i dettagli completi, vedere [Opzioni di configurazione](./java-standalone-config.md) .

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Richieste, dipendenze, log e metriche per la raccolta di dati

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

## <a name="sending-custom-telemetry-from-your-application"></a>Invio di dati di telemetria personalizzati dall'applicazione

L'obiettivo di 3.0 + è quello di consentire l'invio di dati di telemetria personalizzati usando le API standard.

Sono supportati il micrometro, l'API OpenTelemetry e i Framework di registrazione più diffusi. Application Insights Java 3,0 acquisisce automaticamente i dati di telemetria e li correla insieme a tutti i dati di telemetria autoraccolti.

### <a name="supported-custom-telemetry"></a>Telemetria personalizzata supportata

La tabella seguente rappresenta i tipi di telemetria personalizzati attualmente supportati che è possibile abilitare per integrare l'agente Java 3,0. Per riepilogare, le metriche personalizzate sono supportate tramite il micrometro, le eccezioni personalizzate e le tracce possono essere abilitate tramite i Framework di registrazione e tutti i tipi di dati di telemetria personalizzati sono supportati tramite il [Application Insights Java 2. x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

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

Se si sta già usando Application Insights Java SDK 2. x nell'applicazione, non è necessario rimuoverlo.
L'agente Java 3,0 lo rileva e acquisisce e mette in correlazione i dati di telemetria personalizzati che vengono inviati tramite Java SDK 2. x, evitando in tal modo la raccolta automatica eseguita da Java SDK 2. x per evitare la telemetria duplicata.

Se si usa Application Insights agente 2. x, è necessario rimuovere la `-javaagent:` JVM ARG che punta all'agente 2. x.

> [!NOTE]
> Java SDK 2. x TelemetryInitializers e TelemetryProcessors non verranno eseguiti quando si usa l'agente 3,0.
> Molti dei casi d'uso che in precedenza erano necessari possono essere risolti in 3,0 configurando [dimensioni personalizzate](./java-standalone-config.md#custom-dimensions) o configurando [processori di telemetria](./java-standalone-telemetry-processors.md).

> [!NOTE]
> 3,0 non supporta ancora più chiavi di strumentazione in una singola JVM.
