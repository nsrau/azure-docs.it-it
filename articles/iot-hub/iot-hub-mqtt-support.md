---
title: Informazioni sul supporto MQTT dell'hub IoT di Azure | Documentazione Microsoft
description: Guida per sviluppatori - Supporto per dispositivi che si connettono a un endpoint che usa dispositivi dell'hub IoT con il protocollo MQTT. Sono incluse informazioni sul supporto MQTT integrato in Azure IoT SDK per dispositivi.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9b1f0042f501cefc99343d53bbf2ad39f0ae1f4c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640475"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicare con l'hub IoT tramite il protocollo MQTT

L'hub IoT consente ai dispositivi di comunicare con gli endpoint dei dispositivi dell'hub IoT usando:

* [MQTT v 3.1.1](https://mqtt.org/) sulla porta 8883
* MQTT v3.1.1 sul WebSocket sulla porta 443.

L'hub IoT non è un broker MQTT completo e non supporta tutti i comportamenti specificati nello standard MQTT v3.1.1. Questo articolo descrive come i dispositivi possono utilizzare comportamenti MQTT supportati per comunicare con l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Tutte le comunicazioni del dispositivo con l'hub IoT devono essere protette tramite TLS/SSL. Pertanto, l'hub IoT non supporta le connessioni non protette sulla porta 1883.

## <a name="connecting-to-iot-hub"></a>Connessione all'hub IoT

Un dispositivo può usare il protocollo MQTT per connettersi a un hub Internet delle cose usando una delle opzioni seguenti.

* Librerie negli [SDK Azure](https://github.com/Azure/azure-iot-sdks).
* Il protocollo MQTT direttamente.

## <a name="using-the-device-sdks"></a>Uso degli SDK per dispositivi

Gli [SDK per dispositivi](https://github.com/Azure/azure-iot-sdks) che supportano il protocollo MQTT sono disponibili per Java, node. js, C#C, e Python. Gli SDK per dispositivi usano la stringa di connessione dell'hub IoT standard per stabilire una connessione a un hub IoT. Per usare il protocollo MQTT, il parametro del protocollo del client deve essere impostato su **MQTT**. Per impostazione predefinita, gli SDK per dispositivi si connettono a un hub IoT con il flag **CleanSession** impostato su **0** e usano **QoS 1** per lo scambio di messaggi con l'hub IoT.

Quando un dispositivo è connesso a un hub IoT, gli SDK per dispositivi forniscono i metodi che consentono al dispositivo di scambiare messaggi con un hub IoT.

La tabella seguente include i collegamenti a esempi di codice per ogni linguaggio supportato e specifica il parametro da usare per stabilire una connessione all'hub IoT con il protocollo MQTT.

| Linguaggio | Parametro del protocollo |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrazione di un'app per dispositivo da AMQP a MQTT

Se si usano gli [SDK per dispositivi](https://github.com/Azure/azure-iot-sdks), per passare dall'uso di AMQP a MQTT è necessario modificare il parametro del protocollo nell'inizializzazione client come indicato in precedenza.

Quando si esegue questa operazione, controllare gli elementi seguenti:

* AMQP restituisce errori per diverse condizioni, mentre MQTT termina la connessione. Di conseguenza, la logica di gestione delle eccezioni potrebbe richiedere alcune modifiche.

* MQTT non supporta le operazioni di *rifiuto* durante la ricezione [di messaggi da cloud a dispositivo](iot-hub-devguide-messaging.md). Se l'app back-end deve ricevere una risposta dall'app per dispositivo, prendere in considerazione l'uso di [metodi diretti](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Uso diretto del protocollo MQTT (come dispositivo)

Se un dispositivo non può usare gli SDK per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT sulla porta 8883. Nel pacchetto di **connessione** il dispositivo deve usare i valori seguenti:

* Per il campo **ClientId** usare **deviceId**.

* Per il campo **Username** usare `{iothubhostname}/{device_id}/?api-version=2018-06-30`, dove `{iothubhostname}` rappresenta il record CName completo dell'hub IoT.

    Ad esempio, se il nome dell'hub IoT è **contoso.azure-devices.net** e il nome del dispositivo è **MyDevice01**, il campo **Username** completo deve contenere:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Le password di token SAS non sono obbligatorie se si usa l'autenticazione dei certificati X.509. Per altre informazioni, vedere [configurare la sicurezza X. 509 nell'hub Azure](iot-hub-security-x509-get-started.md) .

  Per altre informazioni su come generare i token di firma di accesso condiviso, vedere la sezione relativa ai dispositivi [con i token di sicurezza dell'hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)Internet.

  Quando si esegue il test, è anche possibile usare gli [strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Internet multi-piattaforma per Visual Studio Code o lo strumento [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) per generare rapidamente un token di firma di accesso condiviso che è possibile copiare e incollare nel codice:

### <a name="for-azure-iot-tools"></a>Per gli strumenti di Azure.

1. Espandere la scheda **DISPOSITIVI DELL'HUB AZURE IOT** nell'angolo inferiore sinistro di Visual Studio Code.
  
2. Fare clic con il tasto destro sul dispositivo e selezionare **Genera token di firma di accesso condiviso per dispositivo**.
  
3. Impostare l'**ora di scadenza** premere "Invio".
  
4. Il token di firma di accesso condiviso viene creato e copiato negli appunti.

### <a name="for-device-explorer"></a>Per Device Explorer

1. Andare alla scheda **Management** (Gestione) di **Device Explorer**.

2. Fare clic su **SAS Token** in alto a destra.

3. In **SASTokenForm** selezionare il dispositivo nell'elenco a discesa **DeviceID**. Impostare il valore **TTL**.

4. Fare clic su **Generate** per creare il token.

   Il token SAS generato ha questa struttura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   La porzione di questo token da usare come campo **Password** per connettersi usando MQTT è:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Per i pacchetti di connessione e disconnessione di MQTT l'hub IoT genera un evento nel canale **Monitoraggio operazioni** . Questo evento contiene informazioni aggiuntive che consentono di risolvere i problemi di connettività.

L'app per dispositivo può specificare un messaggio **Will** nel pacchetto **CONNECT**. L'app per dispositivo deve usare `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come nome di argomento **Will** per definire i messaggi **Will** da inoltrare come messaggi di telemetria. In questo caso, se la connessione di rete viene chiusa, ma il dispositivo non ha prima ricevuto un pacchetto **DISCONNECT**, l'hub IoT invia il messaggio **Will** specificato nel pacchetto **CONNECT** al canale di telemetria. Quest'ultimo può essere l'endpoint **Events** predefinito o un endpoint personalizzato definito dal routing dell'hub IoT. Alla proprietà **iothub-MessageType** del messaggio è assegnato un valore **Will**.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Uso diretto del protocollo MQTT (come modulo)

La connessione all'hub IoT tramite MQTT con un'identità modulo è simile a quella in cui viene usato un dispositivo (come descritto [sopra](#using-the-mqtt-protocol-directly-as-a-device)), ma è necessario soddisfare i requisiti seguenti:

* Impostare l'ID client su `{device_id}/{module_id}`.

* Se si esegue l'autenticazione con nome utente e password, impostare il nome utente `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e usare il token di firma di accesso condiviso associato all'identità modulo come password.

* Usare `devices/{device_id}/modules/{module_id}/messages/events/` come argomento per la pubblicazione dei dati di telemetria.

* Usare `devices/{device_id}/modules/{module_id}/messages/events/` come argomento WILL.

* Gli argomenti relativi ai comandi PATCH e GET per i dispositivi gemelli sono identici anche per i moduli.

* L'argomento relativo allo stato dei dispositivi gemelli è identico anche per i moduli.

## <a name="tlsssl-configuration"></a>Configurazione TLS/SSL

Per usare direttamente il protocollo MQTT, il client *deve* connettersi tramite TLS/SSL, altrimenti si verificheranno errori di connessione.

Per stabilire una connessione TLS, potrebbe essere necessario scaricare e fare riferimento al DigiCert Baltimore Root Certificate, Questo certificato è quello usato da Azure per proteggere la connessione. Questo certificato è reperibile nel repository [Azure-Internet-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Altre informazioni su questi certificati sono reperibili nel [sito Web di DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Un esempio di come implementare questa operazione usando la versione Python della [libreria PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) da Eclipse Foundation potrebbe essere simile al seguente.

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

path_to_root_cert = "<local path to digicert.cer>"
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
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Di seguito sono riportate le istruzioni di installazione per i prerequisiti.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="sending-device-to-cloud-messages"></a>Invio di messaggi da dispositivo a cloud

Dopo avere stabilito una connessione, un dispositivo può inviare messaggi all'hub IoT usando `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` come **nome di argomento**. L'elemento `{property_bag}` consente al dispositivo di inviare messaggi con proprietà aggiuntive in un formato con codifica URL. Ad esempio:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Questo `{property_bag}` elemento usa la stessa codifica delle stringhe di query nel protocollo HTTPS.

Di seguito è riportato un elenco di comportamenti specifici dell'implementazione dell'hub IoT:

* L'hub IoT non supporta i messaggi di QoS 2. Quando un'app per dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete.

* L'hub IoT non rende persistenti i messaggi di mantenimento. Se un dispositivo invia un messaggio con il flag **RETAIN** impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain**. In tal caso, anziché rendere persistente il messaggio di mantenimento, l'hub IoT passa invece all'app back-end.

* L'hub IoT supporta solo una connessione MQTT attiva per ogni dispositivo. Qualsiasi nuova connessione MQTT per conto dello stesso ID di dispositivo causa la perdita della connessione esistente da parte dell'hub IoT.

Per ulteriori informazioni, vedere [la guida per gli sviluppatori di messaggistica](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Ricezione di messaggi da cloud a dispositivo

Per ricevere messaggi dall'hub IoT, un dispositivo deve eseguire la sottoscrizione con `devices/{device_id}/messages/devicebound/#` come **filtro di argomento**. Il carattere jolly a più livelli `#` nel filtro argomento viene usato solo per consentire al dispositivo di ricevere proprietà aggiuntive nel nome dell'argomento. L'hub IoT non consente l'uso dei caratteri jolly `#` o `?` per il filtro di argomenti secondari. Poiché l'hub IoT non è un broker di messaggistica di pubblicazione e sottoscrizione generico, supporta solo i nomi di argomento e i filtri di argomento documentati.

Il dispositivo non riceverà i messaggi dall'hub IoT prima di aver effettuato la sottoscrizione all'endpoint del dispositivo specifico, rappresentato dal filtro argomento `devices/{device_id}/messages/devicebound/#`. Dopo aver stabilito la sottoscrizione, il dispositivo inizierà a ricevere i messaggi da cloud a dispositivo che gli sono stati inviati dopo la sottoscrizione. Se il dispositivo si connette con il flag **CleanSession** impostato su **0**, la sottoscrizione sarà mantenuta tra sessioni diverse. In questo caso, alla connessione successiva con **CleanSession 0** il dispositivo riceverà i messaggi in sospeso che gli sono stati inviati mentre era disconnesso. Se il dispositivo usa il flag **CleanSession** impostato su **1** tuttavia, non riceverà i messaggi dall'hub IoT fino a quando non si registra presso l'endpoint del dispositivo.

L'hub IoT recapita i messaggi con il **nome di argomento** `devices/{device_id}/messages/devicebound/` o `devices/{device_id}/messages/devicebound/{property_bag}` se sono presenti proprietà dei messaggi. `{property_bag}` contiene coppie chiave/valore con codifica URL di proprietà dei messaggi. Solo le proprietà dell'applicazione e le proprietà di sistema configurabili dall'utente, ad esempio **messageId** o **correlationId**, sono incluse nel contenitore delle proprietà. I nomi delle proprietà di sistema hanno il prefisso **$** . Le proprietà dell'applicazione usano il nome della proprietà originale senza il prefisso.

Quando un'app del dispositivo esegue una sottoscrizione a un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**. Successivamente, l'hub IoT invierà i messaggi al dispositivo tramite QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recupero delle proprietà dei dispositivi gemelli

Un dispositivo effettua la sottoscrizione a `$iothub/twin/res/#` per ricevere le risposte dell'operazione. Invia quindi un messaggio vuoto all'argomento `$iothub/twin/GET/?$rid={request id}`, con un valore popolato per **request ID**. Il servizio invierà quindi un messaggio di risposta con i dati del dispositivo gemello nell'argomento `$iothub/twin/res/{status}/?$rid={request id}`, usando lo stesso **request ID** della richiesta.

L'ID richiesta può essere qualsiasi valore valido per il valore di una proprietà del messaggio, come per la [Guida per gli sviluppatori della messaggistica dell'hub](iot-hub-devguide-messaging.md)Internet e lo stato viene convalidato come numero intero.

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
| 204 | Operazione riuscita (non viene restituito alcun contenuto) |
| 429 | Troppe richieste (limitate), come per la [limitazione dell'hub](iot-hub-devguide-quotas-throttling.md) degli anni |
| 5** | Errori server |

Per altre informazioni, vedere [la guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aggiornare le proprietà segnalate di un dispositivo gemello

Per aggiornare le proprietà segnalate, il dispositivo invia una richiesta all'hub IoT tramite una pubblicazione su un argomento MQTT designato. Dopo aver elaborato la richiesta, l'hub IoT indica lo stato di esito positivo o negativo dell'operazione di aggiornamento tramite una pubblicazione su un altro argomento, a cui il dispositivo può eseguire la sottoscrizione per poter essere informato sul risultato della richiesta di aggiornamento dei dispositivi gemelli. Per implementare questo tipo di interazione richiesta/risposta in MQTT, ci si avvale dell'ID richiesta (`$rid`) inizialmente fornito dal dispositivo nella richiesta di aggiornamento. L'ID richiesta è incluso anche nella risposta dell'hub IoT per consentire al dispositivo di correlare la risposta alla richiesta appropriata.

La sequenza seguente descrive in che modo un dispositivo aggiorna le proprietà dichiarate in un dispositivo gemello nell'hub IoT:

1. Un dispositivo deve prima sottoscrivere l'argomento `$iothub/twin/res/#` per ricevere le risposte dell'operazione dall'hub IoT.

2. Un dispositivo invia un messaggio che contiene l'aggiornamento di un dispositivo gemello per l'argomento `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Questo messaggio include un valore **request ID**.

3. Il servizio invia un messaggio di risposta che contiene il nuovo valore ETag per la raccolta di proprietà dichiarate sull'argomento `$iothub/twin/res/{status}/?$rid={request id}`. Questo messaggio di risposta usa lo stesso **request ID** della richiesta.

Il corpo del messaggio di richiesta include un documento JSON che contiene nuovi valori per le proprietà segnalate. Ogni membro nel documento JSON aggiorna o aggiunge il membro corrispondente nel documento del dispositivo gemello. Un membro impostato su `null` elimina il membro dall'oggetto contenitore. Ad esempio:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

I possibili codici di stato sono i seguenti:

|Stato | DESCRIZIONE |
| ----- | ----------- |
| 200 | Riuscito |
| 400 | Richiesta non valida. JSON non valido |
| 429 | Troppe richieste (limitate), come per la [limitazione dell'hub](iot-hub-devguide-quotas-throttling.md) degli anni |
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

Per altre informazioni, vedere [la guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

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
> L'hub IoT genera notifiche di modifica solo quando i dispositivi sono connessi. Assicurarsi di implementare il flusso di riconnessione del [dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) per garantire la sincronizzazione delle proprietà desiderate tra l'hub e l'app per dispositivi.

Per altre informazioni, vedere [la guida per gli sviluppatori di dispositivi gemelli](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Rispondere a un metodo diretto

Un dispositivo deve effettuare la sottoscrizione a `$iothub/methods/POST/#`. L'hub IoT invia le richieste di metodo all'argomento `$iothub/methods/POST/{method name}/?$rid={request id}` con un codice JSON valido o un corpo vuoto.

Per rispondere, il dispositivo invia un messaggio con un codice JSON valido o un corpo vuoto all'argomento `$iothub/methods/res/{status}/?$rid={request id}`. In questo messaggio, l'**ID della richiesta** deve corrispondere a quello nel messaggio della richiesta, e lo **stato** deve essere un numero intero.

Per ulteriori informazioni, vedere [Guida per gli sviluppatori di metodi diretti](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Ulteriori considerazioni

Come ultima considerazione, se è necessario personalizzare il comportamento del protocollo MQTT sul lato cloud, è necessario esaminare il gateway del [protocollo di Azure](iot-hub-protocol-gateway.md). Questo software consente di distribuire un gateway di protocollo personalizzato ad alte prestazioni che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo IoT Azure consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio richiede tuttavia l'esecuzione e la gestione di un gateway di protocollo personalizzato.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul protocollo MQTT, vedere la [documentazione di MQTT](https://mqtt.org/documentation).

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Catalogo dei dispositivi Azure Certified per IoT](https://catalog.azureiotsolutions.com/)
* [Supporto di protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Confrontare con hub eventi](iot-hub-compare-event-hubs.md)
* [Scalabilità, disponibilità elevata e ripristino di emergenza](iot-hub-scaling.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
