---
title: Informazioni sulle quote e sulle limitazioni dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: descrizione delle quote che si applicano all'hub IoT e del comportamento di limitazione previsto."
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: daeb09acd11d727b11ad8a7b98d97ff90fddc6d8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228253"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Riferimento - Quote e limitazioni dell'hub IoT

This article explains the quotas for an IoT Hub, and provides information to help you understand how throttling works.

## <a name="quotas-and-throttling"></a>Quote e limitazione

Ogni sottoscrizione di Azure può avere al massimo 50 hub IoT e al massimo un hub gratuito.

Il provisioning di ogni hub IoT viene eseguito con un determinato numero di unità in un livello specifico. Il livello e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare. La dimensione dei messaggi usata per calcolare che la quota giornaliera è 0,5 kB per un hub di livello gratuito e 4 kB per tutti gli altri livelli. Per altre informazioni, vedere [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

Il livello determina anche le limitazioni che l'hub IoT applica alle operazioni.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

During public preview, IoT Plug and Play devices will send separate messages per interface, which may increase the number of messages counted towards your message quota.

## <a name="operation-throttles"></a>Limitazioni per le operazioni

Operation throttles are rate limitations that are applied in minute ranges and are intended to prevent abuse. They're also subject to [traffic shaping](#traffic-shaping).

La tabella seguente mostra le limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Nessuna, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Identity registry operations](#identity-registry-operations-throttle) (create, retrieve, list, update, delete) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unit (5,000/min/unit) |
| [New device connections](#device-connections-throttle) (this limit applies to the rate of _new connections_, not the total number of connections) | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24 nuove connessioni/sec, ma si hanno almeno 100 nuove conessioni/sec tra le unità. Con nove unità S1 si otterranno 108 nuove connessioni/sec (9\*12) tra le unità. | 120 nuove connessioni/sec/unità | 6,000 new connections/sec/unit |
| Inoltri dal dispositivo al cloud | Higher of 100 send operations/sec or 12 send operations/sec/unit <br/> For example, two S1 units are 2\*12 = 24/sec, but you have at least 100 send operations/sec across your units. With nine S1 units, you have 108 send operations/sec (9\*12) across your units. | 120 send operations/sec/unit | 6,000 send operations/sec/unit |
| Inoltri dal cloud al dispositivo<sup>1</sup> | 1.67 send operations/sec/unit (100 messages/min/unit) | 1.67 send operations/sec/unit (100 send operations/min/unit) | 83.33 send operations/sec/unit (5,000 send operations/min/unit) |
| Ricezioni dal cloud al dispositivo<sup>1</sup> <br/> (solo quando il dispositivo usa HTTPS)| 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 833.33 receive operations/sec/unit (50,000 receive operations/min/unit) |
| Caricamento di file | 1.67 file upload initiations/sec/unit (100/min/unit) | 1.67 file upload initiations/sec/unit (100/min/unit) | 83.33 file upload initiations/sec/unit (5,000/min/unit) |
| Metodi diretti<sup>1</sup> | 160 KB/sec/unità<sup>2</sup> | 480 KB/sec/unità<sup>2</sup> | 24 MB/sec/unità<sup>2</sup> | 
| Query | 20/min/unit | 20/min/unit | 1,000/min/unit |
| Letture di dispositivi e moduli gemelli <sup>1</sup> | 100/sec | Higher of 100/sec or 10/sec/unit | 500/sec/unit |
| Aggiornamenti di dispositivi e moduli gemelli <sup>1</sup> | 50/sec | Higher of 50/sec or 5/sec/unit | 250/sec/unit |
| Operazioni dei processi<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unit (5,000/min/unit) |
| Operazioni dei dispositivi dei processi<sup>1</sup> <br/> (aggiornamento del dispositivo gemello, chiamata del metodo diretto) | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |
| Configurazioni e distribuzioni Edge<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) |
| Device stream initiation rate<sup>1</sup> | 5 nuovi flussi/sec | 5 nuovi flussi/sec | 5 nuovi flussi/sec |
| Maximum number of concurrently connected device streams<sup>1</sup> | 50 | 50 | 50 |
| Maximum device stream data transfer<sup>1</sup> (aggregate volume per day) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto). <br/><sup>2</sup>Throttling meter size is 4 KB.

### <a name="throttling-details"></a>Throttling Details

* The meter size determines at what increments your throttling limit is consumed. If your direct call's payload is between 0 and 4 KB, it is counted as 4 KB. You can make up to 40 calls per second per unit before hitting the limit of 160 KB/sec/unit.

   Similarly, if your payload is between 4 KB and 8 KB, each call accounts for 8 KB and you can make up to 20 calls per second per unit before hitting the max limit.

   Finally, if your payload size is between 156KB and 160 KB, you'll be able to make only 1 call per second per unit in your hub before hitting the limit of 160 KB/sec/unit.

*  For *Jobs device operations (update twin, invoke direct method)* for tier S2, 50/sec/unit only applies to when you invoke methods using jobs. If you invoke direct methods directly, the original throttling limit of 24 MB/sec/unit (for S2) applies.

*  **Quota** is the aggregate number of messages you can send in your hub *per day*. You can find your hub's quota limit under the column **Total number of messages /day** on the [IoT Hub pricing page](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Your cloud-to-device and device-to-cloud throttles determine the maximum *rate* at which you can send messages -- number of messages irrespective of 4 KB chunks. Each message can be up to 256 KB which is the [maximum message size](iot-hub-devguide-quotas-throttling.md#other-limits).

*  It's a good practice to throttle your calls so that you don't hit/exceed the throttling limits. If you do hit the limit, IoT Hub responds with error code 429 and the client should back-off and retry. These limits are per hub (or in some cases per hub/unit). For more information, refer to [Manage connectivity and reliable messaging/Retry patterns](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic shaping

To accommodate burst traffic, IoT Hub accepts requests above the throttle for a limited time. The first few of these requests are processed immediately. However, if the number of requests continues violate the throttle, IoT Hub starts placing the requests in a queue and processed at the limit rate. This effect is called *traffic shaping*. Furthermore, the size of this queue is limited. If the throttle violation continues, eventually the queue fills up, and IoT Hub starts rejecting requests with `429 ThrottlingException`.

For example, you use a simulated device to send 200 device-to-cloud messages per second to your S1 IoT Hub (which has a limit of 100/sec D2C sends). For the first minute or two, the messages are processed immediately. However, since the device continues to send more messages than the throttle limit, IoT Hub begins to only process 100 messages per second and puts the rest in a queue. You start noticing increased latency. Eventually, you start getting `429 ThrottlingException` as the queue fills up, and the "number of throttle errors" in [IoT Hub's metrics](iot-hub-metrics.md) starts increasing.

### <a name="identity-registry-operations-throttle"></a>Identity registry operations throttle

Device identity registry operations are intended for run-time use in device management and provisioning scenarios. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Device connections throttle

La limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT. La limitazione delle *connessioni del dispositivo* non determina il numero massimo di dispositivi connessi contemporaneamente. La limitazione di frequenza delle *connessioni del dispositivo* dipende dal numero di unità di cui viene effettuato il provisioning per l'hub IoT.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Therefore, to connect 100,000 devices, it takes at least 1,000 seconds (approximately 16 minutes). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità.

## <a name="other-limits"></a>Altri limiti

L'hub IoT applica altri limiti operativi:

| Operazione | Limite |
| --------- | ----- |
| Dispositivi | The total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. The only way to increase this limit is to contact [Microsoft Support](https://azure.microsoft.com/support/options/).|
| Caricamenti di file | 10 concurrent file uploads per device. |
| Processi<sup>1</sup> | Maximum concurrent jobs is 1 (for Free and S1), 5 (for S2), and 10 (for S3). However, the max concurrent [device import/export jobs](iot-hub-bulk-identity-mgmt.md) is 1 for all tiers. <br/>Job history is retained up to 30 days. |
| Altri endpoint | Agli hub SKU a pagamento possono essere associati 10 endpoint aggiuntivi. Agli hub SKU gratuiti può essere associato solo un endpoint aggiuntivo. |
| Message routing queries | Paid SKU hubs may have 100 routing queries. Free SKU hubs may have five routing queries. |
| Arricchimenti dei messaggi | Paid SKU hubs can have up to 10 message enrichments. Free SKU hubs can have up to 2 message enrichments.|
| Messaggistica da dispositivo a cloud | Dimensioni massime dei messaggi 256 KB |
| Messaggistica da cloud a dispositivo<sup>1</sup> | La dimensione massima dei messaggi è 64 KB. Maximum pending messages for delivery is 50 per device. |
| Metodo diretto<sup>1</sup> | La dimensione massima del payload del metodo diretto è 128 KB. |
| Configurazioni automatiche dei dispositivi<sup>1</sup> | 100 configurazioni per hub SKU a pagamento. 20 configurazioni per hub SKU gratuito. |
| IoT Edge automatic deployments<sup>1</sup> | 20 moduli per distribuzione. 100 distribuzioni per hub SKU a pagamento. 10 deployments per free SKU hub. |
| Dispositivi gemelli<sup>1</sup> | Maximum size of desired properties and reported properties sections are 32 KB each. Maximum size of tags section is 8 KB. |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto).

## <a name="increasing-the-quota-or-throttle-limit"></a>Increasing the quota or throttle limit

At any given time, you can increase quotas or throttle limits by [increasing the number of provisioned units in an IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>Latency

L'hub IoT punta a fornire bassa latenza per tutte le operazioni. However, due to network conditions and other unpredictable factors it cannot guarantee a certain latency. Quando si progetta la soluzione, è necessario:

* Evitare di fare ipotesi sulla latenza massima di qualsiasi operazione dell'hub IoT.
* Eseguire il provisioning dell'hub IoT nell'area di Azure più vicina ai dispositivi.
* Considerare l'eventualità di usare Azure IoT Edge per eseguire operazioni sensibili alla latenza sul dispositivo o su un gateway vicino al dispositivo.

Più unità dell'hub IoT influiscono sulla limitazione come descritto in precedenza, ma non forniscono alcuna prestazione di latenza aggiuntiva o garanzia.

In caso di incremento imprevisto della latenza dell'operazione, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post di blog [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Limitazione dell'hub IoT).

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint hub IoT](iot-hub-devguide-endpoints.md)
