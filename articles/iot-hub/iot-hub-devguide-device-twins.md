---
title: Informazioni sui dispositivi gemelli nell'hub IoT di Azure | Documentazione Microsoft
description: Guida per gli sviluppatori - Usare dispositivi gemelli per sincronizzare stato e dati di configurazione tra l'hub IoT e i dispositivi
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 454eb7b1f4f48e8a2a78bd3fcb6eb03b6097d44d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Comprendere e usare dispositivi gemelli nell'hub IoT
## <a name="overview"></a>Panoramica
I *dispositivi gemelli* sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT. L'articolo illustra:

* La struttura del dispositivo gemello: *tag*, proprietà *desiderate* e *segnalate*.
* Le operazioni che le app per dispositivo e i back-end possono eseguire sui dispositivi gemelli.


### <a name="when-to-use"></a>Quando usare le autorizzazioni
Usare i dispositivi gemelli per:

* Archiviare i metadati specifici del dispositivo nel cloud, ad esempio il percorso di distribuzione di un distributore automatico.
* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni dall'app per dispositivo. Ad esempio, un dispositivo è connesso all'hub IoT mediante il cellulare o il Wi-Fi.
* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata tra l'app per dispositivi e back-end, ad esempio quando il back-end della soluzione specifica la nuova versione del firmware da installare e l'app per dispositivo segnala le varie fasi del processo di aggiornamento.
* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

Vedere [Indicazioni sulle comunicazioni da dispositivo a cloud][lnk-d2c-guidance] per informazioni sull'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.
Vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance] per informazioni sull'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="device-twins"></a>Dispositivi gemelli
I dispositivi gemelli consentono di archiviare informazioni sul dispositivo che possono essere usate:

* Dal dispositivo e dai back-end per sincronizzare condizioni e configurazione del dispositivo.
* Dal back-end della soluzione per eseguire query e come destinazione delle operazioni a esecuzione prolungata.

Il ciclo di vita di un dispositivo gemello è correlato all'[identità del dispositivo][lnk-identity] corrispondente. I dispositivi gemelli vengono creati ed eliminati implicitamente quando viene creata o eliminata una nuova identità del dispositivo nell'hub IoT.

Un dispositivo gemello è un documento JSON che include:

* **Tag**. Una sezione del documento JSON che il back-end della soluzione è in grado di leggere e in cui può scrivere. I tag non sono visibili alle applicazioni per dispositivi.
* **Proprietà desiderate**. Sono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del dispositivo. Le proprietà desiderate possono essere impostate solo dal back-end della soluzione e possono essere lette dall'app per dispositivo. L'app per dispositivo può anche ricevere in tempo reale le notifiche relative alle modifiche apportate alle proprietà desiderate.
* **Proprietà segnalate**. Sono usate insieme alle proprietà desiderate per sincronizzare la configurazione o le condizioni del dispositivo. Le proprietà segnalate possono essere impostate solo dall'app per dispositivo e possono essere lette e sottoposte a query dal back-end della soluzione.

La radice del documento JSON del dispositivo gemello, inoltre, contiene le proprietà di sola lettura dell'identità di dispositivo corrispondente archiviata nel [registro delle identità][lnk-identity].

![][img-twin]

L'esempio seguente illustra un documento JSON del dispositivo gemello:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

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
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

Nell'oggetto radice si trovano le proprietà di sistema e gli oggetti contenitore per `tags` ed entrambe le proprietà `reported` e `desired`. Nel contenitore `properties` sono presenti alcuni elementi di sola lettura (`$metadata`, `$etag` e `$version`) descritti nelle sezioni [Metadati del dispositivo gemello][lnk-twin-metadata] e [Concorrenza ottimistica][lnk-concurrency].

### <a name="reported-property-example"></a>Esempio di proprietà segnalata
Nell'esempio precedente, il dispositivo gemello contiene la proprietà `batteryLevel` che viene segnalata dall'app per dispositivo. Questa proprietà consente di eseguire una query e di far funzionare i dispositivi in base al livello di carica della batteria più recente segnalato. Altri esempi sono l'app per dispositivo che segnala le funzionalità o le opzioni di connettività del dispositivo.

> [!NOTE]
> Le proprietà segnalate semplificano gli scenari in cui il back-end della soluzione è interessato all'ultimo valore noto di una proprietà. Usare i [messaggi dal dispositivo al cloud][lnk-d2c] se il back-end della soluzione deve elaborare la telemetria del dispositivo in forma di sequenze di eventi con timestamp, ad esempio una serie temporale.

### <a name="desired-property-example"></a>Esempio di proprietà desiderata
Nell'esempio precedente le proprietà desiderate e segnalate del dispositivo gemello `telemetryConfig` vengono usate dal back-end della soluzione e dall'app per dispositivo per sincronizzare la configurazione della telemetria per questo dispositivo. Ad esempio:

1. Il back-end della soluzione imposta la proprietà desiderata sul valore di configurazione desiderato. Questa è la parte del documento con il set di proprietà desiderate:
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. L'applicazione per dispositivo riceve notifica della modifica immediatamente se connessa o alla prima riconnessione. L'app segnala quindi la configurazione aggiornata o una condizione di errore riscontrata nell'uso della proprietà `status`. Questa è la parte con le proprietà segnalate:
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. Il back-end della soluzione può tenere traccia dei risultati dell'operazione di configurazione su più dispositivi, eseguendo [query][lnk-query] sui dispositivi gemelli.

> [!NOTE]
> I frammenti di codice precedenti sono esempi ottimizzati per una migliore leggibilità, di un modo per codificare una configurazione del dispositivo e il relativo stato. L'hub IoT non impone uno schema specifico per l'uso delle proprietà desiderate e segnalate del dispositivo gemello nei dispositivi gemelli.
> 
> 

È possibile usare i gemelli per sincronizzare le operazioni a esecuzione prolungata, ad esempio gli aggiornamenti del firmware. Per altre informazioni su come usare le proprietà per sincronizzare e tenere traccia dell'operazione a esecuzione prolungata sui dispositivi, vedere [Usare le proprietà desiderate per configurare i dispositivi][lnk-twin-properties].

## <a name="back-end-operations"></a>Operazioni di back-end
Il back-end della soluzione opera sul dispositivo gemello tramite le seguenti operazioni atomiche esposte tramite HTTPS:

1. **Recuperare un dispositivo gemello tramite ID**. Questa operazione restituisce il documento del dispositivo gemello, inclusi tag e proprietà desiderate, segnalate e di sistema.
2. **Aggiornare parzialmente il dispositivo gemello**. Questa operazione consente al back-end della soluzione di aggiornare parzialmente i tag o le proprietà desiderate di un dispositivo gemello. L'aggiornamento parziale è espresso come documento JSON che aggiunge o aggiorna tutte le proprietà. Le proprietà impostate su `null` vengono rimosse. L'esempio seguente crea una nuova proprietà desiderata con valore `{"newProperty": "newValue"}`, sostituisce il valore esistente di `existingProperty` con `"otherNewValue"`, e rimuove `otherOldProperty`. Non vengono apportate altre modifiche alle altre proprietà desiderate o ai tag esistenti:
   
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
3. **Sostituzione di proprietà desiderate**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutte le proprietà desiderate esistenti e di sostituirle con un nuovo documento JSON `properties/desired`.
4. **Sostituzione di tag**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutti i tag esistenti e di sostituirli con un nuovo documento JSON `tags`.
5. **Ricezione di notifiche relative al dispositivo gemello**. Questa operazione invia notifiche al back-end della soluzione a ogni modifica del dispositivo gemello. A questo scopo, la soluzione IoT deve creare una route e impostare l'origine dati su *twinChangeEvents*. Per impostazione predefinita, non viene inviata alcuna notifica, ovvero queste route non sono preesistenti. Se la frequenza delle modifiche è troppo elevata o per altri motivi, come un errore interno, l'hub IoT potrebbe inviare solo una notifica che contiene tutte le modifiche. Di conseguenza, se l'applicazione ha bisogno di controllo e registrazione affidabili di tutti gli stati intermedi, è consigliabile continuare a usare i messaggi D2C. Il messaggio di notifica relativo al dispositivo gemello include le proprietà e il corpo.

    - Proprietà

    | Nome | Valore |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Data e ora in cui è stata inviata la notifica |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID del dispositivo |
    hubName | Nome dell'hub IoT |
    operationTimestamp | Timestamp [ISO8601] dell'operazione |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" o "updateTwin" |

    Le proprietà di sistema del messaggio hanno come prefisso il simbolo `'$'`.

    - Corpo
        
    Questa sezione include tutte le modifiche apportate al dispositivo gemello in formato JSON. Usa lo stesso formato di una patch, con la differenza che può contenere tutte le sezioni, ovvero tag, properties.reported e properties.desired, e che contiene gli elementi "$metadata". Ad esempio,
    ```
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

Tutte le operazioni precedenti supportano la [concorrenza ottimistica][lnk-concurrency] e richiedono l'autorizzazione **ServiceConnect**, come indicato nell'articolo [Sicurezza][lnk-security].

Oltre a queste operazioni, il back-end della soluzione può:

* Eseguire una query sui dispositivi gemelli usando il [linguaggio di query Hub IoT ][lnk-query] simile a SQL.
* Eseguire operazioni su set di grandi dimensioni dei dispositivi gemelli usando i [processi][lnk-jobs].

## <a name="device-operations"></a>Operazioni del dispositivo
Il dispositivo opera sul dispositivo gemello usando le seguenti operazioni atomiche:

1. **Recuperare un dispositivo gemello**. Questa operazione restituisce il documento del dispositivo gemello, inclusi tag e proprietà desiderate, segnalate e di sistema, del dispositivo attualmente connesso.
2. **Aggiornamento parziale delle proprietà segnalate**. Questa operazione consente l'aggiornamento parziale delle proprietà segnalate del dispositivo attualmente connesso. Questa operazione usa lo stesso formato di aggiornamento JSON che il back-end della soluzione usa per un aggiornamento parziale delle proprietà desiderate.
3. **Osservazione di proprietà desiderate**. Il dispositivo attualmente connesso può scegliere di ricevere la notifica degli aggiornamenti delle proprietà desiderate quando vengono eseguiti. Il dispositivo riceve lo stesso modulo di aggiornamento che segnala la sostituzione parziale o completa eseguita dal back-end della soluzione.

Tutte le operazioni precedenti richiedono l'autorizzazione **DeviceConnect**, come indicato nell'articolo [Sicurezza][lnk-security].

[Azure IoT SDK per dispositivi][lnk-sdks] semplifica l'uso delle operazioni precedenti con linguaggi e piattaforme diversi. Altre informazioni sulle primitive dell'hub IoT per la sincronizzazione delle proprietà desiderate sono reperibili nella sezione [Flusso di riconnessione del dispositivo][lnk-reconnection].


## <a name="reference-topics"></a>Argomenti di riferimento:
Gli argomenti di riferimento seguenti offrono altre informazioni sul controllo dell'accesso all'hub IoT.

## <a name="tags-and-properties-format"></a>Formato di tag e proprietà
I tag e le proprietà desiderate e segnalate sono oggetti JSON soggetti alle restrizioni indicate di seguito:

* Tutte le chiavi negli oggetti JSON sono stringhe UTF-8 UNICODE da 64 caratteri con distinzione tra maiuscole e minuscole. I caratteri consentiti escludono i caratteri di controllo UNICODE (segmenti C0 e C1) e `'.'`, `' '`, `'$'`.
* Tutti i valori negli oggetti JSON possono essere dei seguenti tipi JSON: booleano, numero, stringa, oggetto. Non sono consentite le matrici.
* Tutti gli oggetti JSON nei tag e nelle proprietà desiderate e segnalate possono avere una profondità massima di 5. Ad esempio, l'oggetto seguente è valido:

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

* Tutti i valori di stringa possono avere una lunghezza massima di 512 byte.

## <a name="device-twin-size"></a>Dimensioni del dispositivo gemello
L'hub IoT impone un limite di dimensione pari a 8 KB sui valori di `tags`, `properties/desired` e `properties/reported`, a esclusione degli elementi di sola lettura.
Le dimensioni vengono calcolate contando tutti i caratteri a esclusione dei caratteri di controllo UNICODE (segmenti C0 e C1) e lo spazio `' '` quando è visualizzato al di fuori di una costante di tipo stringa.
L'hub IoT rifiuta con errore tutte le operazioni che aumentano le dimensioni dei documenti oltre il limite specificato.

## <a name="device-twin-metadata"></a>Metadati del dispositivo gemello
L'hub IoT conserva il timestamp dell'ultimo aggiornamento di ogni oggetto JSON nelle proprietà desiderate e segnalate del dispositivo gemello. I timestamp sono in formato UTC e codificati in formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Ad esempio:

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
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
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

Queste informazioni vengono mantenute a ogni livello (non solo al livello foglia della struttura JSON) per conservare gli aggiornamenti che rimuovono le chiavi dell'oggetto.

## <a name="optimistic-concurrency"></a>Concorrenza ottimistica
I tag e le proprietà desiderate e segnalate supportano la concorrenza ottimistica.
I tag sono dotati di un ETag, come indicato in [RFC7232], che rappresenta il JSON del tag. Per garantire la coerenza è possibile usare l'ETag nelle operazioni di aggiornamento condizionale dal back-end della soluzione.

Le proprietà desiderate e segnalate del dispositivo gemello non sono dotate di ETags, ma hanno un valore `$version` sempre incrementale. In modo analogo a un valore ETag, la versione può essere usata dall'entità di aggiornamento per garantire la coerenza degli aggiornamenti. Ad esempio, un'app per dispositivo per una proprietà segnalata o la soluzione del back-end per una proprietà desiderata.

Le versioni sono utili anche quando un agente di osservazione, ad esempio l'app per dispositivo che osserva le proprietà desiderate, deve riconciliare le concorrenze tra il risultato di un'operazione di recupero e una notifica di aggiornamento. Altre informazioni sono reperibili nella sezione [Flusso di riconnessione del dispositivo][lnk-reconnection].

## <a name="device-reconnection-flow"></a>Flusso di riconnessione del dispositivo
L'hub IoT non conserva le notifiche di aggiornamento delle proprietà desiderate dei dispositivi connessi. Ne consegue che un dispositivo che esegue la connessione deve recuperare il documento completo delle proprietà desiderate, ed eseguire la sottoscrizione alle notifiche di aggiornamento. Data la possibilità di concorrenza tra le notifiche di aggiornamento e il recupero completo, deve essere assicurato il flusso seguente:

1. L'app per dispositivo esegue la connessione a un hub IoT.
2. L'app per dispositivo esegue la sottoscrizione per le notifiche di aggiornamento delle proprietà desiderate.
3. L'app per dispositivo recupera il documento completo delle proprietà desiderate.

L'app per dispositivo è in grado di ignorare tutte le notifiche con il valore di `$version` minore o uguale a quello dell'intero documento recuperato. Questo approccio è possibile poiché l'hub IoT garantisce l'incremento delle versioni.

> [!NOTE]
> Questa logica è già implementata in [Azure IoT SDK per dispositivi][lnk-sdks]. La descrizione è utile solo se l'app per dispositivo non può usare un Azure IoT SDK per dispositivi e deve programmare direttamente l'interfaccia MQTT.
> 
> 

## <a name="additional-reference-material"></a>Materiale di riferimento
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* L'articolo [IoT Hub endpoints][lnk-endpoints] (Endpoint dell'hub IoT) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* L'articolo [Quote e limitazioni][lnk-quotas] descrive le quote applicabili al servizio hub IoT e il comportamento di limitazione previsto quando si usa il servizio.
* L'articolo [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* L'articolo [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi][lnk-query] descrive il linguaggio di query dell’hub IoT che è possibile usare per recuperare informazioni dall’hub IoT sui processi e i dispositivi gemelli.
* L'articolo [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come usare i dispositivi gemelli. Altri argomenti di interesse disponibili nella Guida per sviluppatori dell'hub IoT:

* [Richiamare un metodo diretto in un dispositivo][lnk-methods]
* [Pianificare processi in più dispositivi][lnk-jobs]

Per provare alcuni dei concetti descritti in questo articolo, possono essere utili le esercitazioni di hub IoT seguenti:

* [Come usare il dispositivo gemello][lnk-twin-tutorial]
* [Come usare le proprietà del dispositivo gemello][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
