---
title: Guida alle indagini sui dispositivi per il Centro sicurezza di Azure | Microsoft Docs
description: Questa guida illustra come usare il Centro sicurezza di Azure per l'analisi di un dispositivo per le cose sospette usando Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596245"
---
# <a name="investigate-a-suspicious-iot-device"></a>Analizzare un dispositivo IoT sospetto

Il Centro sicurezza di Azure per gli avvisi del servizio Internet fornisce indicazioni chiare quando si sospetta che i dispositivi Internet siano coinvolti in attività sospette o quando sono presenti indicazioni per la compromissione di un dispositivo. 

In questa guida, usare i suggerimenti per l'analisi forniti per determinare i potenziali rischi per l'organizzazione, decidere come correggere e individuare i modi migliori per evitare attacchi simili in futuro.  

> [!div class="checklist"]
> * Trovare i dati del dispositivo
> * Analizzare usando le query kql


## <a name="how-can-i-access-my-data"></a>Come è possibile accedere ai dati?

Per impostazione predefinita, il Centro sicurezza di Azure per le cose archivia gli avvisi di sicurezza e le raccomandazioni nell'area di lavoro Log Analytics. È anche possibile scegliere di archiviare i dati di sicurezza non elaborati.

Per individuare l'area di lavoro Log Analytics per l'archiviazione dei dati:

1. Aprire l'hub IoT. 
1. In **sicurezza**fare clic su **Panoramica**e quindi selezionare **Impostazioni**.
1. Modificare i dettagli di configurazione dell'area di lavoro Log Analytics. 
1. Fare clic su **Save**. 

In seguito alla configurazione, eseguire le operazioni seguenti per accedere ai dati archiviati nell'area di lavoro Log Analytics:

1. Selezionare e fare clic su un centro sicurezza di Azure per gli avvisi relativi all'intero hub. 
1. Fare clic su **Further investigation** (Ulteriori indagini). 
1. Selezionare **To see which devices have this alert click here and view the DeviceId column** (Per vedere quali dispositivi hanno questo avviso fare clic qui e visualizzare la colonna DeviceId).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Procedure di indagine per dispositivi IoT sospetti

Per visualizzare informazioni dettagliate e dati non elaborati sui dispositivi Internet, passare all'area di lavoro Log Analytics [per accedere ai dati](#how-can-i-access-my-data).

Vedere le query KQL di esempio seguenti per iniziare a esaminare gli avvisi e le attività nel dispositivo.

### <a name="related-alerts"></a>Avvisi correlati

Per sapere se gli altri avvisi sono stati attivati intorno alla stessa ora, usare la query kql seguente:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Utenti con accesso

Per scoprire quali utenti hanno accesso a questo dispositivo, usare la query kql seguente: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Usare questi dati per scoprire: 
- Quali utenti hanno accesso al dispositivo
- Gli utenti con accesso hanno i livelli di autorizzazione previsti?

### <a name="open-ports"></a>Porte aperte

Per individuare le porte del dispositivo attualmente in uso o usate, usare la query KQL seguente: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Usare questi dati per scoprire:
- Quali socket di ascolto sono attualmente attivi sul dispositivo
- Devono essere consentiti i socket in ascolto attualmente attivi?
- Sono presenti indirizzi remoti sospetti connessi al dispositivo?

### <a name="user-logins"></a>Account di accesso utente

Per trovare gli utenti che hanno eseguito l'accesso al dispositivo, usare la query KQL seguente: 
 
 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Usare i risultati della query per scoprire:
- Quali utenti hanno eseguito l'accesso al dispositivo
- Gli utenti che hanno eseguito l'accesso dovrebbero accedere?
- Se gli utenti che hanno eseguito l'accesso si connettono da indirizzi IP previsti o imprevisti
  
### <a name="process-list"></a>Elenco processi

Per sapere se l'elenco dei processi è quello previsto, usare la query KQL seguente: 

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Usare i risultati della query per scoprire:

- Se sono stati eseguiti processi sospetti sul dispositivo
- Se i processi sono stati eseguiti dagli utenti appropriati
- Se le esecuzioni della riga di comando contenevano gli argomenti corretti e previsti

## <a name="next-steps"></a>Passaggi successivi

Dopo aver analizzato un dispositivo e ottenuto una migliore comprensione dei rischi, si potrebbe voler considerare la [configurazione di avvisi personalizzati](quickstart-create-custom-alerts.md) per migliorare le condizioni di sicurezza della soluzione IoT. Se non si ha già un agente di dispositivo, per migliorare i risultati prendere in considerazione la [distribuzione di un agente protezione](how-to-deploy-agent.md) oppure [la modifica della configurazione di un agente di dispositivo esistente](how-to-agent-configuration.md). 
