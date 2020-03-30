---
title: Informazioni sul Centro sicurezza di Azure per le configurazioni locali degli agenti per C . Documenti Microsoft
description: Informazioni sul Centro sicurezza di Azure per le configurazioni locali degli agenti per C.Learn about Azure Security Center for agent local configurations for C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600540"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Informazioni sul file LocalConfiguration.json - Agente C

Il Security Agent di Azure Security Center per IoT usa le configurazioni di un file di configurazione locale.
L'agente di sicurezza legge la configurazione una sola volta, all'avvio dell'agente.
La configurazione trovata nel file di configurazione locale contiene la configurazione di autenticazione e altre configurazioni correlate all'agente.
Il file contiene le configurazioni in coppie "Chiave-Valore" in notazione JSON e le configurazioni vengono popolate quando viene installato l'agente. 

Per impostazione predefinita, il file si trova in: /var/ASCIoTAgent/LocalConfiguration.json

Le modifiche apportate al file di configurazione vengono apportate al riavvio dell'agente. 

## <a name="security-agent-configurations-for-c"></a>Configurazioni degli agent di sicurezza per CSecurity agent configurations for C
| Nome della configurazione | Valori possibili | Dettagli | 
|:-----------|:---------------|:--------|
| AgentId | GUID | Identificatore univoco dell'agente |
| TriggerdEventsIntervalTriggerdEventsInterval | Stringa ISO8601 | Intervallo dell'utilità di pianificazione per la raccolta di eventi attivati |
| ConnectionTimeout | Stringa ISO8601 | Periodo di tempo prima che la connessione a IoThub venga stentata |
| Authentication | Jsonobject | Configurazione dell'autenticazione. Questo oggetto contiene tutte le informazioni necessarie per l'autenticazione con IoTHub |
| Identità | "DPS", "SecurityModule", "Dispositivo" | Identità di autenticazione - DPS se l'autenticazione viene eseguita tramite DPS, SecurityModule se l'autenticazione viene eseguita tramite le credenziali del modulo di sicurezza o il dispositivo se l'autenticazione viene eseguita con le credenziali del dispositivo |
| AuthenticationMethod (Metodo Authentication) | "SasToken", "SelfSignedCertificate" | il segreto utente per l'autenticazione - Scegliere SasToken se il segreto di utilizzo è una chiave simmetrica, scegliere il certificato autofirmato se il segreto è un certificato autofirmato  |
| FilePath | Percorso del file (stringa) | Percorso del file che contiene il segreto di autenticazione |
| HostName | string | Nome host dell'hub iot di Azure. di solito <my-hub>.azure-devices.net |
| deviceId | string | The ID of the device (as registered in Azure IoT Hub) |
| DPS | Jsonobject | Configurazioni correlate a DPS |
| IDScope (Ambito IDScope) | string | Ambito ID di DPS |
| ID registrazione | string  | ID di registrazione del dispositivo DPS |
| Registrazione | Jsonobject | Configurazioni correlate al logger dell'agenteAgent logger related configurations |
| SystemLoggerMinimumSeverity (Gravità minima di SystemLogger) | 0 <numero < | messaggi di log uguali e superiori a questa gravità verranno registrati in /var/log/syslog (0 è il livello di gravità più basso) |
| DiagnosticEventMinimumSeverity (Severitità di DiagnosticaEventMinimum | 0 <numero < | messaggi di log uguali e superiori a questa gravità verranno inviati come eventi di diagnostica (0 è il livello più basso) |

## <a name="security-agent-configurations-code-example"></a>Esempio di codice di configurazione dell'agente di sicurezza
```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoT
- Altre informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn more about Azure Security Center for IoT [Architecture](architecture.md)
- Abilitare il Centro sicurezza di Azure per il servizio IoTEnable the Azure Security Center for IoT [service](quickstart-onboard-iot-hub.md)
- Leggere le [domande frequenti](resources-frequently-asked-questions.md) sul Centro sicurezza di Azure per il servizio IoTRead the Azure Security Center for IoT service FAQ
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Comprendere gli avvisi di sicurezzaUnderstand security [alerts](concept-security-alerts.md)