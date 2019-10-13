---
title: Informazioni sul supporto di AMQP per l'hub Azure Microsoft Docs
description: "Guida per gli sviluppatori: supporto per i dispositivi che si connettono agli endpoint con connessione al dispositivo e al servizio dell'hub Internet tramite il protocollo AMQP. Include informazioni sul supporto AMQP incorporato negli SDK per dispositivi Azure."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286645"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Comunicare con l'hub Internet delle cose usando il protocollo AMQP

Hub Internet Azure è in grado di supportare [OASIS Advance Message Queueing Protocol (AMQP) versione 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) per offrire un'ampia gamma di funzionalità tramite endpoint destinati ai dispositivi e ai servizi. Questo documento descrive l'uso dei client AMQP per la connessione a un hub Internet delle cose per usare le funzionalità dell'hub Internet.

## <a name="service-client"></a>Client del servizio

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Connettersi ed eseguire l'autenticazione a un hub Internet (client del servizio)

Per connettersi a un hub Internet delle cose usando AMQP, un client può usare l'autenticazione [con sicurezza basata su attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Per il client del servizio sono necessarie le informazioni seguenti:

| Informazioni | Value |
|-------------|--------------|
| Nome host dell'hub Internet | `<iot-hub-name>.azure-devices.net` |
| Nome chiave | `service` |
| Chiave di accesso | Chiave primaria o secondaria associata al servizio |
| Firma di accesso condiviso | Una firma di accesso condiviso di breve durata nel formato seguente: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Per ottenere il codice per la generazione della firma, vedere [controllare l'accesso all'hub](./iot-hub-devguide-security.md#security-token-structure)Internet.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi a un hub Internet delle cose tramite un collegamento al mittente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Richiamare messaggi da cloud a dispositivo (client del servizio)

Per informazioni sullo scambio di messaggi da cloud a dispositivo tra il servizio e l'hub Internet e tra il dispositivo e l'hub Internet, vedere [inviare messaggi da cloud a dispositivo dall'hub](iot-hub-devguide-messages-c2d.md)Internet. Il client del servizio usa due collegamenti per inviare messaggi e ricevere commenti e suggerimenti per i messaggi inviati in precedenza dai dispositivi, come descritto nella tabella seguente:

| Creato da | Tipo di collegamento | Percorso collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Service | Collegamento al mittente | `/messages/devicebound` | I messaggi da cloud a dispositivo destinati ai dispositivi vengono inviati al collegamento dal servizio. I messaggi inviati tramite questo collegamento hanno la proprietà `To` impostata sul percorso del collegamento del ricevitore del dispositivo di destinazione, `/devices/<deviceID>/messages/devicebound`. |
| Service | Collegamento ricevitore | `/messages/serviceBound/feedback` | Messaggi di feedback di completamento, rifiuto e abbandono che provengono da dispositivi ricevuti su questo collegamento dal servizio. Per altre informazioni sui messaggi di feedback, vedere [inviare messaggi da cloud a dispositivo da un hub](./iot-hub-devguide-messages-c2d.md#message-feedback)Internet. |

Il frammento di codice seguente illustra come creare un messaggio da cloud a dispositivo e come inviarlo a un dispositivo usando la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Per ricevere commenti e suggerimenti, il client del servizio crea un collegamento al ricevitore. Il frammento di codice seguente illustra come creare un collegamento usando la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Come illustrato nel codice precedente, un messaggio di feedback da cloud a dispositivo ha un tipo di contenuto *Application/vnd. Microsoft. iothub. feedback. JSON*. È possibile usare le proprietà nel corpo JSON del messaggio per dedurre lo stato di recapito del messaggio originale:

* La chiave `statusCode` nel corpo del feedback presenta uno dei valori seguenti: *Success*, *expired*, *DeliveryCountExceeded*, *rejected*o *eliminato*.

* La chiave `deviceId` nel corpo del feedback ha l'ID del dispositivo di destinazione.

* La chiave `originalMessageId` nel corpo del feedback ha l'ID del messaggio da cloud a dispositivo originale inviato dal servizio. È possibile usare questo stato di recapito per correlare i feedback ai messaggi da cloud a dispositivo.

### <a name="receive-telemetry-messages-service-client"></a>Ricevere messaggi di telemetria (client del servizio)

Per impostazione predefinita, l'hub Internet delle cose archivia i messaggi di telemetria del dispositivo inseriti in un hub eventi predefinito. Il client del servizio può usare il protocollo AMQP per ricevere gli eventi archiviati.

A questo scopo, il client del servizio deve innanzitutto connettersi all'endpoint dell'hub Internet e ricevere un indirizzo di reindirizzamento per gli hub eventi predefiniti. Il client del servizio usa quindi l'indirizzo fornito per connettersi all'hub eventi predefinito.

In ogni passaggio il client deve presentare le seguenti informazioni:

* Credenziali del servizio valide (token di firma di accesso condiviso del servizio).

* Percorso ben formattato della partizione del gruppo di consumer da cui intende recuperare i messaggi. Per un gruppo di consumer e un ID di partizione specificati, il percorso ha il seguente formato: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (il gruppo di consumer predefinito è `$Default`).

* Predicato di filtro facoltativo per designare un punto iniziale nella partizione. Questo predicato può essere sotto forma di numero di sequenza, offset o timestamp accodato.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per illustrare i passaggi precedenti:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Per un ID dispositivo specificato, l'hub Internet usa un hash dell'ID dispositivo per determinare la partizione in cui archiviare i messaggi. Il frammento di codice precedente illustra il modo in cui gli eventi vengono ricevuti da una singola partizione di questo tipo. Si noti tuttavia che un'applicazione tipica deve spesso recuperare gli eventi archiviati in tutte le partizioni dell'hub eventi.

## <a name="device-client"></a>Client dispositivo

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Connettersi ed eseguire l'autenticazione a un hub Internet (client dispositivo)

Per connettersi a un hub Internet delle cose usando AMQP, un dispositivo può usare l'autenticazione [con sicurezza basata su attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) .

Per il client del dispositivo sono necessarie le informazioni seguenti:

| Informazioni | Value |
|-------------|--------------|
| Nome host dell'hub Internet | `<iot-hub-name>.azure-devices.net` |
| Chiave di accesso | Chiave primaria o secondaria associata al dispositivo |
| Firma di accesso condiviso | Una firma di accesso condiviso di breve durata nel formato seguente: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Per ottenere il codice per la generazione della firma, vedere [controllare l'accesso all'hub](./iot-hub-devguide-security.md#security-token-structure)Internet.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi a un hub Internet delle cose tramite un collegamento al mittente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

I percorsi di collegamento seguenti sono supportati come operazioni del dispositivo:

| Creato da | Tipo di collegamento | Percorso collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Dispositivi | Collegamento ricevitore | `/devices/<deviceID>/messages/devicebound` | I messaggi da cloud a dispositivo destinati ai dispositivi sono ricevuti su questo collegamento da ogni dispositivo di destinazione. |
| Dispositivi | Collegamento al mittente | `/devices/<deviceID>/messages/events` | I messaggi da dispositivo a cloud inviati da un dispositivo vengono inviati tramite questo collegamento. |
| Dispositivi | Collegamento al mittente | `/messages/serviceBound/feedback` | Commenti del messaggio da cloud a dispositivo inviati al servizio tramite questo collegamento da parte dei dispositivi. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Ricevere comandi da cloud a dispositivo (client dispositivo)

I comandi da cloud a dispositivo inviati ai dispositivi arrivano con un collegamento `/devices/<deviceID>/messages/devicebound`. I dispositivi possono ricevere questi messaggi in batch e utilizzare il payload dei dati del messaggio, le proprietà del messaggio, le annotazioni o le proprietà dell'applicazione nel messaggio in base alle esigenze.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python)) per ricevere i messaggi da cloud a dispositivo da un dispositivo.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Inviare messaggi di telemetria (client dispositivo)

È anche possibile inviare messaggi di telemetria da un dispositivo usando AMQP. Il dispositivo può facoltativamente fornire un dizionario di proprietà dell'applicazione o varie proprietà del messaggio, ad esempio l'ID del messaggio.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per inviare messaggi da dispositivo a cloud da un dispositivo.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Note aggiuntive

* Le connessioni AMQP potrebbero essere interrotte a causa di un problema di rete o della scadenza del token di autenticazione (generato nel codice). Il client del servizio deve gestire tali circostanze e ristabilire la connessione e i collegamenti, se necessario. Se un token di autenticazione scade, il client può evitare un calo della connessione tramite il rinnovo proattivo del token prima della scadenza.

* In alcuni casi il client deve essere in grado di gestire correttamente i reindirizzamenti dei collegamenti. Per comprendere tale operazione, vedere la documentazione del client AMQP.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul protocollo AMQP, vedere la [specifica AMQP versione 1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Per ulteriori informazioni sulla messaggistica dell'hub Internet, vedere:

* [Messaggi da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Supporto per protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Supporto per il protocollo di trasporto di telemetria di Accodamento messaggi (MQTT)](./iot-hub-mqtt-support.md)
