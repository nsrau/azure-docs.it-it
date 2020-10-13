---
title: Informazioni sul supporto MQTT dell'hub IoT di Azure | Documentazione Microsoft
description: Supporto per i dispositivi che si connettono a un endpoint del dispositivo dell'hub Internet con il protocollo MQTT. Sono incluse informazioni sul supporto MQTT integrato in Azure IoT SDK per dispositivi.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperfq1
ms.openlocfilehash: 720d8f3b1f3d13427cda56ee68596d190ac40dc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767317"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicare con l'hub IoT tramite il protocollo MQTT

L'hub IoT consente ai dispositivi di comunicare con gli endpoint dei dispositivi dell'hub IoT usando:

* [MQTT v3.1.1](https://mqtt.org/) sulla porta 8883
* MQTT v3.1.1 sul WebSocket sulla porta 443.

L'hub IoT non è un broker MQTT completo e non supporta tutti i comportamenti specificati nello standard MQTT v3.1.1. Questo articolo descrive come i dispositivi possono utilizzare comportamenti MQTT supportati per comunicare con l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Tutte le comunicazioni del dispositivo con l'hub IoT devono essere protette tramite TLS/SSL. Pertanto, l'hub IoT non supporta le connessioni non protette sulla porta 1883.

## <a name="connecting-to-iot-hub"></a>Connessione all'hub IoT

Un dispositivo può usare il protocollo MQTT per connettersi a un hub IoT usando una qualsiasi delle opzioni seguenti.

* Le librerie negli [SDK di Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Direttamente il protocollo MQTT.

In molti ambienti di rete aziendali e didattici, la porta MQTT (8883) è bloccata. Se non è possibile aprire la porta 8883 nel firewall, è consigliabile usare MQTT su Web Socket. Il protocollo MQTT su Web Socket comunica tramite la porta 443, che è quasi sempre aperta negli ambienti di rete. Per informazioni su come specificare i protocolli MQTT e MQTT su Web Socket quando si usano gli SDK per Azure IoT, vedere [Uso degli SDK per dispositivi](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Uso degli SDK per dispositivi

Gli [SDK per dispositivi](https://github.com/Azure/azure-iot-sdks) che supportano il protocollo MQTT sono disponibili per Java, Node.js, C, C# e Python.ython. Gli SDK per dispositivi usano la stringa di connessione dell'hub IoT standard per stabilire una connessione a un hub IoT. Per usare il protocollo MQTT, il parametro del protocollo del client deve essere impostato su **MQTT**. È inoltre possibile specificare MQTT su Web Socket nel parametro del protocollo client. Per impostazione predefinita, gli SDK per dispositivi si connettono a un hub IoT con il flag **CleanSession** impostato su **0** e usano **QoS 1** per lo scambio di messaggi con l'hub IoT. Sebbene sia possibile configurare **QoS 0** per uno scambio di messaggi più veloce, si noti che il recapito non è garantito né riconosciuto. Per questo motivo, **QoS 0** viene spesso definito "Fire and Forget".

Quando un dispositivo è connesso a un hub IoT, gli SDK per dispositivi forniscono i metodi che consentono al dispositivo di scambiare messaggi con un hub IoT.

La tabella seguente contiene collegamenti a esempi di codice per ogni linguaggio supportato e specifica il parametro da usare per stabilire una connessione all'hub IoT con il protocollo MQTT o MQTT su Web Socket.

| Linguaggio | Parametro del protocollo MQTT | Parametro del protocollo MQTT su Web Socket
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt.Mqtt | azure-iot-device-mqtt.MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable).MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet).Mqtt | TransportType.Mqtt esegue il fallback a MQTT su Web Socket se il protocollo MQTT dà esito negativo. Per specificare solo il protocollo MQTT su Web Socket, usare il parametro TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Supporta il protocollo MQTT per impostazione predefinita | Aggiungere `websockets=True` nella chiamata per creare il client |

Il frammento seguente illustra come specificare il protocollo MQTT su Web Socket quando si usa l’SDK di Azure IoT per node.js:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Il frammento seguente illustra come specificare il protocollo MQTT su Web Socket quando si usa l’SDK di Azure IoT per Python.

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Timeout keep-alive predefinito

Per garantire la continuità di una connessione tra client e hub IoT, sia il servizio che il client si inviano reciprocamente un ping *keep-alive* con regolarità. Il client che usa l’IoT SDK invia un comando keep-alive all’intervallo definito nella tabella sottostante:

|Linguaggio  |Intervallo di keep-alive predefinito  |Configurabile  |
|---------|---------|---------|
|Node.js     |   180 secondi      |     No    |
|Java     |    230 secondi     |     No    |
|C     | 240 secondi |  [Sì](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 secondi |  [Sì](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 secondi |  No   |

In seguito alla [specifica MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081), l'intervallo di ping keep-alive dell'hub internet è 1,5 volte il valore Keep-Alive del client. Tuttavia, l'hub IoT limita il timeout massimo lato server a 29,45 minuti (1767 secondi) in quanto tutti i servizi di Azure sono associati al timeout di inattività TCP del servizio di bilanciamento del carico di Azure, che è di 29,45 minuti. 

Ad esempio, un dispositivo che usa Java SDK invia il ping keep-alive, quindi perde la connettività di rete. 230 secondi dopo, il dispositivo perde il ping keep-alive perché è offline. Tuttavia, l'hub IoT non chiude la connessione immediatamente. Attende altri `(230 * 1.5) - 230 = 115` secondi prima di disconnettere il dispositivo con l'errore [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

Il valore di keep-alive massimo del client che è possibile impostare è di `1767 / 1.5 = 1177` secondi. Qualsiasi traffico reimposta il keep-alive. Ad esempio, un aggiornamento corretto del token di firma di accesso condiviso reimposta il keep-alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrazione di un'app per dispositivo da AMQP a MQTT

Se si usano gli [SDK per dispositivi](https://github.com/Azure/azure-iot-sdks), per passare dall'uso di AMQP a MQTT è necessario modificare il parametro del protocollo nell'inizializzazione del client, come indicato in precedenza.

Quando si esegue questa operazione, controllare gli elementi seguenti:

* AMQP restituisce errori per diverse condizioni, mentre MQTT termina la connessione. Di conseguenza, la logica di gestione delle eccezioni potrebbe richiedere alcune modifiche.

* MQTT non supporta le operazioni di *rifiuto* quando si ricevono [messaggi da cloud a dispositivo](iot-hub-devguide-messaging.md). Se l'app back-end deve ricevere una risposta dall'app per dispositivo, considerare la possibilità di usare [metodi diretti](iot-hub-devguide-direct-methods.md).

* AMQP non è supportato in Python SDK.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Esempio in C uso di MQTT senza Azure Internets SDK

Nel [repository di esempio MQTT](https://github.com/Azure-Samples/IoTMQTTSample), è possibile trovare un paio di progetti demo C/C++ che illustrano come inviare messaggi di telemetria e ricevere eventi con un hub Internet senza usare l'SDK di Azure. 

Questi esempi usano la libreria Eclipse mosquitto per inviare messaggi al broker MQTT implementato nell'hub Internet.

Questo repository contiene:

**Per Windows:**

* TelemetryMQTTWin32: contiene il codice per inviare un messaggio di telemetria a un hub IoT di Azure, compilato ed eseguito in un computer Windows.

* SubscribeMQTTWin32: contiene il codice per sottoscrivere eventi di un hub IoT specifico in un computer Windows.

* DeviceTwinMQTTWin32: contiene il codice per eseguire una query e sottoscrivere gli eventi di dispositivi gemelli di un dispositivo nell'Hub IoT di Azure in un computer Windows.

* PnPMQTTWin32: contiene il codice per inviare un messaggio di telemetria con le funzionalità del dispositivo Plug and Play a un hub di Azure, compilato ed eseguito in un computer Windows. Per altre informazioni, vedere [plug and Play](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Per Linux:**

* MQTTLinux: contiene il codice e lo script di compilazione da eseguire su Linux (finora sono stati testati WSL, Ubuntu e Raspbian).

* LinuxConsoleVS2019: contiene lo stesso codice, ma in un progetto VS2019 destinato a WSL (sottosistema di Windows Linux). Questo progetto consente di eseguire il debug passo passo del codice eseguito in Linux dall’interno Visual Studio.

**Per mosquitto_pub:**

Questa cartella contiene due comandi di esempio usati con l’utilità mosquitto_pub di Mosquitto.org.

* Mosquitto_sendmessage: per inviare un messaggio di testo semplice a un hub IoT di Azure che funge da dispositivo.

* Mosquitto_subscribe: per visualizzare gli eventi che si verificano in un hub IoT di Azure.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Uso diretto del protocollo MQTT (come dispositivo)

Se un dispositivo non può usare gli SDK per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT sulla porta 8883. Nel pacchetto **CONNECT** il dispositivo deve usare i valori seguenti:

* Per il campo **ClientId** usare **deviceId**.

* Per il campo **Username** usare `{iothubhostname}/{device_id}/?api-version=2018-06-30`, dove `{iothubhostname}` rappresenta il record CName completo dell'hub IoT.

    Ad esempio, se il nome dell'hub IoT è **contoso.azure-devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Le password di token SAS non sono obbligatorie se si usa l'autenticazione dei certificati X.509. Per altre informazioni, vedere [configurare la sicurezza X. 509 nell'hub Azure](iot-hub-security-x509-get-started.md) e seguire le istruzioni per il codice nella [sezione di configurazione TLS/SSL](#tlsssl-configuration).

  Per altre informazioni su come generare i token di firma di accesso condiviso, vedere la sezione sui dispositivi nell'articolo [Uso dei token di sicurezza dell'hub IoT](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Quando si esegue il test, è anche possibile usare gli [strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Internet multipiattaforma per Visual Studio Code o il comando di estensione CLI [AZ l'hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token) per generare rapidamente un token SAS che è possibile copiare e incollare nel codice.

### <a name="for-azure-iot-tools"></a>Per Azure IoT Tools

1. Espandere la scheda **DISPOSITIVI DELL'HUB AZURE IOT** nell'angolo inferiore sinistro di Visual Studio Code.
  
2. Fare clic con il tasto destro sul dispositivo e selezionare **Genera token di firma di accesso condiviso per dispositivo**.
  
3. Impostare l'**ora di scadenza** premere "Invio".
  
4. Il token di firma di accesso condiviso viene creato e copiato negli appunti.

   Il token SAS generato ha questa struttura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   La porzione di questo token da usare come campo **Password** per connettersi usando MQTT è:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Per i pacchetti di connessione e disconnessione di MQTT l'hub IoT genera un evento nel canale **Monitoraggio operazioni** . Questo evento contiene informazioni aggiuntive che consentono di risolvere i problemi di connettività.

L'app per dispositivo può specificare un messaggio **Will** nel pacchetto **CONNECT**. L'app per dispositivo deve usare `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come nome di argomento **Will** per definire i messaggi **Will** da inoltrare come messaggi di telemetria. In questo caso, se la connessione di rete viene chiusa, ma il dispositivo non ha prima ricevuto un pacchetto **DISCONNECT**, l'hub IoT invia il messaggio **Will** specificato nel pacchetto **CONNECT** al canale di telemetria. Quest'ultimo può essere l'endpoint **Events** predefinito o un endpoint personalizzato definito dal routing dell'hub IoT. Alla proprietà **iothub-MessageType** del messaggio è assegnato un valore **Will**.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Uso diretto del protocollo MQTT (come modulo)

La connessione all'hub Internet delle cose tramite MQTT usando un'identità del modulo è simile al dispositivo (descritto [nella sezione sull'uso del protocollo MQTT direttamente come dispositivo](#using-the-mqtt-protocol-directly-as-a-device)), ma è necessario usare quanto segue:

* Impostare l'ID client su `{device_id}/{module_id}`.

* Se si esegue l'autenticazione con nome utente e password, impostare il nome utente `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e usare il token di firma di accesso condiviso associato all'identità modulo come password.

* Usare `devices/{device_id}/modules/{module_id}/messages/events/` come argomento per la pubblicazione dei dati di telemetria.

* Usare `devices/{device_id}/modules/{module_id}/messages/events/` come argomento WILL.

* Gli argomenti relativi ai comandi PATCH e GET per i dispositivi gemelli sono identici anche per i moduli.

* L'argomento relativo allo stato dei dispositivi gemelli è identico anche per i moduli.

## <a name="tlsssl-configuration"></a>Configurazione TLS/SSL

Per usare direttamente il protocollo MQTT, il client *deve* connettersi tramite TLS/SSL, altrimenti si verificheranno errori di connessione.

Per stabilire una connessione TLS, potrebbe essere necessario scaricare e fare riferimento al DigiCert Baltimore Root Certificate, Questo certificato è quello usato da Azure per proteggere la connessione. È possibile trovare il certificato nel repository [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c). Altre informazioni su questi certificati sono disponibili nel [sito Web di Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Un esempio di implementazione che usa la versione Python della [libreria Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) di Eclipse Foundation può presentarsi come segue.

È prima necessario installare la libreria Paho dall'ambiente della riga di comando:

```cmd/sh
pip install paho-mqtt
```

Implementare poi il client in uno script Python. Sostituire i segnaposto come segue:

* `<local path to digicert.cer>` è il percorso in un file locale che contiene il certificato radice DigiCert Baltimore. È possibile creare questo file copiando le informazioni sul certificato da [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) negli Azure IoT SDK per C. Includere le righe `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----`, rimuovere i contrassegni `"` all'inizio e alla fine di ogni riga e rimuovere i caratteri `\r\n` alla fine di ogni riga.

* `<device id from device registry>` è l'ID di un dispositivo che è stato aggiunto all'hub IoT.

* `<generated SAS token>` è un token SAS per il dispositivo creato come descritto in precedenza in questo articolo.

* `<iot hub name>` nome dell'hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Per eseguire l'autenticazione usando un certificato del dispositivo, aggiornare il frammento di codice precedente con le modifiche seguenti (vedere [Come ottenere un certificato CA X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) per informazioni su come preparare l'autenticazione basata su certificati):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Invio di messaggi da dispositivo a cloud

Dopo avere stabilito una connessione, un dispositivo può inviare messaggi all'hub IoT usando `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come **nome di argomento**. L'elemento `{property_bag}` consente al dispositivo di inviare messaggi con proprietà aggiuntive in un formato con codifica URL. Ad esempio:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> L’elemento `{property_bag}` usa la stessa codifica delle stringhe di query nel protocollo HTTPS.

Di seguito è riportato un elenco di comportamenti specifici dell'implementazione dell'hub IoT:

* L'hub IoT non supporta i messaggi di QoS 2. Quando un'app per dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete.

* L'hub IoT non rende persistenti i messaggi di mantenimento. Se un dispositivo invia un messaggio con il flag **RETAIN** impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain**. In tal caso, anziché rendere persistente il messaggio di mantenimento, l'hub IoT passa invece all'app back-end.

* L'hub IoT supporta solo una connessione MQTT attiva per ogni dispositivo. Qualsiasi nuova connessione MQTT per conto dello stesso ID di dispositivo causa la perdita della connessione esistente da parte dell'hub IoT.

Per altre informazioni, vedere [Guida per gli sviluppatori sulla messaggistica](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Ricezione di messaggi da cloud a dispositivo

Per ricevere messaggi dall'hub IoT, un dispositivo deve eseguire la sottoscrizione con `devices/{device_id}/messages/devicebound/#` come **filtro di argomento**. Il carattere jolly a più livelli `#` nel filtro argomento viene usato solo per consentire al dispositivo di ricevere proprietà aggiuntive nel nome dell'argomento. L'hub IoT non consente l'uso dei caratteri jolly `#` o `?` per il filtro di argomenti secondari. Poiché l'hub IoT non è un broker di messaggistica di pubblicazione e sottoscrizione generico, supporta solo i nomi di argomento e i filtri di argomento documentati.

Il dispositivo non riceve alcun messaggio dall'hub Internet fino a quando non ha completato la sottoscrizione all'endpoint specifico del dispositivo, rappresentato dal `devices/{device_id}/messages/devicebound/#` filtro dell'argomento. Dopo aver stabilito la sottoscrizione, il dispositivo inizierà a ricevere i messaggi da cloud a dispositivo che gli sono stati inviati dopo la sottoscrizione. Se il dispositivo si connette con il flag **CleanSession** impostato su **0**, la sottoscrizione sarà mantenuta tra sessioni diverse. In questo caso, alla connessione successiva con **CleanSession 0** il dispositivo riceverà i messaggi in sospeso che gli sono stati inviati mentre era disconnesso. Se il dispositivo usa il flag **CleanSession** impostato su **1** tuttavia, non riceverà i messaggi dall'hub IoT fino a quando non si registra presso l'endpoint del dispositivo.

L'hub IoT recapita i messaggi con il **Nome argomento** `devices/{device_id}/messages/devicebound/` o `devices/{device_id}/messages/devicebound/{property_bag}` in presenza di proprietà dei messaggi. `{property_bag}` contiene coppie chiave/valore con codifica URL di proprietà dei messaggi. Solo le proprietà dell'applicazione e le proprietà di sistema configurabili dall'utente, ad esempio **messageId** o **correlationId**, sono incluse nel contenitore delle proprietà. I nomi delle proprietà di sistema hanno il prefisso **$** . Le proprietà dell'applicazione usano il nome della proprietà originale senza il prefisso. Per ulteriori informazioni sul formato del contenitore delle proprietà, vedere [invio di messaggi da dispositivo a cloud](#sending-device-to-cloud-messages).

Nei messaggi da cloud a dispositivo, i valori nel contenitore delle proprietà sono rappresentati come nella tabella seguente:

| Valore proprietà | Rappresentazione | Description |
|----|----|----|
| `null` | `key` | Solo la chiave viene visualizzata nel contenitore delle proprietà |
| stringa vuota | `key=` | Chiave seguita da un segno di uguale senza valore |
| valore non null e non vuoto | `key=value` | Chiave seguita da un segno di uguale e dal valore |

Nell'esempio seguente viene illustrato un contenitore delle proprietà contenente tre proprietà dell'applicazione: **Prop1** con il valore `null` ; **prop2**, una stringa vuota (""); e **Prop3** con il valore "a String".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Quando un'app del dispositivo esegue una sottoscrizione a un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**. Successivamente, l'hub IoT invierà i messaggi al dispositivo tramite QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recupero delle proprietà dei dispositivi gemelli

Un dispositivo effettua la sottoscrizione a `$iothub/twin/res/#` per ricevere le risposte dell'operazione. Invia quindi un messaggio vuoto all'argomento `$iothub/twin/GET/?$rid={request id}`, con un valore popolato per **request ID**. Il servizio invierà quindi un messaggio di risposta con i dati del dispositivo gemello nell'argomento `$iothub/twin/res/{status}/?$rid={request id}`, usando lo stesso **request ID** della richiesta.

L'ID richiesta può essere qualsiasi valore valido per un valore di proprietà del messaggio, in base alla guida per gli [sviluppatori di messaggistica dell'hub](iot-hub-devguide-messaging.md)Internet e allo stato viene convalidato come numero intero.

Il corpo della risposta contiene la sezione delle proprietà del dispositivo gemello, come illustrato nell'esempio di risposta seguente:

```json
{
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
```

I possibili codici di stato sono i seguenti:

|Stato | Descrizione |
| ----- | ----------- |
| 200 | Operazione completata |
| 429 | Numero eccessivo di richieste (limitazione), come descritto in [Limitazione dell'hub IoT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Errori server |

Per ulteriori informazioni, vedere la [Guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aggiornare le proprietà segnalate di un dispositivo gemello

Per aggiornare le proprietà segnalate, il dispositivo invia una richiesta all'hub IoT tramite una pubblicazione su un argomento MQTT designato. Dopo aver elaborato la richiesta, l'hub IoT indica lo stato di esito positivo o negativo dell'operazione di aggiornamento tramite una pubblicazione su un altro argomento, a cui il dispositivo può eseguire la sottoscrizione per poter essere informato sul risultato della richiesta di aggiornamento dei dispositivi gemelli. Per implementare questo tipo di interazione richiesta/risposta in MQTT, ci si avvale dell'ID richiesta (`$rid`) inizialmente specificato dal dispositivo nella richiesta di aggiornamento. L'ID richiesta è incluso anche nella risposta dell'hub IoT per consentire al dispositivo di correlare la risposta alla richiesta appropriata.

La sequenza seguente descrive in che modo un dispositivo aggiorna le proprietà dichiarate in un dispositivo gemello nell'hub IoT:

1. Un dispositivo deve prima sottoscrivere l'argomento `$iothub/twin/res/#` per ricevere le risposte dell'operazione dall'hub IoT.

2. Un dispositivo invia un messaggio che contiene l'aggiornamento di un dispositivo gemello per l'argomento `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Questo messaggio include un valore **request ID**.

3. Il servizio invia un messaggio di risposta che contiene il nuovo valore ETag per la raccolta di proprietà dichiarate sull'argomento `$iothub/twin/res/{status}/?$rid={request id}`. Questo messaggio di risposta usa lo stesso **request ID** della richiesta.

Il corpo del messaggio di richiesta include un documento JSON che contiene nuovi valori per le proprietà segnalate. Ogni membro nel documento JSON aggiorna o aggiunge il membro corrispondente nel documento del dispositivo gemello. Un membro impostato per `null` eliminare il membro dall'oggetto che lo contiene. Ad esempio:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

I possibili codici di stato sono i seguenti:

|Stato | Descrizione |
| ----- | ----------- |
| 204 | Operazione riuscita (non viene restituito alcun contenuto) |
| 400 | Richiesta non valida. JSON non valido |
| 429 | Numero eccessivo di richieste (limitazione), come descritto in [Limitazione dell'hub IoT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Errori server |

Il frammento di codice Python seguente illustra il processo di aggiornamento delle proprietà segnalate dei dispositivi gemelli su MQTT (tramite il client MQTT Paho):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

In caso di esito positivo dell'operazione di aggiornamento delle proprietà segnalate dei dispositivi gemelli, il messaggio di pubblicazione dell'hub IoT avrà l'argomento seguente: `$iothub/twin/res/204/?$rid=1&$version=6`, dove `204` è il codice di stato che indica l'esito positivo, `$rid=1` corrisponde all'ID richiesta fornito dal dispositivo nel codice e `$version` corrisponde alla versione della sezione delle proprietà segnalate dei dispositivi gemelli dopo l'aggiornamento.

Per ulteriori informazioni, vedere la [Guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Ricezione delle notifiche di aggiornamento delle proprietà desiderate

Quando un dispositivo è connesso, l'hub IoT invia notifiche all'argomento `$iothub/twin/PATCH/properties/desired/?$version={new version}`, con il contenuto dell'aggiornamento eseguito dal back-end della soluzione. Ad esempio:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Come per gli aggiornamenti delle proprietà, i valori `null` indicano l'eliminazione del membro dell'oggetto JSON. Si noti inoltre che `$version` indica la nuova versione della sezione delle proprietà desiderate del dispositivo gemello.

> [!IMPORTANT]
> L'hub IoT genera notifiche di modifica solo quando i dispositivi sono connessi. Assicurarsi di implementare il [flusso di riconnessione del dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) per mantenere la sincronizzazione delle proprietà desiderate tra l'hub IoT e l'app per dispositivo.

Per ulteriori informazioni, vedere la [Guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Rispondere a un metodo diretto

Un dispositivo deve effettuare la sottoscrizione a `$iothub/methods/POST/#`. L'hub IoT invia le richieste di metodo all'argomento `$iothub/methods/POST/{method name}/?$rid={request id}` con un codice JSON valido o un corpo vuoto.

Per rispondere, il dispositivo invia un messaggio con un codice JSON valido o un corpo vuoto all'argomento `$iothub/methods/res/{status}/?$rid={request id}`. In questo messaggio, l'**ID della richiesta** deve corrispondere a quello nel messaggio della richiesta, e lo **stato** deve essere un numero intero.

Per ulteriori informazioni, vedere la [Guida per gli sviluppatori di metodi diretti](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Altre considerazioni

Come considerazione finale, se è necessario personalizzare il comportamento del protocollo MQTT sul lato cloud, occorre rivedere il [gateway del protocollo IoT di Azure](iot-hub-protocol-gateway.md). Questo software consente di distribuire un gateway di protocollo personalizzato ad alte prestazioni che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo IoT Azure consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio richiede tuttavia l'esecuzione e la gestione di un gateway di protocollo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT](https://mqtt.org/).

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Catalogo dei dispositivi Azure Certified per IoT](https://catalog.azureiotsolutions.com/)
* [Supportare protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Eseguire il confronto con Hub eventi](iot-hub-compare-event-hubs.md)
* [Scalabilità, disponibilità elevata e ripristino di emergenza](iot-hub-scaling.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
