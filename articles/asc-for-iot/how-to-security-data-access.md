---
title: Accesso ai dati tramite Centro sicurezza di AZURE per l'anteprima di IoT | Microsoft Docs
description: Informazioni su come accedere ai dati di avviso e indicazioni di sicurezza quando si usa Centro sicurezza di AZURE per IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541993"
---
# <a name="access-your-security-data"></a>Accedere ai dati di sicurezza 

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Centro sicurezza di AZURE per IoT vengono archiviati gli avvisi di sicurezza, raccomandazioni e i dati di sicurezza non elaborato (se si sceglie di salvare il file) nell'area di lavoro di Log Analitica.

## <a name="log-analytics"></a>Log Analytics

Per configurare l'area di lavoro di Log Analitica viene usato:

1. Aprire l'hub IoT.
1. Fare clic su **sicurezza**
2. Fare clic su **impostazioni**, modificare la configurazione dell'area di lavoro di Log Analitica.

Per accedere all'area di lavoro di Log Analitica dopo la configurazione:

1. Scegliere un avviso nel Centro sicurezza di AZURE per IoT. 
2. Fare clic su **ulteriori indagini condotte**, quindi fare clic su **per verificare quali dispositivi sono associati a questo avviso fare clic qui e visualizzare la colonna DeviceId**.

Per informazioni dettagliate sulle query sui dati di Log Analitica, vedere [Introduzione alle query in Log Analitica](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Avvisi di sicurezza

Avvisi di sicurezza vengono archiviati nel **ASCforIoT.SecurityAlert** tabella nell'area di lavoro di Log Analitica configurata.

Utilizzare le seguenti query di base kql per iniziare a esplorare gli avvisi di sicurezza.

### <a name="sample-records-query"></a>Esempio di query di record

Per selezionare in modo casuale alcuni record: 

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

#### <a name="sample-query-results"></a>Risultati della query di esempio 

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | DESCRIZIONE                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Attacco di forza bruta ha avuto esito positivo           | Un attacco di forza bruta sul dispositivo è stato completato        |    {"Indirizzo di origine completo": "[\"10.165.12.18:\"]", "Nomi utente": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | È stato rilevato un accesso locale ha esito positivo al dispositivo     | {"Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Account di accesso Shell": "/ bin/unità di streaming", "Id di processo di accesso": "28207", "User Name": "autore dell'attacco", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Tentativo di accesso locale sul dispositivo non è riuscito  | È stato rilevato un tentativo di accesso locale nel dispositivo |  {"Indirizzo remoto": "?", "Porta remota": "", "Porta locale": "", "Account di accesso Shell": "/ bin/unità di streaming", "Id di processo di accesso": "22644", "User Name": "autore dell'attacco", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Query di riepilogo dispositivo

Utilizzare questa query kql per selezionare un numero di avvisi di sicurezza distinti rilevato ultima settimana per l'IoT Hub, dispositivo, alla gravità dell'avviso e tipo di avviso.

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

#### <a name="device-summary-query-results"></a>Risultati di query di riepilogo dispositivo

| IoTHubId | deviceId| AlertSeverity| DisplayName | Conteggio |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Attacco di forza bruta ha avuto esito positivo           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | Tentativo di accesso locale sul dispositivo non è riuscito  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | < nome_periferica > | Media        | Operazioni di crittografia moneta Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Riepilogo dell'IoT Hub

Usare questa query kql per selezionare un numero di dispositivi distinti che sono stati rilevati avvisi nell'ultima settimana, dall'hub IoT, alla gravità dell'avviso, tipo di avviso:

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

#### <a name="iot-hub-summary-query-results"></a>Risultati di riepilogo della query dell'IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Attacco di forza bruta ha avuto esito positivo           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Tentativo di accesso locale sul dispositivo non è riuscito  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Alto          | Eseguito correttamente l'accesso locale sul dispositivo      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Media        | Operazioni di crittografia moneta Miner                     | 1          |



## <a name="next-steps"></a>Passaggi successivi

- Leggere il Centro sicurezza di AZURE per IoT [Panoramica](overview.md)
- Informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Comprendere ed esplorare [Centro sicurezza di AZURE per gli avvisi di IoT](concept-security-alerts.md)
