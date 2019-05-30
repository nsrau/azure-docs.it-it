---
title: Informazioni sul supporto di AMQP dell'Hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - supporto per i dispositivi connessi all'IoT Hub per il dispositivo e per il servizio endpoint tramite il protocollo AMQP. Include informazioni sul supporto AMQP nel dispositivo Azure IoT SDK integrato.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297410"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Comunicare con l'hub IoT usando il protocollo AMQP

L'IoT Hub supporta [AMQP versione 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) per offrire un'ampia gamma di funzionalità tramite gli endpoint per il dispositivo e per il servizio. Questo documento descrive l'utilizzo di client AMQP per la connessione all'IoT Hub per poter usare la funzionalità dell'IoT Hub.

## <a name="service-client"></a>Client del servizio

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Connessione e l'autenticazione all'IoT Hub (servizio client)
Per connettersi all'IoT Hub tramite AMQP, un client può usare la [sicurezza basata sulle attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o [Simple Authentication and Security Layer SASL () autenticazione](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Le informazioni seguenti sono necessari per il client del servizio:

| Informazioni | Value | 
|-------------|--------------|
| Nome host dell'Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome della chiave | `service` |
| Chiave di accesso | Chiave primaria o secondaria associata al servizio |
| Firma di accesso condiviso | Firma di accesso condiviso breve durata nel formato seguente: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (il codice per generare questa firma è reperibile [qui](./iot-hub-devguide-security.md#security-token-structure)).


Il frammento di codice seguente viene utilizzata [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi all'hub IoT tramite un collegamento mittente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Richiamo di messaggi da cloud a dispositivo (servizio client)
Viene descritto lo scambio di messaggi da cloud a dispositivo tra servizio e l'IoT Hub, nonché tra dispositivo e l'IoT Hub [qui](iot-hub-devguide-messages-c2d.md). Il client del servizio usa due collegamenti descritti di seguito per inviare messaggi e ricevere commenti e suggerimenti per i messaggi inviati in precedenza dai dispositivi.

| Creato da | Tipo di collegamento | Percorso del collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Service | Collegamento mittente | `/messages/devicebound` | Messaggi C2D destinati ai dispositivi vengono inviati da questo collegamento dal servizio. I messaggi inviati tramite questo collegamento hanno loro `To` proprietà impostato sul percorso di collegamento ricevitore del dispositivo di destinazione: ad esempio, `/devices/<deviceID>/messages/devicebound`. |
| Service | Collegamento ricevitore | `/messages/serviceBound/feedback` | Completamento e rifiuto abbandono i messaggi di feedback proveniente dai dispositivi su questo collegamento ricevuti dal servizio. Per altre informazioni sui messaggi di feedback, vedere [qui](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Il frammento di codice seguente viene illustrato come creare un messaggio C2D e inviarlo a un dispositivo usando [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Per ricevere commenti e suggerimenti, client del servizio crea un collegamento ricevitore. Il frammento di codice seguente viene illustrato come eseguire questa operazione usando [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

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

Come illustrato in precedenza, un messaggio con commenti C2D ha tipo di contenuto di `application/vnd.microsoft.iothub.feedback.json` e proprietà nel rispettivo corpo JSON può essere usata per dedurre lo stato di recapito del messaggio originale:
* Tasto `statusCode` commenti e suggerimenti corpo ha uno di questi valori: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Chiave `deviceId` commenti e suggerimenti corpo è l'ID del dispositivo di destinazione.
* Chiave `originalMessageId` commenti e suggerimenti corpo con l'ID del messaggio originale C2D inviato dal servizio. Ciò consente di correlare i commenti e suggerimenti per i messaggi C2D.

### <a name="receive-telemetry-messages-service-client"></a>Ricevere messaggi di telemetria (servizio client)
Per impostazione predefinita, l'IoT Hub archivia i messaggi di telemetria da dispositivo inseriti in un hub di eventi predefinito. Il client del servizio può usare il protocollo AMQP per ricevere gli eventi archiviati.

A tale scopo, il client del servizio deve prima di connettersi all'endpoint dell'IoT Hub e ricevere un indirizzo di reindirizzamento per l'hub di eventi predefiniti. Client del servizio Usa quindi l'indirizzo fornito per la connessione all'hub eventi predefiniti.

In ogni passaggio, il client deve presentare le informazioni seguenti:
* Credenziali del servizio valido (token di firma di accesso condiviso servizio).
* Un percorso formattato in modo corretto per la partizione di gruppo di consumer che si desidera recuperare i messaggi. Per un ID di partizione e gruppo di consumer specifico, il percorso ha il formato seguente: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (il gruppo di consumer predefinito è `$Default`).
* Un predicato del filtro facoltativo per designare un punto di partenza nella partizione (può essere sotto forma di un timestamp di numero, offset o accodati sequenza).

Il frammento di codice seguente viene utilizzata [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per illustrare i passaggi precedenti.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
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

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Per un ID di dispositivo specificato, l'IoT Hub Usa un hash dell'ID del dispositivo per determinare la partizione per archiviare i messaggi in. Il frammento di codice precedente viene illustrato come ricevere gli eventi da una singola partizione di questo tipo. Si noti, tuttavia, che spesso una tipica applicazione deve recuperare gli eventi archiviati in tutte le partizioni di hub eventi.


## <a name="device-client"></a>Client del dispositivo

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>Connessione e l'autenticazione all'IoT Hub (client di dispositivo)
Per connettersi all'IoT Hub tramite AMQP, un dispositivo può usare la [sicurezza basata sulle attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o [Simple Authentication and Security Layer SASL () autenticazione](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Le informazioni seguenti sono necessari per il client del dispositivo:

| Informazioni | Value | 
|-------------|--------------|
| Nome host dell'Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Chiave di accesso | Chiave primaria o secondaria associata al dispositivo |
| Firma di accesso condiviso | Firma di accesso condiviso breve durata nel formato seguente: `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (il codice per generare questa firma è reperibile [qui](./iot-hub-devguide-security.md#security-token-structure)).


Il frammento di codice seguente viene utilizzata [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi all'hub IoT tramite un collegamento mittente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

I percorsi di collegamento seguenti sono supportati le operazioni del dispositivo:

| Creato da | Tipo di collegamento | Percorso del collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Dispositivi | Collegamento ricevitore | `/devices/<deviceID>/messages/devicebound` | Messaggi C2D destinati ai dispositivi vengono ricevuti su questo collegamento da ogni dispositivo di destinazione. |
| Dispositivi | Collegamento mittente | `/devices/<deviceID>messages/events` | I messaggi D2C inviati da un dispositivo vengono inviati tramite questo collegamento. |
| Dispositivi | Collegamento mittente | `/messages/serviceBound/feedback` | C2D commenti sui messaggi inviati al servizio tramite questo collegamento dai dispositivi. |


### <a name="receive-c2d-commands-device-client"></a>Ricezione di comandi C2D (client di dispositivo)
Comandi C2D inviati ai dispositivi arrivano sul `/devices/<deviceID>/messages/devicebound` collegamento. I dispositivi possono ricevere questi messaggi in batch e usare il payload di dati di messaggio, le proprietà del messaggio, le annotazioni o le proprietà dell'applicazione nel messaggio in base alle esigenze.

Il frammento di codice seguente viene utilizzata [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) C2D messaggi di un dispositivo.

```python
# ... 
# Create a receive client for the C2D receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

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
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Inviare messaggi di telemetria (client di dispositivo)
I dati di telemetria anche inviare messaggi tramite AMQP dai dispositivi. Il dispositivo può facoltativamente fornire un dizionario delle proprietà dell'applicazione o vari messaggi proprietà quali ID messaggio.

Il frammento di codice seguente viene utilizzata [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per inviare i messaggi D2C da un dispositivo.


```python
# ... 
# Create a send client for the D2C send link on the device
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
* Le connessioni AMQP potrebbero essere interrotte a causa di anomalia di rete o la scadenza del token di autenticazione (generato nel codice). Il client del servizio deve gestire queste situazioni e ristabilire la connessione e i collegamenti se necessario. Nel caso di scadenza di token di autenticazione, il client può anche in modo proattivo rinnovare il token prima della relativa scadenza per evitare un calo di connessione.
* In alcuni casi, il client deve essere in grado di gestire correttamente i reindirizzamenti di collegamento. Fare riferimento alla documentazione del client AMQP su come gestire questa operazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo AMQP, vedere la [specifica di AMQP 1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Per altre informazioni sulla messaggistica dell'IoT Hub, vedere:

* [Messaggi da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Supportare protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Supporto del protocollo MQTT](./iot-hub-mqtt-support.md)
