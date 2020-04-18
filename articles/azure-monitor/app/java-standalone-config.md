---
title: Monitor Java applications anywhere - Azure Monitor Application Insights
description: Monitoraggio delle prestazioni delle applicazioni senza codice per le applicazioni Java in esecuzione in qualsiasi ambiente senza instrumentare l'app. Individuare la causa principale dei problemi d utilizzando la traccia distribuita e la mappa dell'applicazione.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641888"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opzioni di configurazione - Agente autonomo Java per Azure Monitor Application InsightsConfiguration options - Java standalone agent for Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Stringa di connessione e nome del ruoloConnection string and role name

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

La stringa di connessione è obbligatoria e il nome del ruolo è importante ogni volta che si inviano dati da applicazioni diverse alla stessa risorsa di Application Insights.The connection string is required, and the role name is important any time you are sending data from different applications to the same Application Insights resource.

Troverete maggiori dettagli e ulteriori opzioni di configurazione qui sotto per maggiori dettagli.

## <a name="configuration-file-path"></a>Percorso del file di configurazione

Per impostazione predefinita, Application Insights Java 3.0 `ApplicationInsights.json`Preview prevede che il file `applicationinsights-agent-3.0.0-PREVIEW.jar`di configurazione sia denominato e che si trovi nella stessa directory di .

È possibile specificare il proprio percorso del file di configurazione

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`variabile di ambiente, o
* `applicationinsights.configurationFile`Proprietà di sistema Java

Se si specifica un percorso relativo, questo `applicationinsights-agent-3.0.0-PREVIEW.jar` verrà risolto in relazione alla directory in cui si trova.

## <a name="connection-string"></a>Stringa di connessione

È un'operazione obbligatoria. È possibile trovare la stringa di connessione nella risorsa Application Insights:You can find your connection string in your Application Insights resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights Connection String":::

È inoltre possibile impostare la `APPLICATIONINSIGHTS_CONNECTION_STRING`stringa di connessione utilizzando la variabile di ambiente .

## <a name="cloud-role-name"></a>Nome del ruolo cloud

Il nome del ruolo cloud viene usato per etichettare il componente nella mappa dell'applicazione.

Se si desidera impostare il nome del ruolo cloud:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Se il nome del ruolo cloud non è impostato, il nome della risorsa di Application Insights verrà usato per etichettare il componente nella mappa dell'applicazione.

È inoltre possibile impostare il nome `APPLICATIONINSIGHTS_ROLE_NAME`del ruolo cloud utilizzando la variabile di ambiente .

## <a name="cloud-role-instance"></a>Istanza del ruolo del cloud

L'istanza del ruolo cloud per impostazione predefinita è il nome del computer.

Se si desidera impostare l'istanza del ruolo cloud su un valore diverso anziché sul nome del computer:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

È anche possibile impostare l'istanza `APPLICATIONINSIGHTS_ROLE_INSTANCE`del ruolo cloud utilizzando la variabile di ambiente .

## <a name="application-log-capture"></a>Acquisizione del log dell'applicazione

Application Insights Java 3.0 Preview acquisisce automaticamente la registrazione dell'applicazione tramite Log4j, Logback e java.util.logging.

Per impostazione predefinita, verranno acquisite tutte le registrazioni eseguite al `WARN` livello o superiore.

Per modificare questa soglia:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Questi sono `threshold` i valori validi che `ApplicationInsights.json` è possibile specificare nel file e come corrispondono ai livelli di registrazione tra diversi framework di registrazione:

| `threshold`  | Log4j (informazioni in cui si logo)  | Logback | LUG     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| Fatale        | Fatale  | ERRORE   | SEVERE  |
| ERRORE/GRAVE | ERRORE  | ERRORE   | SEVERE  |
| AVVISA/AVVISA | Avvertire   | Avvertire    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| DEBUG/FINE   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| TRACCIA/FINEST | TRACE  | TRACE   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Metriche JMX

Se hai alcune metriche JMX che sei interessato a catturare:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Micrometer

Per impostazione predefinita, se l'applicazione usa [Micrometer](https://micrometer.io), Application Insights 3.0 (a partire da Preview.2) ora si aggiunge al Registro di sistema globale Micrometer e acquisisce le metriche Micrometer.

Se si desidera disattivare questa funzione:

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

Per impostazione predefinita, Application Insights Java 3.0 Preview invia una metrica heartbeat una volta ogni 15 minuti. Se si utilizza la metrica heartbeat per attivare gli avvisi, è possibile aumentare la frequenza di questo heartbeat:

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
> Non è possibile ridurre la frequenza di questo heartbeat, poiché i dati heartbeat vengono usati anche per tenere traccia dell'utilizzo di Application Insights.You cannot decrease the frequency of this heartbeat, as the heartbeat data is also used to track Application Insights usage.

## <a name="sampling"></a>campionamento

Il campionamento è utile se è necessario ridurre i costi.
Il campionamento viene eseguito come funzione sull'ID operazione (noto anche come ID traccia), in modo che lo stesso ID operazione comporterà sempre la stessa decisione di campionamento. In questo modo non si otterranno parti di una transazione distribuita campionate mentre altre parti di essa vengono campionate.

Ad esempio, se imposti il campionamento su 10%, vedrai solo il 10% delle tue transazioni, ma ognuna di queste 10% avrà dettagli completi delle transazioni end-to-end.

Ecco un esempio di come impostare il campionamento al **10% di tutte le transazioni** - assicurati di impostare la frequenza di campionamento corretta per il tuo caso d'uso:

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

## <a name="http-proxy"></a>HTTP Proxy

Se l'applicazione è protetto da un firewall e non è in grado di connettersi direttamente ad Application Insights (vedere [Indirizzi IP usati da Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)), è possibile configurare Application Insights Java 3.0 Preview per l'utilizzo di un proxy HTTP:

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

## <a name="self-diagnostics"></a>Autodiagnostica

"Autodiagnostica" si riferisce alla registrazione interna da Application Insights Java 3.0 Preview.

Ciò può essere utile per individuare e diagnosticare i problemi con Application Insights stesso.

Per impostazione predefinita, viene `warn`eseguita dalla console con livello corrispondente a questa configurazione:

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

I livelli `OFF` `ERROR`validi `WARN` `INFO`sono `DEBUG`, `TRACE`, , , e .

Se si desidera accedere a un file anziché registrare nella console:

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

Quando si utilizza la registrazione `maxSizeMB`dei file, una volta che il file raggiunge , verrà eseguito il rollover, mantenendo solo il file di registro completato più di recente oltre al file di registro corrente.
