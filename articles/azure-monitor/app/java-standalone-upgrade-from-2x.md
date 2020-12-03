---
title: 'Aggiornamento da 2. x: monitoraggio di Azure Application Insights Java'
description: Aggiornamento da monitoraggio di Azure Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: 9a0e8237d81428b1ecab95627fe106a563d2090c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532456"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Aggiornamento da Application Insights Java 2. x SDK

Se si sta già usando Application Insights Java 2. x SDK nell'applicazione, non è necessario rimuoverlo.
L'agente Java 3,0 lo rileva e acquisisce e mette in correlazione i dati di telemetria personalizzati che vengono inviati tramite l'SDK 2. x, evitando in tal modo la raccolta automatica eseguita da 2. x SDK per evitare la telemetria duplicata.

Se si usa Application Insights agente 2. x, è necessario rimuovere la `-javaagent:` JVM ARG che punta all'agente 2. x.

Nella parte restante di questo documento vengono descritte le limitazioni e le modifiche che possono verificarsi durante l'aggiornamento da 2. x a 3,0, nonché alcune soluzioni alternative che possono risultare utili.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers e TelemetryProcessors

I 2. x SDK TelemetryInitializers e TelemetryProcessors non verranno eseguiti quando si usa l'agente 3,0.
Molti dei casi d'uso che in precedenza erano necessari possono essere risolti in 3,0 configurando [dimensioni personalizzate](./java-standalone-config.md#custom-dimensions) o configurando [processori di telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Più applicazioni in una singola JVM

Attualmente, 3,0 supporta solo una singola [stringa di connessione e un nome di ruolo](./java-standalone-config.md#connection-string-and-role-name) per ogni processo in esecuzione. In particolare, non è possibile avere più app Web Tomcat nella stessa distribuzione Tomcat usando stringhe di connessione diverse o nomi di ruolo diversi.

## <a name="operation-names"></a>Nomi delle operazioni

I nomi delle operazioni in 3,0 sono stati modificati in modo da offrire in genere una migliore visualizzazione aggregata nel portale Application Insights U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Nomi delle operazioni in 3,0":::

Tuttavia, per alcune applicazioni, è comunque preferibile la visualizzazione aggregata in U/X fornita dai nomi di operazione precedenti, nel qual caso è possibile usare la funzionalità [processori di telemetria](./java-standalone-telemetry-processors.md) (anteprima) in 3,0 per replicare il comportamento precedente.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Anteporre il nome dell'operazione al metodo HTTP ( `GET` , `POST` e così via)

Nell'SDK 2. x i nomi delle operazioni sono stati preceduti dal metodo HTTP ( `GET` , `POST` e così via), ad esempio

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Nomi di operazione preceduti dal metodo http":::

Il frammento di codice seguente configura 3 processori di telemetria che si combinano per replicare il comportamento precedente.
I processori di telemetria eseguono le azioni seguenti (in ordine):

1. Il primo processore di telemetria è un processore span (con tipo `span` ), che indica che si applica a `requests` e `dependencies` .

   Corrisponde a qualsiasi intervallo con un attributo denominato `http.method` e ha un nome di intervallo che inizia con `/` .

   Estrae quindi il nome dell'intervallo in un attributo denominato `tempName` .

2. Il secondo processore di telemetria è anche un processore span.

   Corrisponde a qualsiasi intervallo con un attributo denominato `tempName` .

   Il nome dell'intervallo verrà quindi aggiornato concatenando i due attributi `http.method` e `tempName` , separati da uno spazio.

3. L'ultimo processore di telemetria è un processore di attributi (con tipo `attribute` ), che indica che si applica a tutti i dati di telemetria con attributi (attualmente `requests` `dependencies` e `traces` ).

   Corrisponde a tutti i dati di telemetria con un attributo denominato `tempName` .

   Quindi, eliminerà l'attributo denominato `tempName` , in modo che non venga segnalato come dimensione personalizzata.

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

### <a name="set-the-operation-name-to-the-full-path"></a>Imposta il nome dell'operazione sul percorso completo

Inoltre, in 2. x SDK, in alcuni casi, i nomi delle operazioni contengono il percorso completo, ad esempio

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Nomi delle operazioni con percorso completo":::

Il frammento di codice seguente configura 4 processori di telemetria che si combinano per replicare il comportamento precedente.
I processori di telemetria eseguono le azioni seguenti (in ordine):

1. Il primo processore di telemetria è un processore span (con tipo `span` ), che indica che si applica a `requests` e `dependencies` .

   Corrisponde a qualsiasi intervallo con un attributo denominato `http.url` .

   Il nome dell'intervallo verrà quindi aggiornato con il `http.url` valore dell'attributo.

   Questa è la fine, ad eccezione del fatto che `http.url` ha un aspetto simile a `http://host:port/path` ed è probabile che si voglia solo la `/path` parte.

2. Il secondo processore di telemetria è anche un processore span.

   Corrisponde a qualsiasi intervallo con un attributo denominato `http.url` (in altre parole, tutti gli intervalli a cui corrisponde il primo processore).

   Estrae quindi la parte del percorso del nome dell'intervallo in un attributo denominato `tempName` .

3. Il terzo processore di telemetria è anche un processore span.

   Corrisponde a qualsiasi intervallo con un attributo denominato `tempPath` .

   Il nome dell'intervallo verrà quindi aggiornato dall'attributo `tempPath` .

4. L'ultimo processore di telemetria è un processore di attributi (con tipo `attribute` ), che indica che si applica a tutti i dati di telemetria con attributi (attualmente `requests` `dependencies` e `traces` ).

   Corrisponde a tutti i dati di telemetria con un attributo denominato `tempPath` .

   Quindi, eliminerà l'attributo denominato `tempPath` , in modo che non venga segnalato come dimensione personalizzata.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
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

## <a name="dependency-names"></a>Nomi delle dipendenze

Sono stati modificati anche i nomi delle dipendenze in 3,0, in modo da offrire in genere una migliore visualizzazione aggregata nel portale Application Insights U/X.

Anche in questo caso, per alcune applicazioni, è comunque preferibile la visualizzazione aggregata in U/X fornita dai nomi di dipendenza precedenti. in questo caso, è possibile usare tecniche simili a quelle descritte in precedenza per replicare il comportamento precedente.

## <a name="operation-name-on-dependencies"></a>Nome dell'operazione sulle dipendenze

In precedenza nell'SDK 2. x, il nome dell'operazione dalla telemetria delle richieste è stato anche impostato sui dati di telemetria delle dipendenze.
Application Insights Java 3,0 non popola più il nome dell'operazione sui dati di telemetria delle dipendenze.
Se si desidera visualizzare il nome dell'operazione per la richiesta padre dei dati di telemetria delle dipendenze, è possibile scrivere una query logs (kusto) per creare un join dalla tabella delle dipendenze alla tabella delle richieste.
