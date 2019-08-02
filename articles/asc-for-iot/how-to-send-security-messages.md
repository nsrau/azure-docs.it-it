---
title: Inviare i messaggi di sicurezza al centro sicurezza di Azure per le cose | Microsoft Docs
description: Informazioni su come inviare i messaggi di sicurezza tramite il Centro sicurezza di Azure.
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
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596215"
---
# <a name="send-security-messages-sdk"></a>Inviare l'SDK dei messaggi di sicurezza

Questa guida dettagliata illustra il Centro sicurezza di Azure per le funzionalità del servizio Internet quando si sceglie di raccogliere e inviare i messaggi di sicurezza del dispositivo senza usare un centro sicurezza di Azure per l'agente Internet e illustra come eseguire questa operazione.  

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Usare l'API di invio messaggio di sicurezza per C#
> * Usare l'API di invio messaggio di sicurezza per C

## <a name="azure-security-center-for-iot-capabilities"></a>Centro sicurezza di Azure per le funzionalità di Internet delle cose

Il Centro sicurezza di Azure per Internet è in grado di elaborare e analizzare qualsiasi tipo di dati dei messaggi di sicurezza purché i dati inviati siano conformi al [Centro sicurezza di Azure per lo schema](https://aka.ms/iot-security-schemas) degli oggetti Internet e il messaggio sia impostato come messaggio di sicurezza.

## <a name="security-message"></a>Messaggio di sicurezza

Il Centro sicurezza di Azure per le cose definisce un messaggio di sicurezza con i criteri seguenti:
- Se il messaggio è stato inviato con Azure. CC# /SDK
- Se il messaggio è conforme allo [schema del messaggio di sicurezza](https://aka.ms/iot-security-schemas)
- Se il messaggio è stato impostato come messaggio di sicurezza prima dell'invio

Ogni messaggio di sicurezza include i metadati del mittente `AgentId` `MessageSchemaVersion` , ad esempio `AgentVersion`, e un elenco di eventi di sicurezza.
Lo schema definisce le proprietà valide e obbligatorie del messaggio di sicurezza, inclusi i tipi di eventi.

>[!Note]
> I messaggi inviati che non sono conformi allo schema vengono ignorati. Assicurarsi di verificare lo schema prima di avviare l'invio di dati, perché i messaggi ignorati non vengono attualmente archiviati. 

>[!Note]
> I messaggi inviati che non sono stati impostati come messaggi di sicurezza con Azure Internet perC# l'uso di Azure. C/SDK non verranno indirizzati al centro sicurezza di Azure per la pipeline Internet delle cose

## <a name="valid-message-example"></a>Esempio di messaggio valido

Nell'esempio seguente viene illustrato un oggetto messaggio di sicurezza valido. Nell'esempio sono contenuti i metadati del messaggio `ProcessCreate` e un evento di sicurezza.

Una volta impostato come messaggio di sicurezza e inviato, questo messaggio verrà elaborato dal centro sicurezza di Azure per l'it.

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

Inviare i messaggi di sicurezza senza usare il Centro sicurezza di Azure per l'agente Internet per l'uso dell' [SDK per C# dispositivi Azure](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) Internet o [Azure](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Per inviare i dati del dispositivo dai dispositivi per l'elaborazione da parte del Centro sicurezza di Azure, usare una delle API seguenti per contrassegnare i messaggi per il routing corretto al centro sicurezza di Azure per la pipeline di elaborazione degli elementi. 

Tutti i dati inviati, anche se contrassegnati con l'intestazione corretta, devono essere conformi anche al [Centro sicurezza di Azure per lo schema dei messaggi dell'](https://aka.ms/iot-security-schemas)intero. 

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
- Leggi il Centro sicurezza di Azure per [informazioni generali](overview.md) sul servizio Internet
- Scopri di più sul centro sicurezza di Azure per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
