---
title: Inviare i messaggi di sicurezza al Centro sicurezza di Azure per l'IoT Documenti Microsoft
description: Informazioni su come inviare messaggi di sicurezza usando il Centro sicurezza di Azure per l'IoT.Learn how to send your security messages using Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963819"
---
# <a name="send-security-messages-sdk"></a>Inviare l'SDK dei messaggi di sicurezza

Questa guida alle procedure illustra il Centro sicurezza di Azure per le funzionalità del servizio IoT quando si sceglie di raccogliere e inviare i messaggi di sicurezza del dispositivo senza usare un agente di sicurezza di Azure per IoT e spiega come farlo.  

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Inviare messaggi di sicurezza usando Azure IoT C SDKSend security messages using the Azure IoT C SDK
> * Inviare messaggi di sicurezza usando l'SDK di Azure IoT C
> * Inviare messaggi di sicurezza usando Azure IoT Python SDKSend security messages using the Azure IoT Python SDK
> * Inviare messaggi di sicurezza usando l'SDK di Azure IoT Node.jsSend security messages using the Azure IoT Node.js SDK
> * Inviare messaggi di sicurezza usando L'SDK Java di Azure IoTSend security messages using the Azure IoT Java SDK


## <a name="azure-security-center-for-iot-capabilities"></a>Centro sicurezza di Azure per le funzionalità IoTAzure Security Center for IoT capabilities

Il Centro sicurezza di Azure per ioT può elaborare e analizzare qualsiasi tipo di dati dei messaggi di sicurezza, purché i dati inviati siano conformi allo [schema IoT](https://aka.ms/iot-security-schemas) di Azure e il messaggio venga impostato come messaggio di sicurezza.

## <a name="security-message"></a>Messaggio di sicurezza

Centro sicurezza di Azure per IoT definisce un messaggio di sicurezza usando i criteri seguenti:Azure Security Center for IoT defines a security message using the following criteria:
- Se il messaggio è stato inviato con Azure IoT SDK
- Se il messaggio è conforme allo schema dei messaggi di [protezione](https://aka.ms/iot-security-schemas)
- Se il messaggio è stato impostato come messaggio di sicurezza prima dell'invio

Ogni messaggio di sicurezza include i `AgentId` `AgentVersion`metadati del mittente, ad esempio , `MessageSchemaVersion` e un elenco di eventi di sicurezza.
Lo schema definisce le proprietà valide e obbligatorie del messaggio di sicurezza, inclusi i tipi di eventi.

>[!Note]
> I messaggi inviati che non sono conformi allo schema vengono ignorati. Assicurarsi di verificare lo schema prima di avviare l'invio di dati, perché i messaggi ignorati non vengono attualmente archiviati. 

>[!Note]
> I messaggi inviati che non sono stati impostati come messaggio di sicurezza usando Azure IoT SDK non verranno instradati al Centro sicurezza di Azure per la pipeline IoT.Messages sent that were not set as a security message using the Azure IoT SDK will not be routed to the Azure Security Center for IoT pipeline.

## <a name="valid-message-example"></a>Esempio di messaggio valido

L'esempio seguente mostra un oggetto messaggio di sicurezza valido. L'esempio contiene i `ProcessCreate` metadati del messaggio e un evento di sicurezza.

Una volta impostato come messaggio di sicurezza e inviato, questo messaggio verrà elaborato dal Centro sicurezza di Azure per IoT.Once set as a security message and sent, this message will be processed by Azure Security Center for IoT.

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

Inviare messaggi di sicurezza *senza* usare il Centro sicurezza di Azure per l'agente IoT, utilizzando [Azure IoT C device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), Azure [IoT C, SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)per dispositivi , Azure [IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), Azure [IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)o Azure [IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Per inviare i dati del dispositivo dai dispositivi per l'elaborazione dal Centro sicurezza di Azure per IoT, usare una delle API seguenti per contrassegnare i messaggi per il routing corretto al Centro sicurezza di Azure per la pipeline di elaborazione IoT.To send the device data from your devices for processing by Azure Security Center for IoT, use one of the following APIs to mark messages for correct routing to KoT processing pipeline. 

Tutti i dati inviati, anche se contrassegnati con l'intestazione corretta, devono essere conformi anche allo schema dei messaggi del [Centro sicurezza di Azure per IoT.](https://aka.ms/iot-security-schemas) 

### <a name="send-security-message-api"></a>API di invio messaggio di sicurezza 

L'API **Invia messaggi** di sicurezza è attualmente disponibile in C e C, Python, Node.js e Java.  

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

Per usare l'API Python è necessario installare il pacchetto [azure-iot-device](https://pypi.org/project/azure-iot-device/).

Quando si usa l'API Python, è possibile inviare il messaggio di sicurezza tramite il modulo o tramite il dispositivo utilizzando la stringa di connessione univoca del dispositivo o del modulo. Quando si utilizza l'esempio di script Python seguente, con un dispositivo, utilizzare **IoTHubDeviceClient**e con un modulo, utilizzare **IoTHubModuleClient**. 

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
- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoT
- Altre informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn more about Azure Security Center for IoT [Architecture](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
