---
title: Opzioni di configurazione-monitoraggio di Azure Application Insights Java
description: Opzioni di configurazione per il monitoraggio di Azure Application Insights Java
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: b703a708af564b9dafc8c1409333a2cfed6d2653
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427701"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Opzioni di configurazione per il monitoraggio di Azure Application Insights Java

> [!WARNING]
> **Se si esegue l'aggiornamento da 3,0 Preview**
>
> Verificare con attenzione tutte le opzioni di configurazione, perché la struttura JSON è stata modificata completamente, oltre al nome del file stesso, che è andato in minuscolo.

## <a name="connection-string-and-role-name"></a>Stringa di connessione e nome del ruolo

La stringa di connessione e il nome del ruolo sono le impostazioni più comuni necessarie per iniziare:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

La stringa di connessione è obbligatoria e il nome del ruolo è importante ogni volta che si inviano dati da applicazioni diverse alla stessa risorsa Application Insights.

Per ulteriori informazioni e altre opzioni di configurazione, vedere di seguito.

## <a name="configuration-file-path"></a>Percorso del file di configurazione

Per impostazione predefinita, Application Insights Java 3,0 prevede che il file di configurazione sia denominato `applicationinsights.json` e che si trovi nella stessa directory di `applicationinsights-agent-3.0.0.jar` .

È possibile specificare un percorso del file di configurazione personalizzato utilizzando

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variabile di ambiente o
* `applicationinsights.configuration.file` Proprietà di sistema Java

Se si specifica un percorso relativo, questo verrà risolto in relazione alla directory in cui `applicationinsights-agent-3.0.0.jar` si trova.

## <a name="connection-string"></a>Stringa di connessione

La stringa di connessione è obbligatoria. È possibile trovare la stringa di connessione nella risorsa Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Stringa di connessione Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

È anche possibile impostare la stringa di connessione usando la variabile di ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Se non si imposta la stringa di connessione, l'agente Java viene disattivato.

## <a name="cloud-role-name"></a>Nome del ruolo Cloud

Il nome del ruolo cloud viene usato per etichettare il componente nella mappa dell'applicazione.

Se si desidera impostare il nome del ruolo Cloud:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Se il nome del ruolo Cloud non è impostato, il nome della risorsa Application Insights verrà usato per etichettare il componente nella mappa dell'applicazione.

È anche possibile impostare il nome del ruolo cloud usando la variabile di ambiente `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Istanza del ruolo del cloud

Per impostazione predefinita, l'istanza del ruolo Cloud è il nome del computer.

Se si desidera impostare l'istanza del ruolo Cloud su un valore diverso anziché il nome del computer:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

È anche possibile impostare l'istanza del ruolo cloud usando la variabile di ambiente `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="sampling"></a>campionamento

Il campionamento è utile se è necessario ridurre i costi.
Il campionamento viene eseguito come funzione sull'ID operazione (noto anche come ID traccia), in modo che lo stesso ID operazione provochi sempre la stessa decisione di campionamento. In questo modo si garantisce che non si ottengono parti di una transazione distribuita campionate in mentre altre parti di esso vengono campionate.

Se, ad esempio, si imposta il campionamento sul 10%, si vedrà solo il 10% delle transazioni, ma ognuno di questi 10% avrà dettagli completi sulle transazioni end-to-end.

Di seguito è riportato un esempio di come impostare il campionamento per acquisire circa **1/3 di tutte le transazioni** . Assicurarsi di impostare la frequenza di campionamento corretta per il caso d'uso:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

È inoltre possibile impostare la percentuale di campionamento utilizzando la variabile di ambiente `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

> [!NOTE]
> Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

## <a name="jmx-metrics"></a>Metriche JMX

Se si vuole raccogliere alcune metriche JMX aggiuntive:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` nome della metrica che verrà assegnato a questa metrica JMX (può essere qualsiasi elemento).

`objectName` è il [nome dell'oggetto](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) JMX MBean che si desidera raccogliere.

`attribute` è il nome dell'attributo all'interno del MBean JMX che si desidera raccogliere.

Sono supportati i valori di metrica JMX numerici e booleani. Viene eseguito il mapping della metrica JMX booleana a `0` per false e `1` true.

[//]: # "Nota: non documentare APPLICATIONINSIGHTS_JMX_METRICS qui"
[//]: # "JSON incorporato in env var è molto confuso e deve essere documentato solo per uno scenario di connessione non codificabile"

## <a name="custom-dimensions"></a>Dimensioni personalizzate

Se si vuole aggiungere dimensioni personalizzate a tutti i dati di telemetria:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` può essere usato per leggere il valore dalla variabile di ambiente specificata all'avvio.

## <a name="telemetry-processors-preview"></a>Processori di telemetria (anteprima)

Questa funzionalità è in anteprima.

Consente di configurare le regole che verranno applicate ai dati di telemetria relativi a richieste, dipendenze e tracce, ad esempio:
 * Maschera dati sensibili
 * Aggiungere dimensioni personalizzate in modo condizionale
 * Aggiornare il nome della telemetria usato per l'aggregazione e la visualizzazione

Per altre informazioni, vedere la documentazione del [processore di telemetria](./java-standalone-telemetry-processors.md) .

## <a name="auto-collected-logging"></a>Registrazione automatica

Log4j, Logback e Java. util. Logging sono instrumentati automaticamente e la registrazione eseguita tramite questi framework di registrazione viene raccolta automaticamente.

Per impostazione predefinita, la registrazione viene raccolta solo quando tale registrazione viene eseguita a `INFO` livello o superiore.

Se si desidera modificare il livello di raccolta:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

È anche possibile impostare la soglia usando la variabile di ambiente `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` .

Questi sono i `level` valori validi che è possibile specificare nel `applicationinsights.json` file e il modo in cui corrispondono ai livelli di registrazione in diversi framework di registrazione:

| livello             | Log4j  | Logback | LUG     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| IRREVERSIBILE             | IRREVERSIBILE  | ERRORE   | SEVERE  |
| ERRORE (o grave) | ERRORE  | ERRORE   | SEVERE  |
| AVVISA (o avviso) | AVVERTIRE   | AVVERTIRE    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (o FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (o FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Metriche del micrometro raccolte automaticamente (incluse le metriche dell'attuatore Spring boot)

Se l'applicazione usa il [micrometro](https://micrometer.io), le metriche inviate al registro globale del micrometro vengono raccolte automaticamente.

Inoltre, se l'applicazione usa l' [attuatore Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), verranno raccolte automaticamente anche le metriche configurate dall'attuatore Spring boot.

Per disabilitare la raccolta automatica di metriche del micrometro (incluse le metriche dell'attuatore Spring boot):

> [!NOTE]
> Le metriche personalizzate vengono fatturate separatamente e possono generare costi aggiuntivi. Assicurarsi di controllare le informazioni dettagliate [sui prezzi](https://azure.microsoft.com/pricing/details/monitor/). Per disabilitare le metriche dell'attuatore micrometrico e Spring, aggiungere la configurazione seguente al file di configurazione.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Per impostazione predefinita, Application Insights Java 3,0 invia una metrica Heartbeat una volta ogni 15 minuti. Se si usa la metrica heartbeat per attivare gli avvisi, è possibile aumentare la frequenza di questo heartbeat:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Non è possibile ridurre la frequenza dell'heartbeat, perché i dati di heartbeat vengono usati anche per tenere traccia dell'utilizzo del Application Insights.

## <a name="http-proxy"></a>Proxy HTTP

Se l'applicazione è dietro un firewall e non è in grado di connettersi direttamente a Application Insights (vedere [indirizzi IP usati da Application Insights](./ip-addresses.md)), è possibile configurare Application Insights Java 3,0 per usare un proxy http:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "Si noti che il supporto di OpenTelemetry non viene pubblicizzato fino a quando non viene supportato 0.10.0, che presenta modifiche sostanziali da 0.9.0"

[//]: # "Supporto # # per l'API OpenTelemetry versioni precedenti alla 1,0"

[//]: # "Il supporto per le versioni precedenti 1,0 dell'API OpenTelemetry è esplicito, perché l'API OpenTelemetry non è ancora stabile"
[//]: # "quindi, ogni versione dell'agente supporta solo le versioni precedenti 1,0 dell'API OpenTelemetry"
[//]: # "Questa limitazione non si applica quando viene rilasciata l'API OpenTelemetry 1,0."

[//]: # "''' JSON"
[//]: # "{"
[//]: # "  \"Anteprima \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Diagnostica automatica

"Self-Diagnostics" si riferisce alla registrazione interna da Application Insights Java 3,0.

Questa funzionalità può essere utile per individuare e diagnosticare i problemi con Application Insights stesso.

Per impostazione predefinita, Application Insights i registri Java 3,0 al livello `INFO` del file `applicationinsights.log` e della console, corrispondenti a questa configurazione:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` può essere uno dei `file` , `console` o `file+console` .

`level` può essere uno tra `OFF` , `ERROR` , `WARN` , `INFO` , `DEBUG` o `TRACE` .

`path` può essere un percorso assoluto o relativo. I percorsi relativi vengono risolti rispetto alla directory in cui `applicationinsights-agent-3.0.0.jar` si trova.

`maxSizeMb` dimensioni massime del file di log prima del rollover.

`maxHistory` numero di file di log di cui è stato eseguito il rollback, conservati (oltre al file di log corrente).

## <a name="an-example"></a>un esempio

Questo è solo un esempio per mostrare l'aspetto di un file di configurazione con più componenti.
Configurare opzioni specifiche in base alle esigenze.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "httpProxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```