---
title: Inviare il Defender per i messaggi di sicurezza dei dispositivi
description: Informazioni su come inviare i messaggi di sicurezza usando Defender per l'it.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: devx-track-js
ms.openlocfilehash: 5217940a3696bd001db421e61be8313453807c8b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318518"
---
# <a name="send-security-messages-sdk"></a>Inviare l'SDK dei messaggi di sicurezza

Questa guida dettagliata illustra il Defender per le funzionalità del servizio Internet delle cose quando si sceglie di raccogliere e inviare i messaggi di sicurezza del dispositivo senza usare un Defender per l'agente.

Questa guida illustra come eseguire queste operazioni:

> [!div class="checklist"]
> * Inviare messaggi di sicurezza tramite l'SDK di Azure.
> * Inviare messaggi di sicurezza tramite l'SDK di Azure per C#
> * Inviare messaggi di sicurezza tramite l'SDK Python di Azure
> * Inviare messaggi di sicurezza tramite l'SDK Node.js di Azure.
> * Inviare messaggi di sicurezza tramite l'SDK Java per Azure

## <a name="defender-for-iot-capabilities"></a>Defender per le funzionalità di Internet delle cose

Defender for Internet è in grado di elaborare e analizzare qualsiasi tipo di dati dei messaggi di sicurezza purché i dati inviati siano conformi al [Defender per lo schema](https://aka.ms/iot-security-schemas) Internet e il messaggio sia impostato come messaggio di sicurezza.

## <a name="security-message"></a>Messaggio di sicurezza

Defender for Internet degli argomenti definisce un messaggio di sicurezza con i criteri seguenti:

- Se il messaggio è stato inviato con l'SDK di Azure.
- Se il messaggio è conforme allo [schema del messaggio di sicurezza](https://aka.ms/iot-security-schemas)
- Se il messaggio è stato impostato come messaggio di sicurezza prima dell'invio

Ogni messaggio di sicurezza include i metadati del mittente, ad `AgentId` esempio `AgentVersion` , `MessageSchemaVersion` e un elenco di eventi di sicurezza.
Lo schema definisce le proprietà valide e obbligatorie del messaggio di sicurezza, inclusi i tipi di eventi.

> [!NOTE]
> I messaggi inviati che non sono conformi allo schema vengono ignorati. Assicurarsi di verificare lo schema prima di avviare l'invio di dati, perché i messaggi ignorati non vengono attualmente archiviati.

> [!NOTE]
> I messaggi inviati che non sono stati impostati come messaggi di sicurezza con Azure Internet Users SDK non verranno indirizzati alla pipeline Defender for Internet.

## <a name="valid-message-example"></a>Esempio di messaggio valido

Nell'esempio seguente viene illustrato un oggetto messaggio di sicurezza valido. Nell'esempio sono contenuti i metadati del messaggio e un `ProcessCreate` evento di sicurezza.

Una volta impostato come messaggio di sicurezza e inviato, questo messaggio verrà elaborato da Defender per l'it.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Inviare messaggi di sicurezza

Inviare i messaggi di sicurezza *senza* usare Defender per l'agente per l'uso di [Azure](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), con l'SDK per dispositivi di Azure per dispositivi, Azure Azure Internet SDK per [dispositivi](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), Azure [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure Azure Azure SDK](https://github.com/Azure/azure-iot-sdk-python)o [Java SDK per Azure](https://github.com/Azure/azure-iot-sdk-java).

Per inviare i dati del dispositivo dai dispositivi per l'elaborazione da parte di Defender, usare una delle API seguenti per contrassegnare i messaggi per il routing corretto a Defender per la pipeline di elaborazione degli elementi di lavoro.

Tutti i dati inviati, anche se contrassegnati con l'intestazione corretta, devono essere conformi anche al [Defender per lo schema del messaggio](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>API di invio messaggio di sicurezza

L'API **Invia messaggi di sicurezza** è attualmente disponibile in C e C#, Python, Node.js e Java.

#### <a name="c-api"></a>API C

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>API C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>API Node.js

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>API Python

Per usare l'API Python è necessario installare il pacchetto [Azure-cose-dispositivo](https://pypi.org/project/azure-iot-device/).

Quando si usa l'API Python, è possibile inviare il messaggio di sicurezza tramite il modulo o tramite il dispositivo usando la stringa di connessione del dispositivo o del modulo univoco. Quando si usa l'esempio di script Python seguente, con un dispositivo, usare **IoTHubDeviceClient**e con un modulo, usare **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>API Java

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
