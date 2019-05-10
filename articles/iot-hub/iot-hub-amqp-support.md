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
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473507"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Comunicare con l'hub IoT usando il protocollo AMQP

L'IoT Hub supporta [AMQP versione 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) per offrire un'ampia gamma di funzionalità tramite gli endpoint per il dispositivo e per il servizio. Questo documento descrive l'utilizzo di client AMQP per la connessione all'IoT Hub per poter usare la funzionalità dell'IoT Hub.

## <a name="service-client"></a>Client del servizio

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Connessione e l'autenticazione all'IoT Hub (servizio client)
Per connettersi all'IoT Hub tramite AMQP, un client può usare la [sicurezza basata sulle attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o [Simple Authentication and Security Layer SASL () autenticazione](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Le informazioni seguenti sono necessari per il client del servizio:

| Informazioni | Value | 
|-------------|--------------|
| Nome host hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome chiave | `service` |
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
| Service | Collegamento ricevitore | `/messages/serviceBound/feedback` | Completamento e rifiuto abbandono i messaggi di feedback proveniente dai dispositivi su questo collegamento ricevuti dal servizio. Visualizzare [qui](./iot-hub-devguide-messages-c2d.md#message-feedback) per altre informazioni sui messaggi con commenti. |

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


### <a name="additional-notes"></a>Note aggiuntive
* Le connessioni AMQP potrebbero essere interrotte a causa di anomalia di rete o la scadenza del token di autenticazione (generato nel codice). Il client del servizio deve gestire queste situazioni e ristabilire la connessione e i collegamenti se necessario. Nel caso di scadenza di token di autenticazione, il client può anche in modo proattivo rinnovare il token prima della relativa scadenza per evitare un calo di connessione.
* In alcuni casi, il client deve essere in grado di gestire correttamente i reindirizzamenti di collegamento. Fare riferimento alla documentazione del client AMQP su come eseguire questa operazione.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Ricevere messaggi da cloud a dispositivo (client di dispositivi e moduli)
Come indicato di seguito sono riportati i collegamenti AMQP usati sul lato dispositivo:

| Creato da | Tipo di collegamento | Percorso del collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Dispositivi | Collegamento ricevitore | `/devices/<deviceID>/messages/devicebound` | Messaggi C2D destinati ai dispositivi vengono ricevuti su questo collegamento da ogni dispositivo di destinazione. |
| Dispositivi | Collegamento mittente | `/messages/serviceBound/feedback` | C2D commenti sui messaggi inviati al servizio tramite questo collegamento dai dispositivi. |
| Moduli | Collegamento ricevitore | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | Messaggi C2D destinati ai moduli vengono ricevuti su questo collegamento da ogni modulo di destinazione. |
| Moduli | Collegamento mittente | `/messages/serviceBound/feedback` | C2D commenti sui messaggi inviati al servizio tramite questo collegamento dai moduli. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo AMQP, vedere la [specifica di AMQP 1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Per altre informazioni sulla messaggistica dell'IoT Hub, vedere:

* [Messaggi da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Supportare protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Supporto del protocollo MQTT](./iot-hub-mqtt-support.md)
