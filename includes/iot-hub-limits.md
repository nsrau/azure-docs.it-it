---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224537"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| Gruppi | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Messaggi al giorno |400.000 |6\.000.000 |300.000.000 |8\.000 |
| Unità massime |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| Gruppi | Limite |
| --- | --- |
| Numero massimo hub IoT a pagamento per ogni sottoscrizione di Azure |100 |
| Numero massimo hub IoT gratuiti per ogni sottoscrizione di Azure |1 |
| Maximum number of characters in a device ID | 128 |
| Numero massimo di identità del dispositivo<br/> restituito in una singola chiamata |1\.000 |
| Conservazione massima nell'hub IoT per i messaggi dal dispositivo al cloud |7 giorni |
| Dimensione massima del messaggio del dispositivo al cloud |256 KB |
| Dimensione massima del batch del dispositivo al cloud |AMQP e HTTP: 256 kB per l'intero batch <br/>MQTT: 256 kB per ogni messaggio |
| Messaggi massimi nel batch del dispositivo al cloud |500 |
| Dimensione massima del messaggio del cloud al dispositivo |64 KB |
| TTL massima per i messaggi del cloud al dispositivo |2 giorni |
| Numero massimo di recapiti per i messaggi <br/> del cloud al dispositivo |100 |
| Maximum cloud-to-device queue depth per device |50 |
| Numero massimo di recapiti per i messaggi di feedback <br/> in risposta a un messaggio del cloud al dispositivo |100 |
| TTL massimo per i messaggi di feedback <br/> in risposta a un messaggio del cloud al dispositivo |2 giorni |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Dimensione massima del payload del metodo diretto | 128 KB |
| Periodo di conservazione massimo della cronologia del processo | 30 giorni |
| Numero massimo di processi simultanei | 10 (per S3), 5 per (S2), 1 ( per S1) |
| Numero massimo di endpoint aggiuntivi | 10 (for S1, S2, and S3) |
| Numero massimo di regole di routing dei messaggi | 100 (for S1, S2, and S3) |
| Numero massimo di flussi dispositivo connessi contemporaneamente | 50 (per S1, S2, S3 e solo F1) |
| Trasferimento dati massimo di flussi dispositivo | 300 MB al giorno (per S1, S2, S3 e solo F1) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

IoT Hub throttles requests when the following quotas are exceeded.

| Limitazione | Valore per ogni hub |
| --- | --- |
| Operazioni del registro delle identità <br/> (create, retrieve, list, update, and delete), <br/> importazione/esportazione singola o in blocco |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/sec/unità (100/min/unità) (per S1 e S2). |
| Connessioni del dispositivo |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Minimo di 100/sec. |
| Inoltri dal dispositivo al cloud |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Minimo di 100/sec. |
| Inoltri dal cloud al dispositivo | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Ricezioni dal cloud al dispositivo |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| Operazioni di caricamento file |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Metodi diretti | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| Letture del dispositivo gemello | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| Aggiornamenti dei dispositivi gemelli | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| Operazioni dei processi <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| Velocità effettiva delle operazioni dei processi per dispositivo | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| Velocità di avvio del flusso dispositivo | 5 new streams/sec (for S1, S2, S3, and F1 only). |
