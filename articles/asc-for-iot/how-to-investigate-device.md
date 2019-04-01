---
title: Il Centro sicurezza di Azure per la Guida alle indagini sui dispositivi IoT Preview | Microsoft Docs
description: Questa Guida alle procedure spiega come usare il Centro sicurezza di Azure per IoT per analizzare un dispositivo IoT sospetto usando Log Analitica.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 6097954e09d5fd62c45f59b009d974d277bafc57
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755250"
---
# <a name="investigate-a-suspicious-iot-device"></a>Provare a utilizzare un dispositivo IoT sospetto

> [!IMPORTANT]
> Il Centro sicurezza di Azure per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il Centro sicurezza di Azure (ASC) per l'evidenza e gli avvisi del servizio IoT offrono indiscutibilmente quando i dispositivi IoT sono sospetta di coinvolgimento in attività sospette o quando le indicazioni presenti che un dispositivo sia compromesso. 

In questa Guida, usare i suggerimenti di indagine forniti per aiutare a determinare potenziali rischi per l'organizzazione, decidere come correggere e scoprire i sistemi migliori per impedire attacchi simili in futuro.  

> [!div class="checklist"]
> * Trovare i dati del dispositivo
> * Provare a utilizzare le query kql


## <a name="how-can-i-access-my-data"></a>Come è possibile accedere ai dati?

Per impostazione predefinita, Centro sicurezza di AZURE per IoT archivia gli avvisi di sicurezza e raccomandazioni nell'area di lavoro di Log Analitica. È anche possibile scegliere archiviare i dati di sicurezza non elaborati.

Per individuare l'area di lavoro di Log Analitica per l'archiviazione dei dati:

1. Aprire l'hub IoT, 
1. Fare clic su **sicurezza**, quindi selezionare **impostazioni**.
1. Modificare i dettagli di configurazione dell'area di lavoro di Log Analitica. 
1. Fare clic su **Save**. 

In seguito la configurazione, eseguire le operazioni seguenti per accedere ai dati archiviati nell'area di lavoro di Log Analitica:

1. Selezionare e fare clic su un Centro sicurezza di AZURE per l'avviso IoT nell'IoT Hub. 
1. Fare clic su **ulteriori indagini condotte**. 
1. Selezionare **per verificare quali dispositivi sono associati a questo avviso fare clic qui e visualizzare la colonna DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Passaggi di analisi per i dispositivi IoT sospetti

Per accedere a informazioni dettagliate e dati non elaborati sui dispositivi IoT, visitare l'area di lavoro di Log Analitica [per accedere ai dati](#how-can-i-access-my-data).

Controllare e analizzare i dati del dispositivo per i dettagli seguenti e le attività usando le query kql seguenti.

### <a name="related-alerts"></a>Avvisi correlati

Per sapere se gli altri avvisi sono stati attivati per l'utilizzo di tempo stesso kql nella query seguente:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Utenti con accesso

Per scoprire quali utenti hanno accesso a questo dispositivo, usare la query kql seguente: 

  ~~~
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
  ~~~
Usare questi dati per individuare: 
  1. Gli utenti che hanno accesso al dispositivo?
  2. Sono gli utenti con accesso con i livelli di autorizzazione come previsto? 

### <a name="open-ports"></a>Aprire le porte

Per trovare out quali porte nel dispositivo sono attualmente in uso o sono state usate usare kql nella query seguente: 

  ~~~
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
  ~~~

    Use this data to discover:
  1. Quali socket in attesa sono attualmente attivi nel dispositivo?
  2. Deve essere consentito il socket in attesa che sono attualmente attivi?
  3. Sono presenti eventuali indirizzi remoti sospetti connessi al dispositivo?

### <a name="user-logins"></a>Account di accesso utente

Per scoprire gli utenti connessi al dispositivo usano kql nella query seguente: 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. Gli utenti connessi al dispositivo?
  2. Gli utenti registrati in dovrebbero accedere?
  3. Hanno stabilito gli utenti registrati in una connessione da indirizzi IP previsti o imprevisti?
  
### <a name="process-list"></a>Elenco dei processi

Per determinare se l'elenco dei processi è la seguente query kql come uso previsto: 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. Si sono presenti processi sospetti in esecuzione nel dispositivo?
  2. I processi eseguiti dagli utenti appropriati?
  3. Le esecuzioni della riga di comando conteneva argomenti corretti e previsto?

## <a name="next-steps"></a>Passaggi successivi
Dopo aver analizzato un dispositivo e ottenere una migliore comprensione dei rischi, si potrebbe voler considerare [configurazione di avvisi personalizzati](quickstart-create-custom-alerts.md) per migliorare le condizioni di sicurezza delle soluzioni IoT. Se si ha già un agente di dispositivo, prendere in considerazione [distribuzione di un agente protezione](how-to-deploy-agent.md) oppure [la modifica della configurazione di un agente di dispositivo esistente](how-to-agent-configuration.md) per migliorare i risultati. 