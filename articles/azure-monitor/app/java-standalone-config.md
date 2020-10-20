---
title: Monitora le applicazioni Java ovunque-monitoraggio di Azure Application Insights
description: Application Performance Monitoring senza codice per applicazioni Java in esecuzione in qualsiasi ambiente senza strumentazione dell'app. Trovare la causa radice dei problemi d usando la traccia distribuita e la mappa delle applicazioni.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 36f2add41457d1d82b0efd6c6804496018c85225
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215264"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opzioni di configurazione-Application Insights agente autonomo Java per monitoraggio di Azure



## <a name="connection-string-and-role-name"></a>Stringa di connessione e nome del ruolo

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

La stringa di connessione è obbligatoria e il nome del ruolo è importante ogni volta che si inviano dati da applicazioni diverse alla stessa risorsa Application Insights.

Per altri dettagli, vedere più dettagli e altre opzioni di configurazione.

## <a name="configuration-file-path"></a>Percorso del file di configurazione

Per impostazione predefinita, Application Insights Java 3,0 Preview prevede che il file di configurazione sia denominato `ApplicationInsights.json` e che si trovi nella stessa directory di `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

È possibile specificare un percorso del file di configurazione personalizzato utilizzando

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variabile di ambiente o
* `applicationinsights.configurationFile` Proprietà di sistema Java

Se si specifica un percorso relativo, questo verrà risolto in relazione alla directory in cui `applicationinsights-agent-3.0.0-PREVIEW.5.jar` si trova.

## <a name="connection-string"></a>Stringa di connessione

È un'operazione obbligatoria. È possibile trovare la stringa di connessione nella risorsa Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Stringa di connessione Application Insights":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

È anche possibile impostare la stringa di connessione usando la variabile di ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Se non si imposta la stringa di connessione, l'agente Java viene disattivato.

## <a name="cloud-role-name"></a>Nome del ruolo Cloud

Il nome del ruolo cloud viene usato per etichettare il componente nella mappa dell'applicazione.

Se si desidera impostare il nome del ruolo Cloud:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
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
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

È anche possibile impostare l'istanza del ruolo cloud usando la variabile di ambiente `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Acquisizione registro applicazioni

Application Insights Java 3,0 Preview acquisisce automaticamente la registrazione delle applicazioni tramite log4j, Logback e Java. util. Logging.

Per impostazione predefinita, acquisisce tutte le registrazioni eseguite a `INFO` livello o superiore.

Se si vuole modificare questa soglia:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

È anche possibile impostare la soglia di registrazione usando la variabile di ambiente `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Questi sono i `threshold` valori validi che è possibile specificare nel `ApplicationInsights.json` file e il modo in cui corrispondono ai livelli di registrazione tra diversi framework di registrazione:

| valore soglia   | Log4j  | Logback | LUG     |
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

## <a name="jmx-metrics"></a>Metriche JMX

Se sono presenti alcune metriche JMX che si desidera acquisire:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Sono supportati i valori di metrica JMX numerici e booleani. Viene eseguito il mapping della metrica JMX booleana a `0` per false e `1` true.

[//]: # "Nota: non documentare APPLICATIONINSIGHTS_JMX_METRICS qui"
[//]: # "JSON incorporato in env var è molto confuso e deve essere documentato solo per uno scenario di connessione non codificabile"

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometro (incluse le metriche dell'attuatore Spring boot)

Se l'applicazione usa il [micrometro](https://micrometer.io), Application Insights 3,0 (a partire dalla versione Preview. 2) acquisisce ora le metriche inviate al registro globale micrometrico.

Se l'applicazione usa l' [attuatore Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), Application Insights 3,0 (a partire dalla versione Preview. 4) acquisisce ora le metriche configurate dall'attuatore Spring boot (che usa il micrometro, ma non usa il registro globale del micrometro).

Se si desidera disabilitare queste funzionalità:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Per impostazione predefinita, Application Insights Java 3,0 Preview invia una metrica Heartbeat una volta ogni 15 minuti. Se si usa la metrica heartbeat per attivare gli avvisi, è possibile aumentare la frequenza di questo heartbeat:

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> Non è possibile ridurre la frequenza di questo heartbeat, perché i dati di heartbeat vengono usati anche per tenere traccia dell'utilizzo del Application Insights.

## <a name="sampling"></a>campionamento

Il campionamento è utile se è necessario ridurre i costi.
Il campionamento viene eseguito come funzione sull'ID operazione (noto anche come ID traccia), in modo che lo stesso ID operazione provochi sempre la stessa decisione di campionamento. In questo modo si garantisce che non si ottengono parti di una transazione distribuita campionate in mentre altre parti di esso vengono campionate.

Se, ad esempio, si imposta il campionamento sul 10%, si vedrà solo il 10% delle transazioni, ma ognuno di questi 10% avrà dettagli completi sulle transazioni end-to-end.

Di seguito è riportato un esempio di come impostare il campionamento sul **10% di tutte le transazioni** . Assicurarsi di impostare la frequenza di campionamento corretta per il caso d'uso:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

È inoltre possibile impostare la percentuale di campionamento utilizzando la variabile di ambiente `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>Proxy HTTP

Se l'applicazione è protetti da un firewall e non è in grado di connettersi direttamente a Application Insights (vedere [indirizzi IP usati da Application Insights](./ip-addresses.md)), è possibile configurare Application Insights Java 3,0 Preview per usare un proxy http:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Diagnostica automatica

"Self-Diagnostics" si riferisce alla registrazione interna da Application Insights anteprima Java 3,0.

Questo può essere utile per individuare e diagnosticare i problemi con Application Insights stesso.

Per impostazione predefinita, viene registrato nella console di con `warn` il livello corrispondente a questa configurazione:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

I livelli validi sono `OFF` ,, `ERROR` `WARN` , `INFO` , `DEBUG` e `TRACE` .

Se si desidera accedere a un file anziché registrarsi nella console:

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

Quando si usa la funzionalità di registrazione file, una volta raggiunto il file `maxSizeMB` , verrà eseguito il rollover, mantenendo solo il file di log completato più di recente oltre al file di log corrente.
