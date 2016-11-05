---
title: 'Guida per gli sviluppatori: metodi diretti | Microsoft Docs'
description: 'Guida per gli sviluppatori dell''hub IoT di Azure: usare metodi diretti per richiamare il codice nei dispositivi'
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: nberdy

---
# <a name="invoke-a-direct-method-on-a-device-preview"></a>Richiamare un metodo diretto in un dispositivo (anteprima)
## <a name="overview"></a>Panoramica
L'hub IoT offre la possibilità di richiamare metodi nei dispositivi dal cloud. I metodi rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, dato che dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente per far conoscere all'utente lo stato della chiamata. Questo risulta utile in scenari in cui l'azione immediata da intraprendere varia a seconda che il dispositivo sia riuscito o meno a rispondere. Un esempio è rappresentato dall'invio di un SMS di riattivazione a un dispositivo offline, in cui l'invio di un SMS ha un costo maggiore rispetto a una chiamata a un metodo.

Un metodo è paragonabile a una chiamata di procedura remota che raggiunge direttamente il dispositivo. Dal cloud è possibile chiamare solo i metodi che sono stati implementati in un dispositivo. Se il cloud prova a richiamare un metodo in un dispositivo in cui tale metodo non è definito, la chiamata al metodo ha esito negativo.

Ogni metodo del dispositivo è destinato a un unico dispositivo. I [processi][lnk-devguide-jobs] permettono di richiamare i metodi in più dispositivi e di inserire in coda la chiamata al metodo per i dispositivi disconnessi.

Chiunque abbia autorizzazioni di **connessione servizio** per l'hub IoT può richiamare un metodo in un dispositivo.

### <a name="when-to-use"></a>Quando usare la funzionalità
I metodi dei dispositivi sono simili ai [messaggi da cloud a dispositivo][file lnk-devguide-messaggi] per il fatto che entrambi consentono al back-end cloud di passare informazioni a un dispositivo, ma presentano alcune differenze fondamentali. A livello concettuale, i metodi sono sincroni e non durevoli, mentre i messaggi da cloud a dispositivo sono asincroni e hanno una durabilità fino a 48 ore.

I metodi seguono un modello richiesta-risposta e non sono durevoli. La mancanza di durabilità offre due vantaggi immediati nell'invio di comandi a dispositivi:

* **Commenti immediati sull'esecuzione del metodo**, che eliminano la necessità di gestire la correlazione tra richiesta e risposta.
* **Velocità effettiva più elevata**, che permette di eseguire le operazioni più velocemente dato che l'hub IoT non dà alcuna durabilità. Rispetto ai messaggi da cloud a dispositivo, l'hub IoT consente un numero maggiore di chiamate al metodo per ogni unità.

I messaggi da cloud a dispositivo non sono necessariamente comandi per il dispositivo. Rappresentano piuttosto un passaggio di informazioni da un servizio cloud al dispositivo, che può acquisirle in qualsiasi momento e rispondere o meno. I messaggi da cloud a dispositivo hanno un periodo di timeout più lungo, fino a 48 ore, mentre i metodi scadono molto più rapidamente.

Usare i metodi dei dispositivi per richiamare immediatamente i comandi in un dispositivo. Usare i processi per pianificare la chiamata di comandi in un dispositivo.

## <a name="method-lifecycle"></a>Ciclo di vita dei metodi
I metodi vengono implementati nel dispositivo. Per creare correttamente un'istanza possono essere necessari zero o più input nel payload del metodo. Per richiamare un metodo diretto è possibile usare un URI per il servizio (`{iot hub}/twins/{device id}/methods/`). Il dispositivo riceve i metodi diretti tramite un argomento MQTT specifico del dispositivo (`$iothub/methods/POST/{method name}/`). In futuro potranno essere supportati metodi su altri protocolli di rete sul lato dispositivo.

> [!NOTE]
> Quando si richiama un metodo diretto in un dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici stampabili US-ASCII, ad eccezione dei seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

I metodi sono sincroni e possono solo avere esito positivo o negativo dopo il periodo di timeout. Il valore predefinito è 30 secondi, ma il valore massimo impostabile è 3600 secondi. Risultano utili negli scenari interattivi in cui si vuole che il dispositivo agisca esclusivamente se è online e riceve comandi, ad esempio nel caso dell'accensione di una luce da un telefono. In questi scenari l'esito positivo o negativo deve essere immediato, in modo che il servizio cloud possa agire in base al risultato il prima possibile. Il dispositivo può restituire un corpo del messaggio come risultato del metodo, ma non è necessario che il metodo esegua questa operazione. Nelle chiamate ai metodi non esiste alcuna garanzia di ordinamento o semantica di concorrenza.

Le chiamate ai metodi dei dispositivi sono solo HTTP dal lato cloud e solo MQTT dal lato dispositivo.

## <a name="reference"></a>Riferimento
### <a name="servicefacing"></a>Per il servizio
#### <a name="method-invocation"></a>Chiamata al metodo
Le chiamate a metodi diretti in un dispositivo sono chiamate HTTP e includono:

* *URI* specifico del dispositivo (`{iot hub}/twins/{device id}/methods/`)
* *Metodo* POST
* *Intestazioni* contenenti l'autorizzazione, l'ID richiesta, il tipo di contenuto e la codifica del contenuto
* *Corpo* JSON trasparente nel formato seguente:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Il timeout è espresso in secondi. Se il timeout non è impostato, il valore predefinito è 30 secondi.

#### <a name="response"></a>Response
Il back-end riceve una risposta che include:

* *Codice di stato HTTP*, usato per errori provenienti dall'hub IoT, incluso un errore 404 per i dispositivi attualmente non connessi
* *Intestazioni* contenenti l'ETag, l'ID richiesta, il tipo di contenuto e la codifica del contenuto
* *Corpo* JSON nel formato seguente:

```
{
    "status" : "OK",
    "payload" : {...}
}
```

   Sia `status` che `body` vengono forniti dal dispositivo e usati per rispondere con la descrizione e/o il codice di stato del dispositivo.

### <a name="devicefacing"></a>Per il dispositivo
#### <a name="method-invocation"></a>Chiamata al metodo
I dispositivi ricevono richieste di metodi diretti nell'argomento MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

Il corpo ricevuto dal dispositivo è nel formato seguente:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Le richieste di metodo sono QoS 0.

#### <a name="response"></a>Response
Il dispositivo invia risposte a `$iothub/methods/res/{status}/?$rid={request id}`, in cui:

* La proprietà `status` è lo stato di esecuzione del metodo fornito dal dispositivo.
* La proprietà `$rid` è l'ID richiesta della chiamata al metodo ricevuta dall'hub IoT.

Il corpo è impostato dal dispositivo e accetta qualsiasi stato.

### <a name="additional-reference-material"></a>Materiale di riferimento
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per sviluppatori:

* [IoT Hub endpoints][lnk-endpoints] (Endpoint dell'hub IoT) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Throttling and quotas][lnk-quotas] (Quote e limitazione) descrive le quote applicabili al servizio Hub IoT e il comportamento di limitazione previsto quando si usa il servizio.
* [IoT Hub device and service SDKs][lnk-sdks] (SDK di servizi e dispositivi dell'hub IoT) elenca gli SDK nei diversi linguaggi da usare quando si sviluppano applicazioni per dispositivo e servizi che interagiscono con l'hub IoT.
* [Query language for twins, methods, and jobs][lnk-query] (Linguaggio di query per dispositivi gemelli, metodi e processi) descrive il linguaggio di query da usare per recuperare informazioni dall'hub IoT su dispositivi gemelli, metodi e processi.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come usare i metodi diretti, è possibile vedere un altro argomento di interesse reperibile nella Guida per sviluppatori:

* [Schedule jobs on multiple devices][lnk-devguide-jobs] (Pianificare processi in più dispositivi)

Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Use cloud-to-device methods][lnk-methods-tutorial] (Usare metodi da cloud a dispositivo)

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[file lnk-devguide-messaggi]: iot-hub-devguide-messaging.md



<!---HONumber=Oct16_HO2-->


