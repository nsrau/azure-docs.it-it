---
title: Processori di telemetria (anteprima)-monitoraggio di Azure Application Insights Java
description: Processori di telemetria per monitoraggio di Azure Application Insights Java
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e3c41a7a9968a7de743f0c513b1f2b194501d0df
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425797"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Processori di telemetria (anteprima) per monitoraggio di Azure Application Insights Java

> [!NOTE]
> Questa funzionalità è ancora in anteprima.

Java 3,0 Agent per Application Insights ora offre le funzionalità per elaborare i dati di telemetria prima che i dati vengano esportati.

### <a name="some-use-cases"></a>Alcuni casi d'uso:
 * Maschera dati sensibili
 * Aggiungere dimensioni personalizzate in modo condizionale
 * Aggiornare il nome della telemetria usato per l'aggregazione e la visualizzazione

### <a name="supported-processors"></a>Processori supportati:
 * Processore di attributi
 * Processore span

## <a name="to-get-started"></a>Per iniziare

Creare un file di configurazione denominato `applicationinsights.json` e posizionarlo nella stessa directory di `applicationinsights-agent-***.jar` , con il modello seguente.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Includi/Escludi span

Il processore di attributi e il processore span espongono l'opzione per fornire un set di proprietà di un intervallo in base al quale eseguire la corrispondenza, per determinare se l'intervallo deve essere incluso o escluso dal processore. Per configurare questa opzione, in `include` e/o almeno `exclude` uno `matchType` e uno di `spanNames` o `attributes` è obbligatorio. La configurazione di inclusione/esclusione è supportata per avere più di una condizione specificata. Tutte le condizioni specificate devono restituire true perché si verifichi una corrispondenza. 

**Campo obbligatorio** : 
* `matchType` Controlla la modalità di interpretazione degli elementi nelle `spanNames` `attributes` matrici e. I possibili valori sono `regexp` o `strict`. 

**Campi facoltativi** : 
* `spanNames` deve corrispondere ad almeno uno degli elementi. 
* `attributes` Specifica l'elenco di attributi da confrontare. Tutti questi attributi devono corrispondere esattamente affinché si verifichi una corrispondenza.

> [!NOTE]
> Se `include` `exclude` vengono specificati sia che, le `include` proprietà vengono verificate prima delle `exclude` Proprietà.

#### <a name="sample-usage"></a>Esempio di utilizzo

Nell'esempio seguente viene illustrato come specificare il set di proprietà span per indicare gli intervalli a cui deve essere applicato questo processore. Le `include` proprietà di indicano quali devono essere incluse e le `exclude` Proprietà filtrano ulteriormente gli intervalli che non devono essere elaborati.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

Con la configurazione precedente, vengono applicati gli intervalli seguenti che corrispondono alle proprietà e alle azioni del processore:

* Nome span1: attributi ' svcB ': {ENV: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Nome Span2: attributi ' svcA ': {ENV: staging, test_request: false, redact_trace: true}

Gli intervalli seguenti non corrispondono alle proprietà di inclusione e le azioni del processore non vengono applicate:

* Nome Span3: attributi ' svcB ': {ENV: Production, test_request: true, credit_card: 1234, redact_trace: false}

* Nome Span4: attributi ' svcC ': {ENV: dev, test_request: false}

## <a name="attribute-processor"></a>Processore di attributi 

Il processore degli attributi modifica gli attributi di un intervallo. Supporta facoltativamente la possibilità di includere/escludere gli intervalli.
Accetta un elenco di azioni che vengono eseguite nell'ordine specificato nel file di configurazione. Le azioni supportate sono:

* `insert` : Inserisce un nuovo attributo in intervalli in cui la chiave non esiste già
* `update` : Aggiorna un attributo in intervalli in cui esiste la chiave
* `delete` : Elimina un attributo da un intervallo
* `hash`   : Hash (SHA1) valore di attributo esistente

Per le azioni `insert` e `update`
* `key` è obbligatorio
* uno di `value` o `fromAttribute` è obbligatorio
* `action` è obbligatorio.

Per l' `delete` azione,
* `key` è obbligatorio
* `action`: `delete` è obbligatorio.

Per l' `hash` azione,
* `key` è obbligatorio
* `action` : `hash` è obbligatorio.

L'elenco di azioni può essere composto per creare scenari avanzati, ad esempio l'attributo di riempimento, la copia dei valori in una nuova chiave, relazioni informazioni riservate.

#### <a name="sample-usage"></a>Esempio di utilizzo

Nell'esempio seguente viene illustrata l'inserimento di chiavi/valori in intervalli:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

Nell'esempio seguente viene illustrata la configurazione del processore per aggiornare solo le chiavi esistenti in un attributo:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

Nell'esempio seguente viene illustrato come elaborare gli intervalli con un nome di intervallo che corrispondono ai modelli RegExp.
Questo processore rimuoverà l'attributo "token" e l'attributo "password" verrà offuscato negli intervalli in cui span name corrisponde a "auth \* ". e dove span name non corrisponde a " \* login".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Processori span

Il processore span modifica il nome dell'intervallo o gli attributi di un intervallo in base al nome dell'estensione. Supporta facoltativamente la possibilità di includere/escludere gli intervalli.

### <a name="name-a-span"></a>Assegnare un nome a un intervallo

L'impostazione seguente è obbligatoria come parte della sezione Nome:

* `fromAttributes`: Il valore dell'attributo per le chiavi viene usato per creare un nuovo nome nell'ordine specificato nella configurazione. È necessario specificare tutte le chiavi dell'attributo nell'intervallo per la ridenominazione da parte del processore.

È possibile configurare facoltativamente la seguente impostazione:

* `separator`: Stringa, specificata, che verrà usata per suddividere i valori
> [!NOTE]
> Se la ridenominazione dipende dagli attributi modificati dal processore degli attributi, verificare che il processore span venga specificato dopo il processore degli attributi nella specifica della pipeline.

#### <a name="sample-usage"></a>Esempio di utilizzo

Nell'esempio seguente vengono specificati i valori dell'attributo "DB. svc", "Operation" e "ID" formerà il nuovo nome dell'intervallo, in questo ordine, separato dal valore "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Estrai attributi dal nome dell'estensione

Accetta un elenco di espressioni regolari per la corrispondenza tra il nome dell'estensione e l'estrazione degli attributi in base alle sottoespressioni. Deve essere specificato nella `toAttributes` sezione.

Sono necessarie le impostazioni seguenti:

`rules` : Elenco di regole per estrarre i valori degli attributi dal nome dell'estensione. I valori nel nome dell'intervallo sono sostituiti dai nomi degli attributi estratti. Ogni regola nell'elenco è una stringa di criteri Regex. Il nome dell'estensione viene verificato rispetto all'espressione regolare. Se l'espressione regolare corrisponde, tutte le sottoespressioni denominate dell'espressione regolare vengono estratte come attributi e vengono aggiunte all'intervallo. Ogni nome di sottoespressione diventa un nome di attributo e la parte corrispondente della sottoespressione diventa il valore dell'attributo. La parte corrispondente nel nome dell'intervallo viene sostituita dal nome dell'attributo Estratto. Se gli attributi sono già presenti nell'intervallo, verranno sovrascritti. Il processo viene ripetuto per tutte le regole nell'ordine in cui sono specificate. Ogni regola successiva funziona sul nome dell'intervallo che rappresenta l'output dopo l'elaborazione della regola precedente.

#### <a name="sample-usage"></a>Esempio di utilizzo

Supponiamo che il nome dell'intervallo di input sia/API/v1/Document/12345678/Update. L'applicazione dei seguenti risultati nel nome dell'intervallo di output/api/v1/document/{documentId}/update aggiungerà un nuovo attributo "documentId" = "12345678" all'intervallo.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

Nell'esempio seguente viene illustrato come rinominare il nome dell'estensione in "{operation_website}" e aggiungere l'attributo {Key: operation_website, value: oldSpanName} quando l'intervallo presenta le proprietà seguenti:
- Il nome dell'intervallo contiene '/' in un punto qualsiasi della stringa.
- Il nome dell'intervallo non è' donot/Change '.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```