---
title: Informazioni sui metodi diretti dell'hub IoT di Azure | Documentazione Microsoft
description: 'Guida per gli sviluppatori: usare metodi diretti per richiamare il codice nei dispositivi da un''app di servizio.'
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 003b3f6ef8a6fbc1c6fcdfc58f7d35bf6c42c9ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Comprendere e richiamare metodi diretti dall'hub IoT
L'hub IoT offre la possibilità di richiamare metodi diretti nei dispositivi dal cloud. I metodi diretti rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, dato che dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente. Questo approccio è utile per gli scenari in cui la linea di condotta immediata è diversa a seconda che il dispositivo sia in grado di rispondere o meno, ad esempio nel caso dell'invio di un SMS di riattivazione a un dispositivo se questo è offline, dato che un SMS è più costoso rispetto alla chiamata a un metodo.
Ogni metodo del dispositivo è destinato a un unico dispositivo. I [processi][lnk-devguide-jobs] permettono di richiamare i metodi diretti in più dispositivi e di pianificare la chiamata al metodo per i dispositivi disconnessi.

Chiunque abbia autorizzazioni di **connessione servizio** per l'hub IoT può richiamare un metodo in un dispositivo.

I metodi diretti si basano su un modello di tipo richiesta-risposta e sono destinati a comunicazioni che necessitano di una conferma immediata del risultato, ad esempio il controllo interattivo del dispositivo, come l'accensione di un ventilatore.

Vedere [Cloud-to-device communication guidance][lnk-c2d-guidance] (Indicazioni sulla comunicazione da cloud a dispositivo) in caso di dubbi tra l'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="method-lifecycle"></a>Ciclo di vita dei metodi
I metodi diretti vengono implementati nel dispositivo. Per creare correttamente un'istanza possono essere necessari zero o più input nel payload del metodo. Per richiamare un metodo diretto è possibile usare un URI per il servizio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo riceve metodi diretti tramite un argomento MQTT specifico del dispositivo (`$iothub/methods/POST/{method name}/`) o tramite collegamenti AMQP (proprietà `IoThub-methodname` e `IoThub-status` dell'applicazione). 

> [!NOTE]
> Quando si richiama un metodo diretto in un dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici stampabili US-ASCII, ad eccezione dei seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

I metodi diretti sono sincroni e possono solo avere esito positivo o negativo dopo il periodo di timeout. Il valore predefinito è 30 secondi, ma il valore massimo impostabile è 3600 secondi. Risultano utili negli scenari interattivi in cui si vuole che il dispositivo agisca esclusivamente se è online e riceve comandi, ad esempio nel caso dell'accensione di una luce da un telefono. In questi scenari l'esito positivo o negativo deve essere immediato, in modo che il servizio cloud possa agire in base al risultato il prima possibile. Il dispositivo può restituire un corpo del messaggio come risultato del metodo, ma non è necessario che il metodo esegua questa operazione. Nelle chiamate ai metodi non esiste alcuna garanzia di ordinamento o semantica di concorrenza.

I metodi diretti supportano solo HTTPS lato cloud e solo MQTT o AMQP lato dispositivo.

Il payload per le richieste e le risposte del metodo è un documento JSON con dimensioni massime di 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Richiamare un metodo diretto da un'app back-end
### <a name="method-invocation"></a>Chiamata al metodo
Le chiamate a metodi diretti in un dispositivo sono chiamate HTTPS che includono:

* *URI* specifico del dispositivo (`{iot hub}/twins/{device id}/methods/`)
* *Metodo* POST
* *Intestazioni* contenenti l'autorizzazione, l'ID richiesta, il tipo di contenuto e la codifica del contenuto
* *Corpo* JSON trasparente nel formato seguente:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Il timeout è espresso in secondi. Se il timeout non è impostato, il valore predefinito è 30 secondi.

### <a name="response"></a>Risposta
L'app back-end riceve una risposta che include:

* *Codice di stato HTTP*, usato per errori provenienti dall'hub IoT, incluso un errore 404 per i dispositivi attualmente non connessi
* *Intestazioni* contenenti l'ETag, l'ID richiesta, il tipo di contenuto e la codifica del contenuto
* *Corpo* JSON nel formato seguente:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Sia `status` che `body` vengono forniti dal dispositivo e usati per rispondere con la descrizione e/o il codice di stato del dispositivo.

## <a name="handle-a-direct-method-on-a-device"></a>Gestire un metodo diretto in un dispositivo
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Chiamata al metodo
I dispositivi ricevono richieste di metodi diretti nell'argomento MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

Il corpo ricevuto dal dispositivo è nel formato seguente:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Le richieste di metodo sono QoS 0.

#### <a name="response"></a>Risposta
Il dispositivo invia risposte a `$iothub/methods/res/{status}/?$rid={request id}`, in cui:

* La proprietà `status` è lo stato di esecuzione del metodo fornito dal dispositivo.
* La proprietà `$rid` è l'ID richiesta della chiamata al metodo ricevuta dall'hub IoT.

Il corpo è impostato dal dispositivo e accetta qualsiasi stato.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Chiamata al metodo
Il dispositivo riceve richieste di metodi diretti tramite la creazione di un collegamento di ricezione sull'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Il messaggio AMQP arriva sul collegamento di ricezione che rappresenta la richiesta del metodo. Tale messaggio contiene quanto segue:
* La proprietà ID di correlazione, contenente un ID richiesta che deve essere passato con la relativa risposta del metodo
* Una proprietà dell'applicazione denominata `IoThub-methodname`, contenente il nome del metodo richiamato
* Il corpo del messaggio AMQP, contenente il payload del metodo in formato JSON

#### <a name="response"></a>Risposta
Il dispositivo crea un collegamento di invio per restituire la risposta del metodo all'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

La risposta del metodo viene restituita sul collegamento di invio e contiene gli elementi seguenti:
* La proprietà ID di correlazione, contenente l'ID richiesta passato nel messaggio di richiesta del metodo
* Una proprietà dell'applicazione denominata `IoThub-status`, contenente lo stato del metodo fornito dall'utente
* Il corpo del messaggio AMQP, contenente la risposta del metodo in formato JSON

## <a name="additional-reference-material"></a>Materiale di riferimento
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Limitazione e quote][lnk-quotas] descrive le quote applicabili al comportamento di limitazione previsto quando si usa l'hub IoT.
* [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* [Il linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing messaggi][lnk-query] descrive il linguaggio di query dell'hub IoT che è possibile usare per recuperare informazioni dall'hub IoT sui processi e i dispositivi gemelli.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come usare i metodi diretti, è possibile vedere un altro articolo di interesse della Guida per sviluppatori dell'hub IoT:

* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Usare metodi diretti][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
