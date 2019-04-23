---
title: Invio di messaggi di sicurezza al Centro sicurezza di Azure per l'anteprima di IoT | Microsoft Docs
description: Informazioni su come inviare i messaggi di sicurezza usando il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797347"
---
# <a name="send-security-messages-sdk"></a>Inviare l'SDK dei messaggi di sicurezza

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida illustra Azure Security Center (Centro sicurezza di AZURE) per le funzionalità del servizio IoT quando si sceglie di raccogliere e inviare i messaggi di sicurezza il dispositivo senza l'utilizzo di un Centro sicurezza di AZURE per l'agente di IoT e spiega come eseguire questa operazione.  

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Usare l'API di invio messaggio di sicurezza per C#
> * Usare l'API di invio messaggio di sicurezza per C

## <a name="asc-for-iot-capabilities"></a>Centro sicurezza di AZURE per le funzionalità di IoT

Centro sicurezza di AZURE per IoT può elaborare e analizzare qualsiasi tipo di dati di sicurezza messaggio, purché la conformità dei dati inviati per la [Centro sicurezza di AZURE per IoT schema](https://aka.ms/iot-security-schemas) e il messaggio è impostato su un messaggio di sicurezza.

## <a name="security-message"></a>Messaggio di sicurezza

Centro sicurezza di AZURE per IoT definisce un messaggio di sicurezza usando i criteri seguenti:
- Se il messaggio è stato inviato con Azure IoT C /C# SDK
- Se il messaggio soddisfa i requisiti di [schema del messaggio di sicurezza](https://aka.ms/iot-security-schemas)
- Se il messaggio è stato impostato come un messaggio di sicurezza prima dell'invio

Ogni messaggio di sicurezza sono inclusi i metadati del mittente, ad esempio `AgentId`, `AgentVersion`, `MessageSchemaVersion` e un elenco di eventi di sicurezza.
Lo schema definisce le proprietà valide e necessarie del messaggio di sicurezza, inclusi i tipi di eventi.

[!NOTE]
> I messaggi inviati che non sono conformi allo schema vengono ignorati. Assicurarsi di verificare lo schema prima di avviare l'invio di dati, perché i messaggi ignorati non vengono attualmente archiviati. 
> I messaggi inviati che non sono state impostate come un messaggio di sicurezza usando Azure IoT C /C# SDK non verrà indirizzato il Centro sicurezza di AZURE per IoT pipeline

## <a name="valid-message-example"></a>Esempio di messaggio valido

L'esempio seguente illustra un oggetto messaggio di sicurezza valido. L'esempio contiene i metadati del messaggio e l'altra `ProcessCreate` evento di sicurezza.

Una volta impostata come un messaggio di sicurezza e invio, questo messaggio verrà elaborato dal Centro sicurezza di AZURE per IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Inviare messaggi di sicurezza 

Inviare i messaggi di sicurezza senza usare il Centro sicurezza di AZURE per l'agente di IoT, usando il [IoT di Azure C# SDK per dispositivi](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) oppure [dispositivo Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Per inviare i dati del dispositivo dai dispositivi per l'elaborazione nel Centro sicurezza di Azure per IoT, usare una delle seguenti API per contrassegnare i messaggi per il routing corretto alla pipeline di elaborazione del Centro sicurezza di Azure per IoT. I messaggi inviati in questo modo verranno elaborati e visualizzati come informazioni di sicurezza nel Centro sicurezza di Azure per IoT all'interno dell'hub IoT o del Centro sicurezza di Azure. 

Tutti i dati inviati, anche se contrassegnati con l'intestazione corretta, devono inoltre essere conformi allo [schema di messaggio del Centro sicurezza di Azure per IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>API di invio messaggio di sicurezza

L'API di **invio messaggio di sicurezza** è attualmente disponibile in C e C#.  

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

## <a name="next-steps"></a>Passaggi successivi
- Leggere la [Panoramica](overview.md) sul servizio del Centro sicurezza di Azure per IoT
- Vedere altre informazioni sull'[Architettura](architecture.md) del Centro sicurezza di Azure per IoT
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
