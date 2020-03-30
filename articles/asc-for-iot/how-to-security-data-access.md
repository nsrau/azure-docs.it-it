---
title: Accesso ai dati tramite il Centro sicurezza di Azure per ioT Documenti Microsoft
description: Informazioni su come accedere ai dati di avviso e consigli di sicurezza quando si usa il Centro sicurezza di Azure per l'IoT.Learn about how to access your security alert and recommendation data when using Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597173"
---
# <a name="access-your-security-data"></a>Accedi ai tuoi dati di sicurezza 

Il Centro sicurezza di Azure per IoT archivia avvisi di sicurezza, consigli e dati di sicurezza non elaborati (se si sceglie di salvarli) nell'area di lavoro di Log Analytics.Azure Security Center for IoT stores security alerts, recommendations, and raw security data (if you choose to save it) in your Log Analytics workspace.

## <a name="log-analytics"></a>Log Analytics

Per configurare l'area di lavoro di Log Analytics utilizzata:

1. Aprire l'hub IoT.
1. Fare clic sul pannello **Panoramica** nella sezione **Sicurezza**
2. Fare clic su **Impostazioni**e modificare la configurazione dell'area di lavoro di Log Analytics.

Per accedere agli avvisi e ai suggerimenti nell'area di lavoro di Log Analytics dopo la configurazione:

1. Scegliere un avviso o una raccomandazione nel Centro sicurezza di Azure per ioT.Choose an alert or recommendation in Azure Security Center for IoT. 
2. Fare clic su **ulteriore indagine**, quindi fare clic su **Per visualizzare i dispositivi con questo avviso, fare clic qui e visualizzare la colonna DeviceId**.

Per informazioni dettagliate sull'esecuzione di query sui dati da Log Analytics, vedere [Introduzione alle query in Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Avvisi di sicurezza

Gli avvisi di sicurezza vengono archiviati nella tabella AzureSecurityOfThings.SecurityAlert nell'area di lavoro Log Analytics configurata per il Centro sicurezza di Azure per la soluzione IoT.Security alerts are stored in _AzureSecurityOfThings.SecurityAlert_ table in the Log Analytics workspace configured for the Azure Security Center for IoT solution.

Abbiamo fornito una serie di query utili per aiutarti a iniziare a esplorare gli avvisi di sicurezza.

### <a name="sample-records"></a>Record di esempio

Selezionare alcuni record casuali

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IDI/OTHub                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Descrizione                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | Attacco di forza bruta riuscito           | Un attacco di forza bruta sul dispositivo è stato riuscito        |    " "Indirizzo di origine\"completo": "[ 10.165.12.18:\"]", "Nomi utente": "[\"\"]", "DeviceId": "IoT-Device-Linux"                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | Accesso locale riuscito sul dispositivo      | È stato rilevato un accesso locale riuscito al dispositivo     | " "Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Login Shell": "/bin/su", "Id processo di accesso": "28207", "Nome utente": "attaccante", "IdDispositivo": "IoT-Device-Linux" |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | Tentativo di accesso locale non riuscito nel dispositivo  | È stato rilevato un tentativo di accesso locale non riuscito al dispositivo |  " "Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Login Shell": "/bin/su", "Id processo di accesso": "22644", "Nome utente": "attaccante", "IdDispositivo": "IoT-Device-Linux" |

### <a name="device-summary"></a>Riepilogo del dispositivo

Ottenere il numero di avvisi di sicurezza distinti rilevati nell'ultima settimana, raggruppati per hub IoT, dispositivo, gravità degli avvisi, tipo di avviso.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IDI/OTHub                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | Attacco di forza bruta riuscito           | 9   |   
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media        | Tentativo di accesso locale non riuscito nel dispositivo  | 242 |    
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | Accesso locale riuscito sul dispositivo      | 31  |
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>Riepilogo dell'hub IoT

Selezionare un numero di dispositivi distinti con avvisi nell'ultima settimana, per hub IoT, gravità degli avvisi, tipo di avviso

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IDI/OTHub                                                                                                       | AlertSeverity | DisplayName                           | Dispositivi Cnt |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Attacco di forza bruta riuscito           | 1          |    
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Tentativo di accesso locale non riuscito nel dispositivo  | 1          | 
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Accesso locale riuscito sul dispositivo      | 1          |
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Suggerimenti per la sicurezza

I consigli sulla sicurezza vengono archiviati nella tabella AzureSecurityOfThings.SecurityRecommendation nell'area di lavoro Log Analytics configurata per il Centro sicurezza di Azure per la soluzione IoT.Security recommendations are stored in _AzureSecurityOfThings.SecurityRecommendation_ table in the Log Analytics workspace configured for the Azure Security Center for IoT solution.

Abbiamo fornito una serie di query utili per aiutarti a iniziare a esplorare i consigli sulla sicurezza.

### <a name="sample-records"></a>Record di esempio

Selezionare alcuni record casuali

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IDI/OTHub | deviceId | RecommendationGraviy | RaccomandazioneStato | RaccomandazioneNomeVisualizzato | Descrizione | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media | Attivo | È stata trovata una regola firewall permissiva nella catena di input | È stata trovata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di indirizzi IP o porte | "Regole":" :\":\"\"\"\"\"PortaOrigine\"\": ,\"IndirizzoDestinazione\"\"\"\":\",\"PortaDestinazione\": 1337 |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media | Attivo | È stata trovata una regola firewall permissiva nella catena di input | È stata trovata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di indirizzi IP o porte | "Regole":" :\":\"\"\"\"\"PortaOrigine\"\": ,\"IndirizzoDestinazione\"\"\"\":\",\"PortaDestinazione\": 1337 |

### <a name="device-summary"></a>Riepilogo del dispositivo

Ottieni il numero di consigli di sicurezza attivi distinti, raggruppati per hub IoT, dispositivo, gravità dei consigli e tipo.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IDI/OTHub                                                                                                       | deviceId      | RecommendationGraviy | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | 2   |    
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media        | 1 |  
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Alto          | 1  |
| /subscriptions/<subscription_id>/gruppidirisorse/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <> device_name | Media        | 4   |


## <a name="next-steps"></a>Passaggi successivi

- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per IoT
- Informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn about Azure Security Center for IoT [Architecture](architecture.md)
- Comprendere ed [esplorare il Centro sicurezza di Azure per gli avvisi IoTUnderstand](concept-security-alerts.md) and explore Azure Security Center for IoT alerts
- Comprendere ed esplorare la raccomandazione del [Centro sicurezza di Azure per ioTUnderstand](concept-recommendations.md) and explore Azure Security Center for IoT recommendation
