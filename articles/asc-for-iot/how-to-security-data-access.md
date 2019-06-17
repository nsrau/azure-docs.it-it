---
title: L'accesso ai dati usando il Centro sicurezza di Azure per l'anteprima di IoT | Microsoft Docs
description: Informazioni su come accedere ai dati di avviso e indicazioni di sicurezza quando si usa il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200553"
---
# <a name="access-your-security-data"></a>Accedere ai dati di sicurezza 

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il Centro sicurezza di Azure (ASC) per IoT vengono archiviati gli avvisi di sicurezza, raccomandazioni e i dati di sicurezza non elaborato (se si sceglie di salvare il file) nell'area di lavoro di Log Analitica.

## <a name="log-analytics"></a>Log Analytics

Per configurare l'area di lavoro di Log Analitica viene usato:

1. Aprire l'hub IoT.
1. Fare clic su **sicurezza**
2. Fare clic su **impostazioni**, modificare la configurazione dell'area di lavoro di Log Analitica.

Per accedere all'area di lavoro di Log Analitica dopo la configurazione:

1. Scegliere un avviso o raccomandazione nel Centro sicurezza di AZURE per IoT. 
2. Fare clic su **ulteriori indagini condotte**, quindi fare clic su **per verificare quali dispositivi sono associati a questo avviso fare clic qui e visualizzare la colonna DeviceId**.

Per informazioni dettagliate sulle query sui dati di Log Analitica, vedere [Introduzione alle query in Log Analitica](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Avvisi di sicurezza

Avvisi di sicurezza vengono archiviati nel _AzureSecurityOfThings.SecurityAlert_ tabella nell'area di lavoro di Log Analitica configurato per il Centro sicurezza di AZURE per la soluzione IoT.

È stato fornito un numero di query utili che consentono di iniziare a esplorare gli avvisi di sicurezza.

### <a name="sample-records"></a>Record di esempio

Selezionare alcuni record casuale

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

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Descrizione                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Attacco di forza bruta ha avuto esito positivo           | Un attacco di forza bruta sul dispositivo è stato completato        |    {"Indirizzo di origine completo": "[\"10.165.12.18:\"]", "Nomi utente": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | È stato rilevato un accesso locale ha esito positivo al dispositivo     | {"Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Account di accesso Shell": "/ bin/unità di streaming", "Id di processo di accesso": "28207", "User Name": "autore dell'attacco", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Tentativo di accesso locale sul dispositivo non è riuscito  | È stato rilevato un tentativo di accesso locale nel dispositivo |  {"Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Account di accesso Shell": "/ bin/unità di streaming", "Id di processo di accesso": "22644", "User Name": "autore dell'attacco", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Riepilogo del dispositivo

Selezionare il numero di avvisi di sicurezza distinti rilevato ultima settimana per l'IoT Hub, dispositivo, alla gravità dell'avviso e tipo di avviso.

```
// Select number of distinct security alerts detected last week by 
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

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Attacco di forza bruta ha avuto esito positivo           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | Tentativo di accesso locale sul dispositivo non è riuscito  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | Operazioni di crittografia moneta Miner                     | 4   |

### <a name="iot-hub-summary"></a>Riepilogo dell'hub IoT

Selezionare un numero di dispositivi distinti che sono stati rilevati avvisi nell'ultima settimana, dall'IoT Hub, alla gravità dell'avviso, tipo di avviso

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Attacco di forza bruta ha avuto esito positivo           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Tentativo di accesso locale sul dispositivo non è riuscito  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Operazioni di crittografia moneta Miner                     | 1          |

## <a name="security-recommendations"></a>Suggerimenti per la sicurezza

Raccomandazioni sulla sicurezza vengono archiviati nel _AzureSecurityOfThings.SecurityRecommendation_ tabella nell'area di lavoro di Log Analitica configurato per il Centro sicurezza di AZURE per la soluzione IoT.

È stato fornito un numero di query utili che consentono di iniziare a esplorare le raccomandazioni sulla sicurezza.

### <a name="sample-records"></a>Record di esempio

Selezionare alcuni record casuale

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
    
| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Descrizione | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media | Attivo | Regola di firewall permissivi nella catena di input è stata trovata | Una regola del firewall è stata trovata che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media | Attivo | Regola di firewall permissivi nella catena di input è stata trovata | Una regola del firewall è stata trovata che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Riepilogo del dispositivo

Selezionare il numero di raccomandazioni di sicurezza attivi distinti dall'IoT Hub, dispositivi, la gravità di raccomandazione e tipo.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Conteggio |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | 4   |


## <a name="next-steps"></a>Passaggi successivi

- Leggere il Centro sicurezza di AZURE per IoT [Panoramica](overview.md)
- Informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Comprendere ed esplorare [Centro sicurezza di AZURE per gli avvisi di IoT](concept-security-alerts.md)
- Comprendere ed esplorare [Centro sicurezza di AZURE per IoT raccomandazione](concept-recommendations.md)