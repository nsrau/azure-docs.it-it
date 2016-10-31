<properties
 pageTitle="Guida per gli sviluppatori - Informazioni sui dispositivi gemelli | Microsoft Azure"
 description="Guida per gli sviluppatori dell'hub IoT di Azure - Utilizzo di dispositivi gemelli per sincronizzare stato e dati di configurazione tra l'hub IoT e i dispositivi"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>


# <a name="understand-device-twins---preview"></a>Informazioni sui dispositivi gemelli - anteprima

## <a name="overview"></a>Panoramica

I *dispositivi gemelli* sono documenti JSON nei quali vengono archiviate informazioni sullo stato del dispositivo (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT. In questo articolo verrà illustrato quanto segue:

* la struttura del dispositivo gemello: *tag*, proprietà *desiderate* e *segnalate*, e
* le operazioni che le app per i dispositivi e i back-end possono eseguire sui dispositivi gemelli.

> [AZURE.NOTE] Al momento i dispositivi gemelli sono accessibili solo dai dispositivi che si connettono all'hub IoT tramite il protocollo MQTT. Per istruzioni su come convertire l'app del dispositivo esistente affinché usi MQTT, leggere l'articolo [Supporto di MQTT][lnk-devguide-mqtt].

### <a name="when-to-use"></a>Quando usare i dispositivi gemelli

Usare i dispositivi gemelli per:

* Archiviare i metadati specifici del dispositivo nel cloud, ad esempio la località di distribuzione di un distributore automatico.
* Segnalare le informazioni di stato correnti, ad esempio capacità e condizioni disponibili dell'app per il dispositivo, come nel caso di un dispositivo che si connette tramite rete dati o WiFi.
* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata tra app per dispositivi e back-end, ad esempio un back-end che specifica la nuova versione del firmware da installare e l'app del dispositivo che segnala le varie fasi del processo di aggiornamento.
* Eseguire query relative a metadati, configurazione o stato del dispositivo.

Usare [messaggi dal dispositivo al cloud][lnk-d2c] per le sequenze di eventi con timestamp, ad esempio la serie temporale dei dati o degli avvisi del sensore. Usare metodi da [cloud a dispositivo][lnk-methods] per il controllo interattivo dei dispositivi, ad esempio l'accensione di una ventola.

## <a name="device-twins"></a>Dispositivi gemelli

I dispositivi gemelli consentono di archiviare informazioni sul dispositivo che possono essere usate:

- Dal dispositivo e dai back-end per sincronizzare condizioni e configurazione del dispositivo.
- Dal back-end applicazioni per eseguire query e come destinazione delle operazioni a esecuzione prolungata.

Il ciclo di vita di un dispositivo gemello è correlato all'[identità del dispositivo]corrispondente [lnk-identity]. I gemelli vengono creati ed eliminati implicitamente quando una nuova identità del dispositivo viene creata o eliminata nell'hub IoT.

Un dispositivo gemello è un documento JSON che include:

* **Tag**. Un documento JSON letti e scritto dal back-end. I tag non sono visibili alle applicazioni per dispositivi.
* **Proprietà desiderate**. Sono usate insieme alle proprietà segnalate per sincronizzare la configurazione o la condizione del dispositivo. Le proprietà desiderate possono essere impostate solo dal back-end applicazione e possono essere lette dall'app per dispositivo. L'app per dispositivo può anche ricevere in tempo reale le notifiche relative alle modifiche apportate alle proprietà desiderate.
* **Proprietà segnalate**. Usate insieme alle proprietà desiderate per sincronizzare la configurazione o la condizione del dispositivo. Le proprietà segnalate possono essere impostate solo dall'app del dispositivo e possono essere lette e sottoposte a query dall'applicazione back-end.

La root del dispositivo gemello contiene le proprietà di sola lettura dell'identità corrispondente, le stesse contenute nel [Registro delle identità dei dispositivi](lnk-identity).

![][img-twin]

Di seguito è riportato un esempio di documento JSON del dispositivo gemello:

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

Nell'oggetto radice si trovano le proprietà di sistema e gli oggetti contenitore per `tags`, `reported` e `desired properties`. Nel contenitore `properties` sono presenti alcuni elementi di sola lettura (`$metadata`, `$etag` e `$version`) descritti rispettivamente nelle sezioni relative ai [metadati gemelli][lnk-twin-metadata] e alla [concorrenza ottimistica][lnk-concurrency].

### <a name="reported-property-example"></a>Esempio di proprietà segnalata

Nell'esempio precedente, il dispositivo gemello contiene la proprietà `batteryLevel` che viene segnalata dall'app per dispositivo. Questa proprietà consente di eseguire una query e di far funzionare i dispositivi in base al livello di carica della batteria più recente segnalato. In un altro esempio l'applicazione segnala la capacità o le opzioni di connettività del dispositivo.

Si noti come le proprietà segnalate semplificano gli scenari in cui il back-end è interessato all'ultimo valore noto di una proprietà. Usare i [messaggi dal dispositivo al cloud][lnk-d2c] se il back-end deve elaborare la telemetria del dispositivo in forma di sequenze di eventi con timestamp, ad esempio una serie temporale.

### <a name="desired-configuration-example"></a>Esempio di configurazione di una proprietà desiderata

Nell'esempio precedente, le proprietà `telemetryConfig` desiderata e segnalata vengono usate dal back-end e dall'applicazione per dispositivo per sincronizzare la configurazione della telemetria di questo dispositivo. Ad esempio:

1. Il back-end applicazioni imposta la proprietà desiderata sul valore di configurazione desiderato. Questa è la parte del documento con la proprietà desiderata:

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

3. Il back-end applicazioni può tenere traccia dei risultati dell'operazione di configurazione su più dispositivi, eseguendo [query][lnk-query] sui dispositivi gemelli.

> [AZURE.NOTE] I frammenti di codice precedenti sono esempi ottimizzati per una migliore leggibilità, di un possibile modo per codificare una configurazione del dispositivo e il relativo stato. L'hub IoT non impone uno schema specifico per l'uso delle proprietà desiderate e segnalate nei dispositivi gemelli.

Spesso i gemelli vengono usati per sincronizzare le operazioni a esecuzione prolungata, ad esempio gli aggiornamenti del firmware. Fare riferimento a [Usare le proprietà desiderate per configurare i dispositivi ][lnk-twin-properties] per altre informazioni su come usare le proprietà per sincronizzare e tenere traccia delle operazioni a esecuzione prolungata sui dispositivi.

## <a name="back-end-operations"></a>Operazioni del back-end

Il back-end opera sul gemello tramite le seguenti operazioni atomiche esposte tramite HTTP:

1. **Recupero del gemello tramite ID**. Questa operazione restituisce il contenuto del documento del gemello, inclusi tag e proprietà desiderate, segnalate e di sistema.
2. **Aggiornamento parziale del gemello**. Questa operazione consente al back-end di aggiornare parzialmente i tag o le proprietà desiderate del gemello. L'aggiornamento parziale è espresso come documento JSON che aggiunge o aggiorna tutte le proprietà indicate. Le proprietà impostate su `null` vengono rimosse. L'esempio seguente crea una nuova proprietà desiderata con valore `{"newProperty": "newValue"}`, sostituisce il valore esistente di `existingProperty` con `"otherNewValue"`, e rimuove completamente `otherOldProperty`. Non vengono modificate le altre proprietà desiderate o tag esistenti:

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

3. **Sostituzione di proprietà desiderate**. Questa operazione consente al back-end di sovrascrivere completamente tutte le proprietà desiderate esistenti e di sostituirle con un nuovo documento JSON `properties/desired`.
4. **Sostituzione di tag**. Analogamente alla sostituzione delle proprietà desiderate, questa operazione consente al back-end di sovrascrivere completamente tutte le tag esistenti e di sostituirle con un nuovo documento JSON `tags`.

Tutte le operazioni precedenti supportano la [concorrenza ottimistica][lnk-concurrency] e richiedono l'autorizzazione **ServiceConnect**, come indicato nell'articolo relativo alla [sicurezza][lnk-security].

Oltre a queste operazioni, il back-end può eseguire una query sui gemelli usando un [linguaggio di query][lnk-query] simile a SQL e può anche eseguire operazioni su grandi insiemi di gemelli usando i [processi][lnk-jobs].

## <a name="device-operations"></a>Operazioni del dispositivo

Il dispositivo opera sul gemello usando le seguenti operazioni atomiche:

1. **Recupero del gemello**. Questa operazione restituisce il contenuto del documento del gemello, inclusi tag e proprietà desiderate, segnalate e di sistema, del dispositivo attualmente connesso.
2. **Aggiornamento parziale delle proprietà segnalate**. Questa operazione consente l'aggiornamento parziale delle proprietà segnalate del dispositivo attualmente connesso. Usa lo stesso formato di aggiornamento JSON del back-end che esegue l'aggiornamento parziale delle proprietà desiderate.
3. **Osservazione di proprietà desiderate**. Il dispositivo attualmente connesso può ricevere la notifica degli aggiornamenti delle proprietà desiderate non appena vengono eseguiti. Il dispositivo riceve lo stesso modulo di aggiornamento che segnala la sostituzione parziale o completa eseguita dal back-end.

Tutte le operazioni precedenti richiedono l'autorizzazione **DeviceConnect** come indicato nell'articolo sulla [sicurezza][lnk-security].

[Azure IoT SDK per dispositivi][lnk-sdks] semplifica l'uso delle operazioni precedenti con linguaggi e piattaforme diversi. Altre informazioni sulle primitive dell'hub IoT per la sincronizzazione delle proprietà desiderate sono reperibili nella sezione [Flusso di riconnessione del dispositivo][lnk-reconnection].

> [AZURE.NOTE] Al momento i dispositivi gemelli sono accessibili solo dai dispositivi che si connettono all'hub IoT tramite il protocollo MQTT.

## <a name="reference"></a>riferimento

### <a name="tags-and-properties-format"></a>Formato di tag e proprietà

I tag e le proprietà desiderate e segnalate sono oggetti JSON soggetti alle restrizioni indicate di seguito:

* Tutte le chiavi negli oggetti JSON sono stringhe UNICODE da 128 caratteri con distinzione tra maiuscole e minuscole. I caratteri consentiti escludono i caratteri di controllo UNICODE (segmenti C0 e C1) e `'.'`, `' '`, `'$'`.
* Tutti i valori nell'oggetto JSON possono essere dei seguenti tipi JSON: booleano, numero, stringa, oggetto. Non sono consentite le matrici.

### <a name="twin-size"></a>Dimensione dei gemelli

L'hub IoT impone un limite di dimensione pari a 8 KB sui valori di `tags`, `properties/desired` e `properties/reported`, a esclusione degli elementi di sola lettura.
Le dimensioni vengono calcolate contando tutti i caratteri a esclusione dei caratteri di controllo UNICODE (segmenti C0 e C1) e lo spazio `' '` quando è visualizzato al di fuori di una costante di tipo stringa.
L'hub IoT rifiuta con errore tutte le operazioni che aumentano le dimensioni dei documenti oltre al limite specificato.

### <a name="twin-metadata"></a>Metadati del gemello

L'hub IoT conserva il timestamp dell'ultimo aggiornamento di ogni oggetto JSON nelle proprietà desiderate e segnalate. I timestamp sono in formato UTC e codificati in formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

### <a name="optimistic-concurrency"></a>Concorrenza ottimistica

Le tag e le proprietà desiderate e segnalate supportano la concorrenza ottimistica.
I tag sono dotati di un eTag, come indicato in [RFC7232], che rappresenta il JSON del tag. Per garantire la coerenza è possibile usare l'eTag nelle operazioni di aggiornamento condizionale dal back-end.

Le proprietà desiderate e segnalate non sono dotate di eTag, ma hanno un valore `$version` sempre incrementale. Analogamente a un eTag, la versione può essere usata dall'elemento che esegue l'aggiornamento, ad esempio l'app per dispositivo di una proprietà segnalata o il back-end di una proprietà desiderata, per imporre la coerenza degli aggiornamenti.

Sono inoltre utili quando un agente di osservazione, ad esempio l'app per dispositivo che osserva le proprietà desiderate, deve riconciliare le concorrenze tra il risultato di un'operazione di recupero e una notifica di aggiornamento. Altre informazioni sono reperibili nella sezione [Flusso di riconnessione del dispositivo][lnk-reconnection].

### <a name="device-reconnection-flow"></a>Flusso di riconnessione del dispositivo

L'hub IoT non conserva le notifiche di aggiornamento delle proprietà desiderate dei dispositivi connessi. Ne consegue che un dispositivo che esegue la connessione deve recuperare il documento completo delle proprietà desiderate, ed eseguire la sottoscrizione alle notifiche di aggiornamento. Data la possibilità di concorrenza tra le notifiche di aggiornamento e il recupero completo, deve essere assicurato il flusso seguente:

1. L'app per dispositivo esegue la connessione a un hub IoT.
2. L'app per dispositivo esegue la sottoscrizione per le notifiche di aggiornamento delle proprietà desiderate.
3. L'app per dispositivo recupera il documento completo delle proprietà desiderate.

L'app per dispositivo è in grado di ignorare tutte le notifiche con il valore di `$version` minore o uguale a quello dell'intero documento recuperato. Ciò è possibile poiché hub IoT garantisce l'incremento delle versioni.

> [AZURE.NOTE] Questa logica è già implementata in [Azure IoT SDK per dispositivi][lnk-sdks]. La descrizione è utile solo se l'app per dispositivo non può usare un Azure IoT SDK per dispositivi e deve programmare direttamente l'interfaccia MQTT.

### <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per sviluppatori:

- La sezione [Endpoint hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
- La sezione [Quote e limitazione][lnk-quotas] descrive le quote applicabili al servizio hub IoT e il comportamento di limitazione previsto quando si usa il servizio.
- La sezione [SDK di servizi e dispositivi dell'hub IoT][lnk-sdks] elenca gli SDK nei diversi linguaggi da usare quando si sviluppano applicazioni per dispositivo e servizi che interagiscono con l'hub IoT.
- La sezione relativa al [linguaggio di query per gemelli, metodi e processi][lnk-query] descrive il linguaggio di query da usare per recuperare informazioni da hub IoT su dispositivi gemelli, metodi e processi.
- La sezione [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] fornisce altre informazioni sul supporto di hub IoT al protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare i dispositivi gemelli. Altri argomenti di interesse reperibili nella Guida per sviluppatori:

- [Richiamare un metodo diretto su un dispositivo][lnk-methods]
- [Pianificare processi in più dispositivi][lnk-jobs]

Per provare alcuni dei concetti descritti in questo articolo, possono essere utili le esercitazioni di hub IoT seguenti:

- [Come usare il dispositivo gemello][lnk-twin-tutorial]
- [Come usare le proprietà dei dispositivi gemelli][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png


<!---HONumber=Oct16_HO2-->


