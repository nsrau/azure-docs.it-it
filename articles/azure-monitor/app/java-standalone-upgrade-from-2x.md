---
title: 'Aggiornamento da 2. x: monitoraggio di Azure Application Insights Java'
description: Aggiornamento da monitoraggio di Azure Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355043"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Aggiornamento da Application Insights Java SDK 2. x

Se si sta già usando Application Insights Java SDK 2. x nell'applicazione, non è necessario rimuoverlo.
L'agente Java 3,0 lo rileva e acquisisce e mette in correlazione i dati di telemetria personalizzati che vengono inviati tramite Java SDK 2. x, evitando in tal modo la raccolta automatica eseguita da Java SDK 2. x per evitare la telemetria duplicata.

Se si usa Application Insights agente 2. x, è necessario rimuovere la `-javaagent:` JVM ARG che punta all'agente 2. x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers e TelemetryProcessors

Java SDK 2. x TelemetryInitializers e TelemetryProcessors non verranno eseguiti quando si usa l'agente 3,0.
Molti dei casi d'uso che in precedenza erano necessari possono essere risolti in 3,0 configurando [dimensioni personalizzate](./java-standalone-config.md#custom-dimensions) o configurando [processori di telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Più applicazioni in una singola JVM

Attualmente, 3,0 supporta solo una singola [stringa di connessione e un nome di ruolo](./java-standalone-config.md#connection-string-and-role-name) per ogni processo in esecuzione. In particolare, non è possibile avere più app Web Tomcat nella stessa distribuzione Tomcat usando stringhe di connessione diverse o nomi di ruolo diversi.

## <a name="http-request-telemetry-names"></a>Nomi di telemetria delle richieste HTTP

I nomi di telemetria delle richieste HTTP in 3,0 sono stati modificati in modo da offrire in genere una migliore visualizzazione aggregata nel portale Application Insights U/X.

Tuttavia, per alcune applicazioni, è comunque preferibile la visualizzazione aggregata in U/X fornita dai nomi di telemetria precedenti, nel qual caso è possibile usare la funzionalità di anteprima dei processori di telemetria in 3,0 per tornare ai nomi precedenti.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Per anteporre al nome di telemetria il metodo HTTP ( `GET` , `POST` e così via):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Per impostare il nome della telemetria sul percorso URL completo

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
