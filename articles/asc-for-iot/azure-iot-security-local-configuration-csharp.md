---
title: Configurazione locale dell'agente di sicurezza
description: Altre informazioni sul Centro sicurezza di Azure per il servizio di sicurezza IoT, il file di configurazione locale dell'agente di sicurezza per C.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311671"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Informazioni sul file di configurazione locale (agente C

Il Security Agent di Azure Security Center per IoT usa le configurazioni di un file di configurazione locale.

L'agente di sicurezza legge il file di configurazione una volta all'avvio dell'agente. Le configurazioni disponibili nel file di configurazione locale contengono sia la configurazione di autenticazione che altre configurazioni correlate all'agente.

L'agente di sicurezza di C'è utilizza più file di configurazione:The C's security agent uses multiple configuration files:

- **General.config** - Configurazioni correlate all'agente.
- **Authentication.config-** Configurazione correlata all'autenticazione (inclusi i dettagli di autenticazione).
- **SecurityIotInterface.config** - Configurazioni correlate all'IoT.SecurityIotInterface.config - IoT related configurations.

I file di configurazione contengono la configurazione predefinita. La configurazione dell'autenticazione viene popolata durante l'installazione dell'agente e le modifiche al file di configurazione vengono apportate al riavvio dell'agente.

## <a name="configuration-file-location"></a>Percorso del file di configurazione

Per Linux:

- I file di configurazione `/var/ASCIoTAgent`del sistema operativo si trovano in .

Per Windows:

- I file di configurazione del sistema operativo si trovano all'interno della directory dell'agente di protezione.

### <a name="generalconfig-configurations"></a>Configurazioni General.config

| Nome della configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| agentId | GUID | Identificatore univoco dell'agenteAgent unique identifier |
| readRemoteConfigurationTimeout (informazioni in base al servizio di windows) | TimeSpan | Periodo di tempo per il recupero della configurazione remota dall'hub IoT. Se l'agente non è in grado di recuperare la configurazione entro il tempo specificato, si stimerà l'operazione.|
| schedulerInterval (intervallo di pianificazione) | TimeSpan | Intervallo dell'utilità di pianificazione interna. |
| producerInterval (intervallo) | TimeSpan | Intervallo di lavoro del produttore di eventi. |
| ConsumerInterval | TimeSpan | Intervallo di lavoro del consumer di eventi. |
| highPriorityQueueSizePercentage | 0 numero < < 1 | Parte della cache totale dedicata ai messaggi con priorità alta. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "Debug"  | I messaggi di log uguali e superiori a questa gravità vengono registrati nella console di debug (Syslog in Linux). |
| fileLogLevel (livello di registro) |  "Off", "Fatal", "Error", "Warning", "Information", "Debug"| I messaggi di log uguali e superiori a questa gravità vengono registrati nel file (Syslog in Linux). |
| diagnosticVerbosityLevel | "Nessuno", "Alcuni", "Tutti", | Livello di verbosità degli eventi di diagnostica. Nessuno - gli eventi di diagnostica non vengono inviati, Alcuni - Solo gli eventi di diagnostica con priorità alta vengono inviati, Tutti - tutti i log vengono inviati anche come eventi di diagnostica. |
| logFilePercorso | Percorso del file | Se fileLogLevel > Off, i registri vengono scritti in questo file. |
| defaultEventPriority (Prioritàevento predefinita) | "Alto", "Basso", "Disattivato" | Priorità predefinita dell'evento. |

### <a name="generalconfig-example"></a>Esempio General.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nome configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| moduleName | string | Nome dell'identità del modulo di sicurezza. Questo nome deve corrispondere al nome dell'identità del modulo nel dispositivo. |
| deviceId | string | ID del dispositivo (come registrato nell'hub IoT di Azure). || schedulerInterval (intervallo di pianificazione) | Stringa TimeSpan | Intervallo dell'utilità di pianificazione interna. |
| gatewayNome host | string | Nome host dell'hub Iot di Azure. In genere <my-hub>.azure-devices.net |
| filePath | string - percorso del file | Percorso del file che contiene il segreto di autenticazione.|
| type | "SymmetricKey", "SelfSignedCertificate" | Segreto utente per l'autenticazione. Scegliere *SymmetricKey* se il segreto utente è una chiave simmetrica, scegliere *il certificato autofirmato* se il segreto è un certificato autofirmato. |
| identity | "DPS", "Modulo", "Dispositivo" | Identità di autenticazione: DPS se l'autenticazione viene eseguita tramite DPS, Module se l'autenticazione viene eseguita utilizzando le credenziali del modulo o il dispositivo se l'autenticazione viene eseguita utilizzando le credenziali del dispositivo.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile se il certificato è archiviato in un file, archiviare se il certificato si trova in un archivio certificati. |
| idScope (contenitore per i documenti | string | Ambito ID di DPS |
| registrationId (id registrazione) | string  | ID di registrazione del dispositivo DPS. |
|

### <a name="authenticationconfig-example"></a>Esempio Authentication.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config (informazioni in lingua inlingua sconosciuta)

| Nome della configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| transportType (tipo di trasporto) | "Ampq" "Mqtt" | Tipo di trasporto Hub IoT. |
|

### <a name="securityiotinterfaceconfig-example"></a>Esempio SecurityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoT
- Altre informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn more about Azure Security Center for IoT [Architecture](architecture.md)
- Abilitare il Centro sicurezza di Azure per il servizio IoTEnable the Azure Security Center for IoT [service](quickstart-onboard-iot-hub.md)
- Leggere le [domande frequenti](resources-frequently-asked-questions.md) sul Centro sicurezza di Azure per il servizio IoTRead the Azure Security Center for IoT service FAQ
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Comprendere gli avvisi di sicurezzaUnderstand security [alerts](concept-security-alerts.md)
