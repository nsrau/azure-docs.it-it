---
title: Informazioni sui moduli gemelli nell'hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Usare moduli gemelli per sincronizzare stato e dati di configurazione tra l'hub IoT e i dispositivi
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 064bfd7a51f3ccb0252f37fbaa11ebc122a4b97f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807426"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Comprendere e usare i moduli gemelli nell'hub IoT

Questo articolo presuppone che sia già stato letto l'articolo [Comprendere e usare i dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md). Nell'hub IoT è possibile creare fino a 20 identità del modulo in ogni identità del dispositivo. Ogni identità del modulo genera implicitamente un modulo gemello. Simili ai dispositivi gemelli, i moduli gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei moduli, tra cui metadati, configurazioni e condizioni. L'hub IoT di Azure mantiene un modulo gemello per ogni modulo che viene connesso all'hub IoT. 

Sul lato del dispositivo, gli SDK per dispositivi dell'hub IoT consentono di creare moduli ognuno dei quali apre una connessione indipendente all'hub IoT. Questa funzionalità permette di usare spazi dei nomi distinti per i diversi componenti nel dispositivo. Supponiamo ad esempio di avere un distributore automatico con tre diversi sensori. Ogni sensore è controllato da reparti diversi dell'azienda. È possibile creare un modulo per ogni sensore. In questo modo ogni reparto può inviare processi o metodi diretti solo al sensore che controlla, evitando conflitti ed errori degli utenti.

 L'identità del modulo e il modulo gemello offrono le stesse funzionalità dell'identità del dispositivo e dei dispositivi gemelli, ma con una granularità superiore. Questa granularità superiore consente a determinati dispositivi, ad esempio i dispositivi basati su sistema operativo o i dispositivi firmware che gestiscono più componenti, di isolare la configurazione e le condizioni di ognuno di questi componenti. L'identità del modulo e i moduli gemelli consentono di gestire separatamente i diversi problemi quando si usano dispositivi IoT con componenti software modulari. L'obiettivo di Microsoft è supportare tutte le funzionalità dei dispositivi gemelli a livello di modulo gemello in base alla disponibilità generale dei moduli gemelli. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

L'articolo illustra:

* la struttura del modulo gemello: *tag*, *proprietà desiderate* e *proprietà segnalate*.
* Le operazioni che i moduli e i back-end possono eseguire sui moduli gemelli.

Vedere [Indicazioni sulle comunicazioni da dispositivo a cloud](iot-hub-devguide-d2c-guidance.md) per informazioni sull'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.

Vedere [Indicazioni sulle comunicazioni da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) per informazioni sull'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="module-twins"></a>Moduli gemelli

I moduli gemelli consentono di archiviare informazioni sul modulo che possono essere usate:

* Dai moduli nel dispositivo e dall'hub IoT per sincronizzare le condizioni e la configurazione del modulo.

* Dal back-end della soluzione per eseguire query e come destinazione delle operazioni a esecuzione prolungata.

Il ciclo di vita di un modulo gemello è correlato all'[identità del modulo](iot-hub-devguide-identity-registry.md) corrispondente. I moduli gemelli vengono creati ed eliminati implicitamente quando viene creata o eliminata un'identità del modulo nell'hub IoT.

Un modulo gemello è un documento JSON che include:

* **Tag**. Una sezione del documento JSON che il back-end della soluzione è in grado di leggere e in cui può scrivere. I tag non sono visibili per i moduli nel dispositivo. I tag vengono impostati per consentire l'esecuzione di query.

* **Proprietà desiderate**. Sono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del modulo. Il back-end della soluzione è in grado di impostare le proprietà desiderate e l'app per modulo è in grado di leggerle. L'app per modulo può anche ricevere notifiche relative alle modifiche apportate alle proprietà desiderate.

* **Proprietà segnalate**. Sono usate insieme alle proprietà desiderate per sincronizzare la configurazione o le condizioni del modulo. L'app per modulo è in grado di impostare le proprietà segnalate, mentre il back-end della soluzione è in grado di leggerle ed eseguire query su di esse.

* **Proprietà dell'identità del modulo**. La radice del documento JSON del modulo gemello contiene le proprietà di sola lettura dell'identità del modulo corrispondente archiviata nel [registro delle identità](iot-hub-devguide-identity-registry.md).

![Rappresentazione dei dispositivi gemelli a livello di architettura](./media/iot-hub-devguide-device-twins/module-twin.jpg)

L'esempio seguente illustra un documento JSON del modulo gemello:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

Nell'oggetto radice si trovano le proprietà dell'identità del modulo e gli oggetti contenitore per `tags` e per entrambe le proprietà `reported` e `desired`. Nel contenitore `properties` sono presenti alcuni elementi di sola lettura (`$metadata`, `$etag` e `$version`) descritti nelle sezioni [Metadati del modulo gemello](iot-hub-devguide-module-twins.md#module-twin-metadata) e [Concorrenza ottimistica](iot-hub-devguide-device-twins.md#optimistic-concurrency).

### <a name="reported-property-example"></a>Esempio di proprietà segnalata

Nell'esempio precedente, il dispositivo gemello contiene la proprietà `batteryLevel` che viene segnalata dall'app per modulo. Questa proprietà consente di eseguire query e di operare sui moduli in base al livello di carica della batteria più recente segnalato. Altri esempi sono l'app per modulo che segnala le funzionalità o le opzioni di connettività del modulo.

> [!NOTE]
> Le proprietà segnalate semplificano gli scenari in cui il back-end della soluzione è interessato all'ultimo valore noto di una proprietà. Usare i [messaggi dal dispositivo al cloud](iot-hub-devguide-messages-d2c.md) se il back-end della soluzione deve elaborare la telemetria del modulo in forma di sequenze di eventi con timestamp, ad esempio una serie temporale.

### <a name="desired-property-example"></a>Esempio di proprietà desiderata

Nell'esempio precedente le proprietà desiderate e segnalate del modulo gemello `telemetryConfig` vengono usate dal back-end della soluzione e dall'app per modulo per sincronizzare la configurazione della telemetria per questo modulo. ad esempio:

1. Il back-end della soluzione imposta la proprietà desiderata sul valore di configurazione desiderato. Questa è la parte del documento con il set di proprietà desiderate:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. L'app per modulo riceve notifica della modifica immediatamente, se connessa, o alla prima riconnessione. L'app segnala quindi la configurazione aggiornata o una condizione di errore riscontrata nell'uso della proprietà `status`. Questa è la parte con le proprietà segnalate:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Il back-end della soluzione può tenere traccia dei risultati dell'operazione di configurazione su più moduli, eseguendo [query](iot-hub-devguide-query-language.md) sui moduli gemelli.

> [!NOTE]
> I frammenti di codice precedenti sono esempi, ottimizzati per una migliore leggibilità, di un modo per codificare una configurazione del modulo e il relativo stato. L'hub IoT non impone uno schema specifico per l'uso delle proprietà desiderate e segnalate del modulo gemello nei moduli gemelli.
> 
> 

## <a name="back-end-operations"></a>Operazioni di back-end
Il back-end della soluzione opera sul modulo gemello tramite le seguenti operazioni atomiche esposte tramite HTTPS:

* **Recupero del modulo gemello in base all'ID**. Questa operazione restituisce il documento del modulo gemello, inclusi tag e proprietà di sistema desiderate e segnalate.

* **Aggiornamento parziale del modulo gemello**. Questa operazione consente al back-end della soluzione di aggiornare parzialmente i tag o le proprietà desiderate di un modulo gemello. L'aggiornamento parziale è espresso come documento JSON che aggiunge o aggiorna tutte le proprietà. Le proprietà impostate su `null` vengono rimosse. L'esempio seguente crea una nuova proprietà desiderata con valore `{"newProperty": "newValue"}`, sostituisce il valore esistente di `existingProperty` con `"otherNewValue"`, e rimuove `otherOldProperty`. Non vengono apportate altre modifiche alle altre proprietà desiderate o ai tag esistenti:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Sostituzione di proprietà desiderate**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutte le proprietà desiderate esistenti e di sostituirle con un nuovo documento JSON `properties/desired`.

* **Sostituzione di tag**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutti i tag esistenti e di sostituirli con un nuovo documento JSON `tags`.

* **Ricezione di notifiche relative al dispositivo gemello**. Questa operazione invia notifiche al back-end della soluzione a ogni modifica del dispositivo gemello. A questo scopo, la soluzione IoT deve creare una route e impostare l'origine dati su *twinChangeEvents*. Per impostazione predefinita, non viene inviata alcuna notifica, ovvero queste route non sono preesistenti. Se la frequenza delle modifiche è troppo elevata o per altri motivi, ad esempio un errore interno, l'hub IoT potrebbe inviare solo una notifica che contiene tutte le modifiche. Se pertanto l'applicazione ha bisogno di controllo e registrazione affidabili di tutti gli stati intermedi, è consigliabile usare messaggi da dispositivo a cloud. Il messaggio di notifica relativo al dispositivo gemello include le proprietà e il corpo.

  - properties

    | name | Value |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Data e ora in cui è stata inviata la notifica |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID del dispositivo |
    moduleId | ID del modulo |
    hubName | Nome dell'hub IoT |
    operationTimestamp | Timestamp [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) dell'operazione |
    iothub-message-schema | twinChangeNotification |
    opType | "replaceTwin" o "updateTwin" |

    Le proprietà di sistema del messaggio hanno come prefisso il simbolo `$`.

  - Corpo
        
    Questa sezione include tutte le modifiche apportate al dispositivo gemello in formato JSON. Usa lo stesso formato di una patch, con la differenza che può contenere tutte le sezioni, ovvero tag, properties.reported e properties.desired, e che contiene gli elementi "$metadata". Ad esempio,

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Tutte le operazioni precedenti supportano la [concorrenza ottimistica](iot-hub-devguide-device-twins.md#optimistic-concurrency) e richiedono l'autorizzazione **ServiceConnect**, come indicato nell'articolo [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md).

Oltre a queste operazioni, il back-end della soluzione può eseguire una query sui dispositivi gemelli usando un [linguaggio di query dell'hub IoT](iot-hub-devguide-query-language.md) simile a SQL.

## <a name="module-operations"></a>Operazioni sui moduli

L'app per modulo opera sul modulo gemello usando le seguenti operazioni atomiche:

* **Recupero del modulo gemello**. Questa operazione restituisce il documento del modulo gemello, inclusi tag e proprietà di sistema desiderate e segnalate, del modulo attualmente connesso.

* **Aggiornamento parziale delle proprietà segnalate**. Questa operazione consente l'aggiornamento parziale delle proprietà segnalate del modulo attualmente connesso. Questa operazione usa lo stesso formato di aggiornamento JSON che il back-end della soluzione usa per un aggiornamento parziale delle proprietà desiderate.

* **Osservazione di proprietà desiderate**. Il modulo attualmente connesso può scegliere di ricevere la notifica degli aggiornamenti delle proprietà desiderate quando vengono eseguiti. Il modulo riceve lo stesso modulo di aggiornamento che segnala la sostituzione parziale o completa eseguita dal back-end della soluzione.

Tutte le operazioni precedenti richiedono l'autorizzazione **ModuleConnect**, come definito nell'articolo [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md).

[Azure IoT SDK per dispositivi](iot-hub-devguide-sdks.md) semplifica l'uso delle operazioni precedenti con linguaggi e piattaforme diversi.

## <a name="tags-and-properties-format"></a>Formato di tag e proprietà

I tag e le proprietà desiderate e segnalate sono oggetti JSON soggetti alle restrizioni indicate di seguito:

* Tutte le chiavi negli oggetti JSON sono stringhe UTF-8 UNICODE da 64 caratteri con distinzione tra maiuscole e minuscole. I caratteri consentiti escludono i caratteri di controllo UNICODE (segmenti C0 e C1) e `.`, SP e `$`.

* Tutti i valori negli oggetti JSON possono essere dei seguenti tipi JSON: booleano, numero, stringa, oggetto. Non sono consentite le matrici. Il valore massimo per il numero intero è 4503599627370495, mentre quello minimo è -4503599627370496.

* Tutti gli oggetti JSON nei tag e nelle proprietà desiderate e segnalate possono avere una profondità massima di 5. Ad esempio, l'oggetto seguente è valido:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Tutti i valori di stringa possono avere una lunghezza massima di 512 byte.

## <a name="module-twin-size"></a>Dimensioni del modulo gemello

L'hub Internet delle cose impone un limite di dimensioni di 8 KB per il valore di `tags`e una dimensione di 32 KB limite ogni sul valore di `properties/desired` e `properties/reported`. Questi totali sono esclusivi degli elementi di sola lettura.

Le dimensioni vengono calcolate contando tutti i caratteri a esclusione dei caratteri di controllo UNICODE, ovvero i segmenti C0 e C1, e gli spazi al di fuori delle costanti stringa.

L'hub IoT rifiuta con errore tutte le operazioni che aumentano le dimensioni dei documenti oltre il limite specificato.

## <a name="module-twin-metadata"></a>Metadati del modulo gemello

L'hub IoT conserva il timestamp dell'ultimo aggiornamento di ogni oggetto JSON nelle proprietà desiderate e segnalate del modulo gemello. I timestamp sono in formato UTC e codificati in formato [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)`YYYY-MM-DDTHH:MM:SS.mmmZ`.
ad esempio:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Queste informazioni vengono mantenute a ogni livello (non solo al livello foglia della struttura JSON) per conservare gli aggiornamenti che rimuovono le chiavi dell'oggetto.

## <a name="optimistic-concurrency"></a>Concorrenza ottimistica

I tag e le proprietà desiderate e segnalate supportano la concorrenza ottimistica.
I tag sono dotati di un ETag, come indicato in [RFC7232](https://tools.ietf.org/html/rfc7232), che rappresenta il JSON del tag. Per garantire la coerenza è possibile usare l'ETag nelle operazioni di aggiornamento condizionale dal back-end della soluzione.

Le proprietà desiderate e segnalate del modulo gemello non sono dotate di ETag, ma hanno un valore `$version` sempre incrementale. In modo analogo a un valore ETag, la versione può essere usata dall'entità di aggiornamento per garantire la coerenza degli aggiornamenti. Ad esempio, un'app per modulo per una proprietà segnalata o il back-end della soluzione per una proprietà desiderata.

Le versioni sono utili anche quando un agente di osservazione, ad esempio l'app per modulo che osserva le proprietà desiderate, deve riconciliare le concorrenze tra il risultato di un'operazione di recupero e una notifica di aggiornamento. Altre informazioni sono disponibili nella sezione [Flusso di riconnessione del dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow). 

## <a name="next-steps"></a>Passaggi successivi

Per provare alcuni dei concetti descritti in questo articolo, vedere le esercitazioni sull'hub IoT seguenti:

* [Introduzione all'identità del modulo e ai moduli gemelli dell'hub IoT usando il back-end .NET e i dispositivi .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
