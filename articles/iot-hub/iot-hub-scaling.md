---
title: Ridimensionamento dell'hub IoT di Azure | Microsoft Docs
description: Come ridimensionare l'hub IoT per supportare la velocità effettiva dei messaggi prevista e le funzionalità desiderate. Include un riepilogo della velocità effettiva supportata per ogni livello e le opzioni per il partizionamento orizzontale.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: b4c5bf3b11c2ee661d95dc50f5c93e12fe2d56bf
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901042"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Scegliere il livello di hub IoT più adatto per la soluzione

Ogni soluzione IoT è diversa, pertanto l'hub IoT di Azure offre diverse opzioni in base al prezzo e alla scalabilità. Questo articolo ha lo scopo di assistere nella valutazione delle esigenze a livello di hub IoT. Per informazioni sui prezzi dei diversi livelli di hub IoT, vedere [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub). 

Per decidere quale livello di hub IoT è adatto per la propria soluzione, è opportuno porsi due domande:

**Quali funzionalità si prevede di usare?**
L'hub IoT di Azure offre due livelli, Basic e Standard, che si differenziano per il numero di funzionalità supportate. Se la soluzione IoT si basa sulla raccolta di dati dai dispositivi e sulla loro analisi centralizzata, il livello Basic è probabilmente il più adatto. Se si vogliono usare configurazioni più avanzate per controllare in remoto i dispositivi IoT o per distribuire alcuni dei carichi di lavoro nei dispositivi stessi, prendere invece in considerazione il livello Standard. Per un'analisi dettagliata delle funzionalità incluse in ogni livello, vedere la sezione [Livelli Basic e Standard](#basic-and-standard-tiers) più avanti.

**Quanti dati si prevede di spostare ogni giorno?**
Ogni livello di hub IoT è disponibile in tre dimensioni, in base alla velocità effettiva dei dati che possono gestire in un determinato giorno. Queste dimensioni sono identificate numericamente come 1, 2 e 3. Ad esempio, ogni unità di un hub IoT di livello 1 può gestire 400.000 messaggi al giorno, mentre un'unità di livello 3 ne può gestire 300 milioni. Per altre informazioni sulle linee guida sui dati, vedere la sezione [Velocità effettiva dei messaggi](#message-throughput) più avanti.

## <a name="basic-and-standard-tiers"></a>Livelli Basic e Standard

Il livello Standard dell'hub IoT supporta tutte le funzionalità ed è necessario per qualsiasi soluzione IoT in cui si vogliano usare le funzionalità di comunicazione bidirezionale. Il livello Basic supporta un subset delle funzionalità ed è destinato alle soluzioni IoT per cui è sufficiente la comunicazione unidirezionale dai dispositivi al cloud. Entrambi i livelli offrono le stesse funzionalità di sicurezza e autenticazione.

Dopo aver creato l'hub IoT, è possibile eseguire l'aggiornamento dal livello Basic al livello Standard senza interrompere le operazioni esistenti. Per altre informazioni, vedere [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT). Si noti che il limite di partizioni per il livello di base dell'hub IoT è 8. Questo limite rimarrà invariato con la migrazione dal livello di base al livello standard.

| Funzionalità | Livello Basic | Livello Standard |
| ---------- | ---------- | ------------- |
| [Telemetria da dispositivo a cloud](iot-hub-devguide-messaging.md) | Sì | Sì |
| [Identità per dispositivo](iot-hub-devguide-identity-registry.md) | Sì | Sì |
| [Routing di messaggi](iot-hub-devguide-messages-read-custom.md) e [integrazione con Griglia di eventi](iot-hub-event-grid.md) | Sì | Sì |
| [Protocolli HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sì | Sì |
| [Servizio Device Provisioning](../iot-dps/about-iot-dps.md) | Sì | Sì |
| [Monitoraggio e diagnostica](iot-hub-monitor-resource-health.md) | Sì | Sì |
| [Messaggistica da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sì |
| [Dispositivi gemelli](iot-hub-devguide-device-twins.md), [Module gemelli](iot-hub-devguide-module-twins.md) e [Gestione dei dispositivi](iot-hub-device-management-overview.md) |   | Sì |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Sì |

L'hub IoT offre inoltre un livello gratuito adatto per il testing e la valutazione. Include tutte le funzionalità del livello standard, ma offre un supporto limitato delle funzionalità di messaggistica. Non è possibile eseguire l'aggiornamento dal livello gratuito al livello Basic o Standard. 

### <a name="iot-hub-rest-apis"></a>API REST dell'hub IoT

La differenza a livello di funzionalità supportate tra i livelli Basic e Standard dell'hub IoT è che alcune chiamate API non funzionano con gli hub di livello Basic. La tabella seguente illustra le API disponibili: 

| API | Livello Basic | Livello Standard |
| --- | ---------- | ------------- |
| [Eliminazione dispositivo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/deletedevice) | Sì | Sì |
| [Recupero dispositivo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdevice) | Sì | Sì |
| Eliminazione modulo | Sì | Sì |
| Recupero modulo | Sì | Sì |
| [Recupero statistiche del Registro di sistema](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdeviceregistrystatistics) | Sì | Sì |
| [Recupero statistiche dei servizi](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getservicestatistics) | Sì | Sì |
| [Inserimento dispositivo](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Sì | Sì |
| Inserimento modulo | Sì | Sì |
| [Query su dispositivi](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Sì | Sì |
| Query su moduli | Sì | Sì |
| [Creazione URI di firma di accesso condiviso per il caricamento di file](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/createfileuploadsasuri) | Sì | Sì |
| [Ricezione notifica di dispositivo associato](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/receivedeviceboundnotification) | Sì | Sì |
| [Invio evento dispositivo](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/senddeviceevent) | Sì | Sì |
| Invio evento modulo | Sì | Sì |
| [Aggiornamento stato di caricamento file](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/updatefileuploadstatus) | Sì | Sì |
| [Operazioni in blocco su dispositivi](https://docs.microsoft.com/en-us/rest/api/iot-dps/deviceenrollment/bulkoperation) | Sì, ad eccezione delle funzionalità di IoT Edge | Sì | 
| [Eliminazione coda di comandi](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/purgecommandqueue) |   | Sì |
| [Recupero dispositivo gemello](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/gettwin) |   | Sì |
| Recupero modulo gemello |   | Sì |
| [Richiamo metodo dispositivo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/invokedevicemethod) |   | Sì |
| [Aggiornamento dispositivo gemello](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/updatetwin) |   | Sì | 
| Aggiornamento modulo gemello |   | Sì | 
| [Abbandono notifica di dispositivo associato](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/abandondeviceboundnotification) |   | Sì |
| [Completamento notifica di dispositivo associato](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/completedeviceboundnotification) |   | Sì |
| [Annullamento processo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/canceljob) |   | Sì |
| [Creazione processo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/createjob) |   | Sì |
| [Recupero processo](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getjob) |   | Sì |
| [Query sui processi](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/queryjobs) |   | Sì |

## <a name="message-throughput"></a>Velocità effettiva dei messaggi

Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per unità. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Il traffico viene misurato per unità, non per hub. A un'istanza di hub IoT di livello 1 o 2 possono essere associate fino a 200 unità. Un'istanza di hub IoT di livello 3 può avere fino a 10 unità. Dopo aver creato l'hub IoT, è possibile cambiare il numero di unità o spostarsi tra le dimensioni 1, 2 e 3 all'interno di uno specifico livello senza interrompere le operazioni esistenti. Per altre informazioni, vedere [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT).

Come esempio delle capacità di traffico di ogni livello, i messaggi da dispositivo a cloud seguono queste linee guida di velocità effettiva sostenuta:

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| --- | --- | --- |
| B1, S1 |Fino a 1.111 KB al minuto per unità<br/>(1,5 GB al giorno per unità) |Una media di 278 messaggi al minuto per unità<br/>(400.000 messaggi al giorno per unità) |
| B2, S2 |Fino a 16 MB al minuto per unità<br/>(22,8 GB al giorno per unità) |Una media di 4.167 messaggi al minuto per unità<br/>(6 milioni di messaggi al giorno per unità) |
| B3, S3 |Fino a 814 MB al minuto per unità<br/>(1144,4 GB al giorno per unità) |Una media di 208,333 messaggi al minuto per unità<br/>(300 milioni di messaggi al giorno per unità) |

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles] e progettare la propria soluzione di conseguenza.

### <a name="identity-registry-operation-throughput"></a>Velocità effettiva delle operazioni del registro delle identità
Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime perché sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere le [quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles].

## <a name="sharding"></a>Partizionamento orizzontale
Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In tal caso è possibile partizionare i dispositivi su più hub IoT. Più hub IoT appianano i picchi di traffico e ottengono il throughput necessario o i tassi di operazione richiesti.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle funzionalità dell'hub IoT e per dettagli sulle prestazioni, vedere [Prezzi di Hub IoT][link-pricing] o [Quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles].
* Per cambiare il livello dell'hub IoT, seguire la procedura descritta in [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
