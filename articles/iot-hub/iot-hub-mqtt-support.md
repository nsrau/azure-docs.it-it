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
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Supporto di MQTT nell'hub IoT

L'hub IoT consente ai dispositivi di comunicare con gli endpoint dei dispositivi dell'hub IoT tramite il protocollo [MQTT v3.1.1][lnk-mqtt-org] sulla porta 8883. L'hub IoT richiede che tutte le comunicazioni del dispositivo siano protette tramite TLS/SSL.

Per altre informazioni, vedere [Note sul supporto di MQTT][lnk-mqtt-devguide] nella Guida per gli sviluppatori dell'hub IoT di Azure.

## Connessione all'hub IoT

Un dispositivo può connettersi a un hub IoT con il protocollo MQTT usando le librerie disponibili nei [Microsoft Azure IoT SDK][lnk-device-sdks] o direttamente con il protocollo MQTT.

## Uso degli SDK per client per dispositivi

[SDK per client per dispositivi][lnk-device-sdks] che supportano il protocollo MQTT sono disponibili per Java, Node.js, C e C#. Gli SDK per client per dispositivi usano la stringa di connessione dell'hub IoT standard per stabilire una connessione a un hub IoT. Per usare il protocollo MQTT, il parametro del protocollo del client deve essere impostato su **MQTT**. Per impostazione predefinita, gli SDK per client per dispositivi si connettono a un hub IoT con il flag **CleanSession** impostato su **0** e usano **QoS 1** per lo scambio di messaggi con l'hub IoT.

Quando un dispositivo è connesso a un hub IoT, gli SDK per client per dispositivi forniscono i metodi che consentono al dispositivo di inviare messaggi a un hub IoT e di riceverne.

La tabella seguente include i collegamenti a esempi di codice per ogni linguaggio supportato e specifica il parametro da usare per stabilire una connessione all'hub IoT con il protocollo MQTT.

| Linguaggio | Parametro del protocollo |
| -------------------------- | ------------------------- |
| [Node.JS][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Uso del protocollo MQTT direttamente

Se un dispositivo non può usare gli SDK per client per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT. Nel pacchetto **CONNECT** il dispositivo deve usare i valori seguenti:

- Per il campo **ClientId** usare **deviceId**. 
- Per il campo **Username** usare `{iothubhostname}/{device_id}`, dove {iothubhostname} rappresenta il record CName completo dell'hub IoT.

    Ad esempio, se il nome dell'hub IoT è **contoso.azure-devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere `contoso.azure-devices.net/MyDevice01`.

- Per il campo **Password** usare un token di firma di accesso condiviso. Il [formato del token di firma di accesso condiviso][lnk-iothub-security] è identico a quello descritto per i protocolli HTTP e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

    Per altre informazioni su come generare i token di firma di accesso condiviso, vedere l'articolo [Uso dei token di sicurezza hub IoT][lnk-sas-tokens].
    
    Durante il test, è anche possibile usare lo strumento [Device Explorer][lnk-device-explorer] per generare rapidamente un token di firma di accesso condiviso da copiare e incollare direttamente nel codice:
    
    1. Andare alla scheda **Management** di Device Explorer.
    2. Fare clic su **SAS Token** in alto a destra.
    3. In **SASTokenForm** selezionare il dispositivo nell'elenco a discesa **DeviceID**. Impostare il valore **TTL**.
    4. Fare clic su **Generate** per creare il token.
    
    Il token di firma di accesso condiviso generato avrà l'aspetto seguente: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La porzione da usare nel campo **Password** per connettersi usando MQTT è: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Per i pacchetti di connessione e disconnessione di MQTT l'hub IoT genera un evento nel canale **Monitoraggio operazioni**.

### Invio di messaggi all'hub IoT

Dopo aver stabilito correttamente una connessione, un dispositivo può inviare messaggi all'hub IoT usando `devices/{did}/messages/events/` o `devices/{did}/messages/events/{property_bag}` come **Nome argomento**. L'elemento `{property_bag}` consente al dispositivo di inviare messaggi con proprietà aggiuntive in un formato con codifica URL. Ad esempio:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] È la stessa codifica usata per le stringhe di query nel protocollo HTTP.

L'applicazione client per dispositivi può anche usare `devices/{did}/messages/events/{property_bag}` come **nome dell'argomento Will** per definire *i messaggi Will* da inoltrare come messaggio di telemetria.

### Ricezione di messaggi

Per ricevere messaggi dall'hub IoT, un dispositivo deve eseguire la sottoscrizione con `devices/{did}/messages/devicebound/#”` come **Filtro argomento**. L'hub IoT recapita i messaggi con il **Nome argomento** `devices/{did}/messages/devicebound/` o `devices/{did}/messages/devicebound/{property_bag}` se sono presenti proprietà del messaggio. `{property_bag}` contiene coppie chiave/valore con codifica URL di proprietà del messaggio. Solo le proprietà dell'applicazione e le proprietà di sistema configurabili dall'utente, ad esempio **messageId** o **correlationId**, sono incluse nel contenitore delle proprietà. I nomi delle proprietà di sistema hanno il prefisso **$**. Le proprietà dell'applicazione usano il nome della proprietà originale senza il prefisso.

## Passaggi successivi

Per altre informazioni sull'uso degli SDK per client per dispositivi che consentono di comunicare con l'hub IoT, vedere [Introduzione all'hub IoT di Azure per .NET][lnk-iot-get-stated].

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

<!---HONumber=AcomDC_0406_2016-->