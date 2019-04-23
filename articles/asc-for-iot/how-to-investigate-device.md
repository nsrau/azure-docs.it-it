---
title: Il Centro sicurezza di Azure per la Guida alle indagini sui dispositivi IoT Preview | Microsoft Docs
description: Questa Guida alle procedure spiega come usare il Centro sicurezza di Azure per IoT per analizzare un dispositivo IoT sospetto usando Log Analitica.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 6182662cb0da7fa5bcd3f329ada9ca5851490724
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007883"
---
# <a name="investigate-a-suspicious-iot-device"></a>Analizzare un dispositivo IoT sospetto

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il Centro sicurezza di Azure (ASC) per l'evidenza e gli avvisi del servizio IoT offrono indiscutibilmente quando i dispositivi IoT sono sospetta di coinvolgimento in attività sospette o quando le indicazioni presenti che un dispositivo sia compromesso. 

In questa Guida, usare i suggerimenti di indagine forniti per aiutare a determinare potenziali rischi per l'organizzazione, decidere come correggere e scoprire i sistemi migliori per impedire attacchi simili in futuro.  

> [!div class="checklist"]
> * Trovare i dati del dispositivo
> * Analizzare usando le query kql


## <a name="how-can-i-access-my-data"></a>Come è possibile accedere ai dati?

Per impostazione predefinita, il Centro sicurezza di Azure per IoT archivia gli avvisi di sicurezza e le raccomandazioni nell'area di lavoro Log Analytics. È anche possibile scegliere di archiviare i dati di sicurezza non elaborati.

Per trovare l'area di lavoro Log Analytics per l'archiviazione dei dati:

1. Aprire l'hub IoT. 
1. Sotto **sicurezza**, fare clic su **Panoramica**, quindi selezionare **impostazioni**.
1. Modificare i dettagli di configurazione dell'area di lavoro Log Analytics. 
1. Fare clic su **Save**. 

In seguito alla configurazione, eseguire le operazioni seguenti per accedere ai dati archiviati nell'area di lavoro Log Analytics:

1. Selezionare e fare clic su un avviso del Centro sicurezza di Azure per IoT nell'hub IoT. 
1. Fare clic su **Further investigation** (Ulteriori indagini). 
1. Selezionare **To see which devices have this alert click here and view the DeviceId column** (Per vedere quali dispositivi hanno questo avviso fare clic qui e visualizzare la colonna DeviceId).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Procedure di indagine per dispositivi IoT sospetti

Per accedere a informazioni dettagliate e dati non elaborati sui dispositivi IoT, visitare l'area di lavoro di Log Analitica [per accedere ai dati](#how-can-i-access-my-data).

Controllare e analizzare i dati del dispositivo per i dettagli seguenti e le attività usando le query kql seguenti.

### <a name="related-alerts"></a>Avvisi correlati

Per sapere se gli altri avvisi sono stati attivati intorno alla stessa ora, usare la query kql seguente:

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
Usare questi dati per scoprire: 
  1. Quali utenti hanno accesso al dispositivo
  2. Sono gli utenti con accesso con i livelli di autorizzazione come previsto? 

### <a name="open-ports"></a>Aprire le porte

Per scoprire quali porte nel dispositivo sono attualmente in uso o sono state usate, utilizzare la seguente query kql: 

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
  1. Quali socket di ascolto sono attualmente attivi sul dispositivo
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
  1. Quali utenti hanno eseguito l'accesso al dispositivo
  2. Sono gli utenti che è connesso, dovrà accedere?
  3. Se gli utenti che hanno eseguito l'accesso si connettono da indirizzi IP previsti o imprevisti
  
### <a name="process-list"></a>Elenco dei processi

Per scoprire se l'elenco dei processi è come previsto, usare la query kql seguente: 

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

  1. Se sono stati eseguiti processi sospetti sul dispositivo
  2. Se i processi sono stati eseguiti dagli utenti appropriati
  3. Se le esecuzioni della riga di comando contenevano gli argomenti corretti e previsti

## <a name="next-steps"></a>Passaggi successivi

Dopo aver analizzato un dispositivo e ottenuto una migliore comprensione dei rischi, si potrebbe voler considerare la [configurazione di avvisi personalizzati](quickstart-create-custom-alerts.md) per migliorare le condizioni di sicurezza della soluzione IoT. Se non si ha già un agente di dispositivo, per migliorare i risultati prendere in considerazione la [distribuzione di un agente protezione](how-to-deploy-agent.md) oppure [la modifica della configurazione di un agente di dispositivo esistente](how-to-agent-configuration.md). 