---
title: Informazioni sui metodi diretti dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: usare metodi diretti per richiamare il codice nei dispositivi da un'app di servizio."
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: d7c63ffe5a318507053f59bf3a18242ee8c327a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61327755"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Comprendere e richiamare metodi diretti dall'hub IoT

L'hub IoT offre la possibilità di richiamare metodi diretti nei dispositivi dal cloud. I metodi diretti rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, dato che dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente. Questo approccio è utile per gli scenari in cui la linea di condotta immediata è diversa a seconda che il dispositivo sia in grado di rispondere o meno,

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ogni metodo del dispositivo è destinato a un unico dispositivo. In [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md) viene descritto come fornire un modo per richiamare i metodi diretti in più dispositivi e di pianificare la chiamata al metodo per i dispositivi disconnessi.

Chiunque abbia autorizzazioni di **connessione servizio** per l'hub IoT può richiamare un metodo in un dispositivo.

I metodi diretti si basano su un modello di tipo richiesta-risposta e sono destinati a comunicazioni che necessitano di una conferma immediata del risultato, ad esempio il controllo interattivo del dispositivo, come l'accensione di un ventilatore.

Vedere [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo) in caso di dubbi tra l'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="method-lifecycle"></a>Ciclo di vita dei metodi

I metodi diretti vengono implementati nel dispositivo. Per creare correttamente un'istanza possono essere necessari zero o più input nel payload del metodo. Per richiamare un metodo diretto è possibile usare un URI per il servizio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo riceve metodi diretti tramite un argomento MQTT specifico del dispositivo (`$iothub/methods/POST/{method name}/`) o tramite collegamenti AMQP (proprietà `IoThub-methodname` e `IoThub-status` dell'applicazione). 

> [!NOTE]
> Quando si richiama un metodo diretto in un dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri alfanumerici stampabili US-ASCII, ad eccezione dei seguenti: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

I metodi diretti sono sincroni e possono solo avere esito positivo o negativo dopo il periodo di timeout. Il valore predefinito è 30 secondi, ma il valore massimo impostabile è 300 secondi. Risultano utili negli scenari interattivi in cui si vuole che il dispositivo agisca esclusivamente se è online e riceve comandi, ad esempio nel caso dell'accensione di una luce da un telefono. In questi scenari l'esito positivo o negativo deve essere immediato, in modo che il servizio cloud possa agire in base al risultato il prima possibile. Il dispositivo può restituire un corpo del messaggio come risultato del metodo, ma non è necessario che il metodo esegua questa operazione. Nelle chiamate ai metodi non esiste alcuna garanzia di ordinamento o semantica di concorrenza.

I metodi diretti supportano solo HTTPS lato cloud e solo MQTT o AMQP lato dispositivo.

Il payload per le richieste e le risposte del metodo è un documento JSON con dimensioni massime di 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Richiamare un metodo diretto da un'app back-end

Richiamare ora un metodo diretto da un'app back-end.

### <a name="method-invocation"></a>Chiamata al metodo

Le chiamate a metodi diretti in un dispositivo sono chiamate HTTPS composte dagli elementi seguenti:

* *URI della richiesta* specifico del dispositivo con la [versione dell'API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metodo* POST

* *Intestazioni* contenenti l'autorizzazione, l'ID richiesta, il tipo di contenuto e la codifica del contenuto.

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

#### <a name="example"></a>Esempio

Vedere di seguito un semplice esempio che usa `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Risposta

L'app back-end riceve una risposta composta dagli elementi seguenti:

* *Codice di stato HTTP*, usato per errori provenienti dall'hub IoT, incluso un errore 404 per i dispositivi attualmente non connessi.

* *Intestazioni* contenenti l'ETag, l'ID richiesta, il tipo di contenuto e la codifica del contenuto.

* *Corpo* JSON nel formato seguente:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Sia `status` che `body` vengono forniti dal dispositivo e usati per rispondere con la descrizione e/o il codice di stato del dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Chiamata al metodo per i moduli di IoT Edge

La chiamata di metodi diretti mediante un ID modulo è supportata nell'[SDK per C# del client del servizio IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

A tale scopo, usare il metodo `ServiceClient.InvokeDeviceMethodAsync()` e passare `deviceId` e `moduleId` come parametri.

## <a name="handle-a-direct-method-on-a-device"></a>Gestire un metodo diretto in un dispositivo

Si vedrà ora come gestire un metodo diretto in un dispositivo IoT.

### <a name="mqtt"></a>MQTT

La sezione seguente è per il protocollo MQTT.

#### <a name="method-invocation"></a>Chiamata al metodo

I dispositivi ricevono richieste di metodi diretti nell'argomento MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. Il numero di sottoscrizioni per dispositivo è limitato a 5. È pertanto consigliabile non sottoscrivere ogni metodo diretto singolarmente. Valutare invece la possibilità di sottoscrivere `$iothub/methods/POST/#` e quindi di filtrare i messaggi recapitati in base ai nomi dei metodi desiderati.

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

La sezione seguente è per il protocollo AMQP.

#### <a name="method-invocation"></a>Chiamata al metodo

Il dispositivo riceve richieste di metodi diretti tramite la creazione di un collegamento di ricezione sull'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Il messaggio AMQP arriva sul collegamento di ricezione che rappresenta la richiesta del metodo. Contiene le sezioni seguenti:

* La proprietà ID di correlazione, contenente un ID richiesta che deve essere passato con la relativa risposta del metodo.

* Una proprietà dell'applicazione denominata `IoThub-methodname`, contenente il nome del metodo richiamato.

* Il corpo del messaggio AMQP, contenente il payload del metodo in formato JSON.

#### <a name="response"></a>Risposta

Il dispositivo crea un collegamento di invio per restituire la risposta del metodo all'indirizzo `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

La risposta del metodo viene restituita sul collegamento di invio e contiene gli elementi seguenti:

* La proprietà ID di correlazione, contenente l'ID richiesta passato nel messaggio di richiesta del metodo.

* Una proprietà dell'applicazione denominata `IoThub-status`, contenente lo stato del metodo fornito dall'utente.

* Il corpo del messaggio AMQP, contenente la risposta del metodo in formato JSON.

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.

* [Limitazione e quote](iot-hub-devguide-quotas-throttling.md) descrive le quote applicabili al comportamento di limitazione previsto quando si usa l'hub IoT.

* [Azure IoT SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.

* [Il linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing messaggi](iot-hub-devguide-query-language.md) descrive il linguaggio di query dell'hub IoT che è possibile usare per recuperare informazioni dall'hub IoT sui processi e i dispositivi gemelli.

* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md) offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i metodi diretti, è possibile vedere un altro articolo di interesse della Guida per sviluppatori dell'hub IoT:

* [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md)

Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Usare metodi diretti](quickstart-control-device-node.md)
* [Gestione dei dispositivi con Azure IoT Tools per VS Code](iot-hub-device-management-iot-toolkit.md)
