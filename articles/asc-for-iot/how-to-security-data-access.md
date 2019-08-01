---
title: Accesso ai dati tramite il Centro sicurezza di Azure Microsoft Docs
description: Informazioni su come accedere ai dati di avviso e raccomandazione di sicurezza quando si usa il Centro sicurezza di Azure.
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
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597173"
---
# <a name="access-your-security-data"></a>Accedere ai dati di sicurezza 

Il Centro sicurezza di Azure per archiviare gli avvisi di sicurezza, le raccomandazioni e i dati di sicurezza non elaborati (se si sceglie di salvarli) nell'area di lavoro Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Per configurare quale area di lavoro Log Analytics viene utilizzata:

1. Aprire l'hub IoT.
1. Fare clic sul pannello **Panoramica** nella sezione **sicurezza**
2. Fare clic su **Impostazioni**e modificare la configurazione dell'area di lavoro log Analytics.

Per accedere agli avvisi e alle raccomandazioni nell'area di lavoro Log Analytics dopo la configurazione:

1. Scegliere un avviso o una raccomandazione nel centro sicurezza di Azure. 
2. Fare clic su **ulteriori indagini**, quindi fare clic **per visualizzare i dispositivi con questo avviso fare clic qui e visualizzare la colonna DeviceID**.

Per informazioni dettagliate sull'esecuzione di query sui dati di Log Analytics, vedere [Introduzione alle query in log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Avvisi sicurezza

Gli avvisi di sicurezza vengono archiviati nella tabella _AzureSecurityOfThings. SecurityAlert_ nell'area di lavoro log Analytics configurata per il Centro sicurezza di Azure per la soluzione Internet delle cose.

Sono state fornite numerose query utili che consentono di iniziare a esplorare gli avvisi di sicurezza.

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Descrizione                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | Attacco di forza bruta riuscito           | Un attacco di forza bruta sul dispositivo è riuscito        |    {"Indirizzo di origine completo": "\"[10.165.12.18\":]", "nomi utente": "\"[\"]", "DeviceID": "Cose-dispositivo-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | Accesso locale riuscito nel dispositivo      | È stato rilevato un accesso locale riuscito al dispositivo     | {"Indirizzo remoto": "?", "porta remota": "", "porta locale": "", "shell di accesso": "/bin/su", "ID processo di accesso": "28207", "nome utente": "pirata informatico", "DeviceId": "Cose-dispositivo-Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | Tentativo di accesso locale non riuscito nel dispositivo  | È stato rilevato un tentativo di accesso locale non riuscito al dispositivo |  {"Indirizzo remoto": "?", "porta remota": "", "porta locale": "", "shell di accesso": "/bin/su", "ID processo di accesso": "22644", "nome utente": "pirata informatico", "DeviceId": "Cose-dispositivo-Linux"} |

### <a name="device-summary"></a>Riepilogo dispositivo

Ottenere il numero di avvisi di sicurezza distinti rilevati nell'ultima settimana, raggruppati per hub di gestione delle cose, dispositivo, gravità dell'avviso, tipo di avviso.

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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | Attacco di forza bruta riuscito           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi        | Tentativo di accesso locale non riuscito nel dispositivo  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | Accesso locale riuscito nel dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>Riepilogo dell'hub Internet

Selezionare un numero di dispositivi distinti che hanno ricevuto avvisi nell'ultima settimana, per hub tutto, gravità avviso, tipo di avviso

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

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | High          | Attacco di forza bruta riuscito           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medi        | Tentativo di accesso locale non riuscito nel dispositivo  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | High          | Accesso locale riuscito nel dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medi        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Raccomandazioni sulla sicurezza

Le raccomandazioni sulla sicurezza vengono archiviate nella tabella _AzureSecurityOfThings. SecurityRecommendation_ nell'area di lavoro log Analytics configurata per il Centro sicurezza di Azure per la soluzione Internet delle cose.

Sono state fornite numerose query utili che consentono di iniziare a esplorare le raccomandazioni sulla sicurezza.

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
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | DESCRIZIONE | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi | Attivo | È stata trovata una regola permissiva del firewall nella catena di input | È stata rilevata una regola del firewall che contiene un criterio permissivo per un intervallo ampio di indirizzi IP o porte | {"Rules": "[\"{\"sourceAddress\":\"\",\"SourcePort\":\",DestinationAddress\":\" \"\" ,\"DestinationPort\":\"1337}]"}\" |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi | Attivo | È stata trovata una regola permissiva del firewall nella catena di input | È stata rilevata una regola del firewall che contiene un criterio permissivo per un intervallo ampio di indirizzi IP o porte | {"Rules": "[\"{\"sourceAddress\":\"\",\"SourcePort\":\",DestinationAddress\":\" \"\" ,\"DestinationPort\":\"1337}]"}\" |

### <a name="device-summary"></a>Riepilogo dispositivo

Ottenere il numero di consigli distinti per la sicurezza attiva, raggruppati per hub degli elementi, dispositivo, gravità della raccomandazione e tipo.

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

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | High          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < device_name > | Medi        | 4   |


## <a name="next-steps"></a>Passaggi successivi

- Leggi il Centro sicurezza di Azure per [informazioni generali](overview.md)
- Informazioni sul centro sicurezza di Azure per l' [architettura dell'it](architecture.md)
- Comprendere ed esplorare il [Centro sicurezza di Azure per gli avvisi relativi alle](concept-security-alerts.md) cose
- Comprendere ed esplorare il [Centro sicurezza di Azure per le raccomandazioni](concept-recommendations.md)
