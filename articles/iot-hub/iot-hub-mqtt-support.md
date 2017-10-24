---
title: Informazioni sul supporto MQTT dell'hub IoT di Azure | Documentazione Microsoft
description: Guida per sviluppatori - Supporto per dispositivi che si connettono a un endpoint che usa dispositivi dell'hub IoT con il protocollo MQTT. Sono incluse informazioni sul supporto MQTT integrato in Azure IoT SDK per dispositivi.
services: iot-hub
documentationcenter: .net
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: kdotchko
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc0dcfd004a453df2c0ce64d0d92c6f533a54f6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicare con l'hub IoT tramite il protocollo MQTT

L'hub IoT consente ai dispositivi di comunicare con gli endpoint dei dispositivi dell'hub IoT usando il protocollo [MQTT v3.1.1][lnk-mqtt-org] sulla porta 8883 o MQTT v3.1.1 con protocollo WebSocket sulla porta 443. L'hub IoT richiede che tutte le comunicazioni del dispositivo siano protette tramite TLS/SSL (pertanto l'hub IoT non supporta connessioni non protette sulla porta 1883).

## <a name="connecting-to-iot-hub"></a>Connessione all'hub IoT

Un dispositivo può usare il protocollo MQTT per connettersi a un hub IoT usando le librerie disponibili negli [Azure IoT SDK][lnk-device-sdks] o direttamente con il protocollo MQTT.

## <a name="using-the-device-sdks"></a>Uso degli SDK per dispositivi

Gli [SDK per dispositivi][lnk-device-sdks] che supportano il protocollo MQTT sono disponibili per Java, Node.js, C, C# e Python. Gli SDK per dispositivi usano la stringa di connessione dell'hub IoT standard per stabilire una connessione a un hub IoT. Per usare il protocollo MQTT, il parametro del protocollo del client deve essere impostato su **MQTT**. Per impostazione predefinita, gli SDK per dispositivi si connettono a un hub IoT con il flag **CleanSession** impostato su **0** e usano **QoS 1** per lo scambio di messaggi con l'hub IoT.

Quando un dispositivo è connesso a un hub IoT, gli SDK per dispositivi forniscono i metodi che consentono al dispositivo di inviare messaggi a un hub IoT e di riceverne.

La tabella seguente include i collegamenti a esempi di codice per ogni linguaggio supportato e specifica il parametro da usare per stabilire una connessione all'hub IoT con il protocollo MQTT.

| Linguaggio | Parametro del protocollo |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrazione di un'app per dispositivo da AMQP a MQTT

Se si usano gli [SDK per dispositivi][lnk-device-sdks], per passare da AMQP a MQTT è necessario modificare il parametro del protocollo nell'inizializzazione client come indicato in precedenza.

Quando si esegue questa operazione, controllare gli elementi seguenti:

* AMQP restituisce errori per diverse condizioni, mentre MQTT termina la connessione. Di conseguenza, la logica di gestione delle eccezioni potrebbe richiedere alcune modifiche.
* MQTT non supporta le operazioni di *rifiuto* quando si ricevono [messaggi da cloud a dispositivo][lnk-messaging]. Se l'app back-end deve ricevere una risposta dall'app per dispositivo, considerare la possibilità di usare [metodi diretti][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Uso del protocollo MQTT direttamente
Se un dispositivo non può usare gli SDK per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT sulla porta 8883. Nel pacchetto **CONNECT** il dispositivo deve usare i valori seguenti:

* Per il campo **ClientId** usare **deviceId**.
* Per il campo **Username** usare `{iothubhostname}/{device_id}/api-version=2016-11-14`, dove {iothubhostname} rappresenta il record CName completo dell'hub IoT.

    Ad esempio, se il nome dell'hub IoT è **contoso.azure-devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`.
* Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTPS e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Per altre informazioni su come generare i token di firma di accesso condiviso, vedere la sezione sui dispositivi nell'articolo [Uso dei token di sicurezza dell'hub IoT][lnk-sas-tokens].

    Durante il test è anche possibile usare lo strumento [Device Explorer][lnk-device-explorer] per generare rapidamente un token di firma di accesso condiviso da copiare e incollare nel codice:

  1. Andare alla scheda **Management** (Gestione) di **Device Explorer**.
  2. Fare clic su **SAS Token** in alto a destra.
  3. In **SASTokenForm** selezionare il dispositivo nell'elenco a discesa **DeviceID**. Impostare il valore **TTL**.
  4. Fare clic su **Generate** per creare il token.

     Il token di firma di accesso condiviso generato ha questa struttura: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

     La porzione di questo token da usare come campo **Password** per connettersi usando MQTT è: `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

Per i pacchetti di connessione e disconnessione di MQTT l'hub IoT genera un evento nel canale **Monitoraggio operazioni** con ulteriori informazioni in grado di contribuire a risolvere i problemi di connettività.

### <a name="sending-device-to-cloud-messages"></a>Invio di messaggi da dispositivo a cloud

Dopo avere stabilito una connessione, un dispositivo può inviare messaggi all'hub IoT usando `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come **nome di argomento**. L'elemento `{property_bag}` consente al dispositivo di inviare messaggi con proprietà aggiuntive in un formato con codifica URL. ad esempio:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Questo elemento `{property_bag}` usa la stessa codifica delle stringhe di query nel protocollo HTTPS.
>
>

L'app per dispositivo può usare anche `devices/{device_id}/messages/events/{property_bag}` come **nome argomento Will** per definire *messaggi Will* da inoltrare come messaggio di telemetria.

- L'hub IoT non supporta i messaggi di QoS 2. Quando un'app per dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete.
- L'hub IoT non rende persistenti i messaggi di mantenimento. Se un dispositivo invia un messaggio con il flag **RETAIN** impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain**. In tal caso, anziché rendere persistente il messaggio di mantenimento, l'hub IoT passa invece all'app back-end.
- L'hub IoT supporta solo una connessione MQTT attiva per ogni dispositivo. Qualsiasi nuova connessione MQTT per conto dello stesso ID di dispositivo causa la perdita della connessione esistente da parte dell'hub IoT.

Per altre informazioni, vedere [Guida per gli sviluppatori sulla messaggistica][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Ricezione di messaggi da cloud a dispositivo

Per ricevere messaggi dall'hub IoT, un dispositivo deve eseguire la sottoscrizione con `devices/{device_id}/messages/devicebound/#` come **filtro di argomento**. Il carattere jolly a più livelli **#** nel filtro argomento viene utilizzato solo per consentire al dispositivo di ricevere proprietà aggiuntive nel nome dell'argomento. L'hub IoT on consente l'utilizzo di caratteri jolly **#** o **?** per il filtro di argomenti secondari. Poiché l'hub IoT non è un broker di messaggistica di pubblicazione e sottoscrizione generico, supporta solo i nomi di argomento e i filtri di argomento documentati .

Il dispositivo non riceverà i messaggi dall'hub IoT prima di aver effettuato la sottoscrizione all'endpoint del dispositivo specifico, rappresentato dal filtro argomento `devices/{device_id}/messages/devicebound/#`. Dopo aver stabilito la sottoscrizione correttamente, il dispositivo inizierà a ricevere solo messaggi da cloud a dispositivo che gli sono stati inviati dopo la sottoscrizione. Se il dispositivo si connette con il flag **CleanSession** impostato su **0**, la sottoscrizione sarà mantenuta tra sessioni diverse. In questo caso, alla connessione successiva con **CleanSession 0** il dispositivo riceverà i messaggi in sospeso che gli sono stati inviati mentre era disconnesso. Se il dispositivo usa il flag **CleanSession** impostato su **1** tuttavia, non riceverà i messaggi dall'hub IoT fino a quando non si registra presso l'endpoint del dispositivo.

L'hub IoT recapita i messaggi con il **nome di argomento** `devices/{device_id}/messages/devicebound/` o `devices/{device_id}/messages/devicebound/{property_bag}` se sono presenti proprietà dei messaggi. `{property_bag}` contiene coppie chiave/valore con codifica URL di proprietà dei messaggi. Solo le proprietà dell'applicazione e le proprietà di sistema configurabili dall'utente, ad esempio **messageId** o **correlationId**, sono incluse nel contenitore delle proprietà. I nomi delle proprietà di sistema hanno il prefisso **$**. Le proprietà dell'applicazione usano il nome della proprietà originale senza il prefisso.

Quando un'app del dispositivo esegue una sottoscrizione a un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**. Successivamente, l'hub IoT invierà i messaggi al dispositivo tramite QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Recupero delle proprietà dei dispositivi gemelli

Un dispositivo effettua la sottoscrizione a `$iothub/twin/res/#` per ricevere le risposte dell'operazione. Invia quindi un messaggio vuoto all'argomento `$iothub/twin/GET/?$rid={request id}`, con un valore popolato per **request id**. Il servizio invierà quindi un messaggio di risposta con i dati del dispositivo gemello nell'argomento `$iothub/twin/res/{status}/?$rid={request id}`, usando lo stesso **ID richiesta** della richiesta.

L'ID richiesta può essere qualsiasi valore valido per la proprietà di un messaggio, come descritto nella [Guida per gli sviluppatori sulla messaggistica dell'hub IoT][lnk-messaging], e lo stato viene convalidato come valore intero.
Il corpo della risposta contiene la sezione delle proprietà del dispositivo gemello:

Il corpo della voce del registro delle identità sarà limitato al membro "properties", ad esempio:

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

I possibili codici di stato sono i seguenti:

|Stato | Descrizione |
| ----- | ----------- |
| 200 | Success |
| 429 | Numero eccessivo di richieste (limitazione), come descritto in [Limitazione dell'hub IoT][lnk-quotas] |
| 5** | Errori server |

Per altre informazioni, vedere la [Guida per gli sviluppatori sui dispositivi gemelli][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Aggiornare le proprietà segnalate di un dispositivo gemello

La sequenza seguente descrive in che modo un dispositivo aggiorna le proprietà dichiarate in un dispositivo gemello nell'hub IoT:

1. Un dispositivo deve prima sottoscrivere l'argomento `$iothub/twin/res/#` per ricevere le risposte dell'operazione dall'hub IoT.

1. Un dispositivo invia un messaggio che contiene l'aggiornamento di un dispositivo gemello per l'argomento `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Questo messaggio include un valore **request id**.

1. Il servizio invia un messaggio di risposta che contiene il nuovo valore ETag per la raccolta di proprietà dichiarate sull'argomento `$iothub/twin/res/{status}/?$rid={request id}`. Questo messaggio di risposta usa lo stesso valore **request id** della richiesta.

Il corpo del messaggio di richiesta contiene un documento JSON che specifica nuovi valori per le proprietà segnalate. Non è possibile modificare altre proprietà o altri metadati.
Ogni membro nel documento JSON aggiorna o aggiunge il membro corrispondente nel documento del dispositivo gemello. Un membro impostato su `null` elimina il membro dall'oggetto contenitore. Ad esempio:

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

I possibili codici di stato sono i seguenti:

|Stato | Descrizione |
| ----- | ----------- |
| 200 | Success |
| 400 | Richiesta non valida. JSON non valido |
| 429 | Numero eccessivo di richieste (limitazione), come descritto in [Limitazione dell'hub IoT][lnk-quotas] |
| 5** | Errori server |

Per altre informazioni, vedere la [Guida per gli sviluppatori sui dispositivi gemelli][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Ricezione delle notifiche di aggiornamento delle proprietà desiderate

Quando un dispositivo è connesso, l'hub IoT invia notifiche all'argomento `$iothub/twin/PATCH/properties/desired/?$version={new version}`, con il contenuto dell'aggiornamento eseguito dal back-end della soluzione. Ad esempio:

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

Come per gli aggiornamenti delle proprietà, i valori `null` indicano l'eliminazione del membro dell'oggetto JSON.


> [!IMPORTANT] 
> L'hub IoT genera notifiche di modifica solo quando i dispositivi sono connessi. Assicurarsi di implementare il [flusso di riconnessione del dispositivo][lnk-devguide-twin-reconnection] per mantenere la sincronizzazione delle proprietà desiderate tra l'hub IoT e l'app per dispositivo.

Per altre informazioni, vedere la [Guida per gli sviluppatori sui dispositivi gemelli][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Rispondere a un metodo diretto

Un dispositivo deve effettuare la sottoscrizione a `$iothub/methods/POST/#`. L'hub IoT invia le richieste di metodo all'argomento `$iothub/methods/POST/{method name}/?$rid={request id}` con un codice JSON valido o un corpo vuoto.

Per rispondere, il dispositivo invia all'argomento `$iothub/methods/res/{status}/?$rid={request id}` un messaggio con un codice JSON valido o un corpo vuoto, dove l'**ID della richiesta** deve corrispondere al valore presente nel messaggio della richiesta e lo **stato** deve essere un numero intero.

Per altre informazioni, vedere la [Guida per gli sviluppatori sui metodi diretti][lnk-methods].

### <a name="additional-considerations"></a>Ulteriori considerazioni

Se è necessario personalizzare il comportamento del protocollo MQTT sul lato cloud, è infine consigliabile vedere [Gateway del protocollo IoT Azure][lnk-azure-protocol-gateway], che descrive come distribuire un gateway del protocollo personalizzato con prestazioni elevate che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo IoT Azure consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio richiede tuttavia l'esecuzione e la gestione di un gateway di protocollo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT][lnk-mqtt-docs].

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Catalogo dei dispositivi Azure Certified per IoT][lnk-devices]
* [Supportare protocolli aggiuntivi][lnk-protocols]
* [Eseguire il confronto con Hub eventi][lnk-compare]
* [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
