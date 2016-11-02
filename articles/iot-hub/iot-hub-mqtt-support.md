<properties
 pageTitle="Supporto di MQTT nell'hub IoT | Microsoft Azure"
 description="Descrizione del supporto di MQTT a livello di hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>


# <a name="iot-hub-mqtt-support"></a>Supporto di MQTT nell'hub IoT

L'hub IoT consente ai dispositivi di comunicare con gli endpoint dei dispositivi dell'hub IoT usando il protocollo [MQTT v3.1.1][lnk-mqtt-org] sulla porta 8883. L'hub IoT richiede che tutte le comunicazioni del dispositivo siano protette tramite TLS/SSL.

## <a name="connecting-to-iot-hub"></a>Connessione all'hub IoT

Un dispositivo può usare il protocollo MQTT per connettersi a un hub IoT usando le librerie disponibili negli [SDK di Microsoft Azure IoT][lnk-device-sdks] o direttamente con il protocollo MQTT.

## <a name="using-the-device-client-sdks"></a>Uso degli SDK per client per dispositivi

[SDK per client per dispositivi][lnk-device-sdks] che supportano il protocollo MQTT sono disponibili per Java, Node.js, C e C#. Gli SDK per client per dispositivi usano la stringa di connessione dell'hub IoT standard per stabilire una connessione a un hub IoT. Per usare il protocollo MQTT, il parametro del protocollo del client deve essere impostato su **MQTT**. Per impostazione predefinita, gli SDK per client per dispositivi si connettono a un hub IoT con il flag **CleanSession** impostato su **0** e usano **QoS 1** per lo scambio di messaggi con l'hub IoT.

Quando un dispositivo è connesso a un hub IoT, gli SDK per client per dispositivi forniscono i metodi che consentono al dispositivo di inviare messaggi a un hub IoT e di riceverne.

La tabella seguente include i collegamenti a esempi di codice per ogni linguaggio supportato e specifica il parametro da usare per stabilire una connessione all'hub IoT con il protocollo MQTT.

| Linguaggio                   | Parametro del protocollo        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>Eseguire la migrazione di un'app per dispositivo da AMQP a MQTT
Se si usano gli [SDK per client per dispositivi][lnk-device-sdks], per passare da AMQP a MQTT, è necessario modificare il parametro del protocollo nell'inizializzazione client, come indicato prima.

Quando si esegue questa operazione, controllare gli elementi seguenti:

* AMQP restituisce errori per diverse condizioni, mentre MQTT termina la connessione. Di conseguenza, la logica di gestione delle eccezioni potrebbe richiedere alcune modifiche.
* MQTT non supporta le operazioni di *rifiuto* quando si ricevono [messaggi da cloud a dispositivo][lnk-messaging]. Se il back-end deve ricevere una risposta dall'app per dispositivo, considerare la possibilità di usare [metodi diretti][lnk-methods].
* Per il momento, MQTT non è disponibile su WebSockets.

## <a name="using-the-mqtt-protocol-directly"></a>Uso del protocollo MQTT direttamente

Se un dispositivo non può usare gli SDK per client per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT. Nel pacchetto **CONNECT** il dispositivo deve usare i valori seguenti:

- Per il campo **ClientId** usare **deviceId**. 
- Per il campo **Username** usare `{iothubhostname}/{device_id}`, dove {iothubhostname} rappresenta il record CName completo dell'hub IoT.

    Ad esempio, se il nome dell'hub IoT è **contoso.azure-devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere `contoso.azure-devices.net/MyDevice01`.

- Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTP e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Per altre informazioni su come generare i token di firma di accesso condiviso, vedere la sezione sui dispositivi nell'articolo [Using IoT Hub security tokens][lnk-sas-tokens] (Uso dei token di sicurezza dell'hub IoT).
    
    Durante il test, è anche possibile usare lo strumento [Device Explorer][lnk-device-explorer] per generare rapidamente un token di firma di accesso condiviso da copiare e incollare nel codice:
    
    1. Andare alla scheda **Management** di Device Explorer.
    2. Fare clic su **SAS Token** in alto a destra.
    3. In **SASTokenForm** selezionare il dispositivo nell'elenco a discesa **DeviceID**. Impostare il valore **TTL**.
    4. Fare clic su **Generate** per creare il token.
    
    Il token di firma di accesso condiviso generato ha questa struttura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La porzione di questo token da usare come campo **Password** per connettersi usando MQTT è:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Per i pacchetti di connessione e disconnessione di MQTT l'hub IoT genera un evento nel canale **Monitoraggio operazioni** .

### <a name="sending-messages-to-iot-hub"></a>Invio di messaggi all'hub IoT

Dopo avere stabilito una connessione, un dispositivo può inviare messaggi all'hub IoT usando `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come **nome di argomento**. L'elemento `{property_bag}` consente al dispositivo di inviare messaggi con proprietà aggiuntive in un formato con codifica URL. Ad esempio:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Questo elemento `{property_bag}`usa la stessa codifica delle stringhe di query nel protocollo HTTP.

L'applicazione client del dispositivo può usare anche `devices/{device_id}/messages/events/{property_bag}` come **nome argomento Will** per definire *messaggi Will* da inoltrare come messaggio di telemetria.

### <a name="receiving-messages"></a>Ricezione di messaggi

Per ricevere messaggi dall'hub IoT, un dispositivo deve eseguire la sottoscrizione con `devices/{device_id}/messages/devicebound/#”` come **filtro di argomento**. L'hub IoT recapita i messaggi con il **nome di argomento** `devices/{device_id}/messages/devicebound/` o `devices/{device_id}/messages/devicebound/{property_bag}` se sono presenti proprietà dei messaggi. `{property_bag}` contiene coppie chiave/valore con codifica URL di proprietà dei messaggi. Solo le proprietà dell'applicazione e le proprietà di sistema configurabili dall'utente, ad esempio **messageId** o **correlationId**, sono incluse nel contenitore delle proprietà. I nomi delle proprietà di sistema hanno il prefisso **$**. Le proprietà dell'applicazione usano il nome della proprietà originale senza il prefisso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Notes on MQTT support][lnk-mqtt-devguide] (Note sul supporto di MQTT) nella guida per gli sviluppatori dell'hub IoT di Azure.

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT][lnk-mqtt-docs].

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

- [Dispositivi supportati][lnk-devices]
- [Supportare protocolli aggiuntivi][lnk-protocols]
- [Eseguire il confronto con Hub eventi][lnk-compare]
- [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

- [Guida per gli sviluppatori][lnk-devguide]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


