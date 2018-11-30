---
title: Ridimensionamento dell'hub IoT di Azure | Microsoft Docs
description: Come ridimensionare l'hub IoT per supportare la velocità effettiva dei messaggi prevista e le funzionalità desiderate. Include un riepilogo della velocità effettiva supportata per ogni livello e le opzioni per il partizionamento orizzontale.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: wesmc
ms.openlocfilehash: 8ff99b8c5cf839afb8c8e73c18c2c413998ba64a
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261719"
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

Per ogni hub IoT è possibile scegliere un solo tipo di [edizione](https://azure.microsoft.com/pricing/details/iot-hub/) all'interno di un livello. Ad esempio, è possibile creare un hub IoT con più unità di S1, ma non con una combinazione di unità appartenenti a edizioni diverse, ad esempio, S1, B3 o S1 e S2.

| Funzionalità | Livello Basic | Livello Standard |
| ---------- | ---------- | ------------- |
| [Telemetria da dispositivo a cloud](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Identità per dispositivo](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [Routing di messaggi](iot-hub-devguide-messages-read-custom.md) e [integrazione con Griglia di eventi](iot-hub-event-grid.md) | Yes | Yes |
| [Protocolli HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Servizio Device Provisioning](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [Monitoraggio e diagnostica](iot-hub-monitor-resource-health.md) | Yes | Yes |
| [Messaggistica da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Dispositivi gemelli](iot-hub-devguide-device-twins.md), [Moduli gemelli](iot-hub-devguide-module-twins.md) e [Gestione dei dispositivi](iot-hub-device-management-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Yes |

L'hub IoT offre inoltre un livello gratuito adatto per il testing e la valutazione. Include tutte le funzionalità del livello standard, ma offre un supporto limitato delle funzionalità di messaggistica. Non è possibile eseguire l'aggiornamento dal livello gratuito al livello Basic o Standard. 


## <a name="partitions"></a>Partitions

Gli hub IoT di Azure contengono molti componenti di base di [Hub eventi di Azure](../event-hubs/event-hubs-features.md), tra cui le [partizioni](../event-hubs/event-hubs-features.md#partitions). I flussi di eventi per gli hub IoT vengono in genere popolati con dati di telemetria in ingresso che vengono segnalati da vari dispositivi IoT. Il partizionamento del flusso di eventi consente di ridurre le contese che si verificano durante la lettura e la scrittura simultanee nei flussi di eventi. 

Il limite di partizioni viene scelto al momento della creazione dell'hub IoT Hub e non può essere modificato. Il limite massimo di partizioni per hub IoT di livello Basic è 8 e per hub IoT di livello Standard è 32. La maggior parte degli hub IoT richiede solo 4 partizioni. Per altre informazioni sulla determinazione delle partizioni, vedere la domanda frequente su Hub eventi [Quante partizioni sono necessarie?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)


## <a name="tier-upgrade"></a>Aggiornamento del livello

Dopo aver creato l'hub IoT, è possibile eseguire l'aggiornamento dal livello Basic al livello Standard senza interrompere le attività in corso. Per altre informazioni, vedere [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT).

Con la migrazione dal livello di base al livello standard la configurazione della partizione rimane invariata.


## <a name="iot-hub-rest-apis"></a>API REST dell'hub IoT

La differenza a livello di funzionalità supportate tra i livelli Basic e Standard dell'hub IoT è che alcune chiamate API non funzionano con gli hub di livello Basic. La tabella seguente illustra le API disponibili: 

| API | Livello Basic | Livello Standard |
| --- | ---------- | ------------- |
| [Eliminazione dispositivo](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Yes | Yes |
| [Recupero dispositivo](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Yes | Yes |
| Eliminazione modulo | Yes | Yes |
| Recupero modulo | Yes | Yes |
| [Recupero statistiche del Registro di sistema](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Yes | Yes |
| [Recupero statistiche dei servizi](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Yes | Yes |
| [Creazione o aggiornamento dispositivo](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Yes | Yes |
| Inserimento modulo | Yes | Yes |
| [Query su hub IoT](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Yes | Yes |
| Query su moduli | Yes | Yes |
| [Creazione URI di firma di accesso condiviso per il caricamento di file](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Yes | Yes |
| [Ricezione notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Yes | Yes |
| [Invio evento dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Yes | Yes |
| Invio evento modulo | Yes | Yes |
| [Aggiornamento stato di caricamento file](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Yes | Yes |
| [Operazioni in blocco su dispositivi](/rest/api/iot-dps/runbulkenrollmentoperation/runbulkenrollmentoperation) | Sì, ad eccezione delle funzionalità di IoT Edge | Yes | 
| [Eliminazione coda di comandi](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Yes |
| [Recupero dispositivo gemello](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Yes |
| Recupero modulo gemello |   | Yes |
| [Richiamo metodo dispositivo](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Yes |
| [Aggiornamento dispositivo gemello](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Yes | 
| Aggiornamento modulo gemello |   | Yes | 
| [Abbandono notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Yes |
| [Completamento notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Yes |
| [Annullamento processo](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Yes |
| [Creazione processo](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Yes |
| [Recupero processo](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Yes |
| [Query sui processi](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Yes |

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

## <a name="auto-scale"></a>Scalabilità automatica
Se si sta per raggiungere il limite consentito per un messaggio nell'IoT Hub, è possibile usare questi [passaggi per la scalabilità automatica](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) al fine di incrementare un'unità hub IoT allo stesso livello di hub IoT.

## <a name="sharding"></a>Partizionamento orizzontale
Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In tal caso è possibile partizionare i dispositivi su più hub IoT. Più hub IoT appianano i picchi di traffico e ottengono il throughput necessario o i tassi di operazione richiesti.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle funzionalità dell'hub IoT e per dettagli sulle prestazioni, vedere [Prezzi di Hub IoT][lnk-pricing] o [Quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles].
* Per cambiare il livello dell'hub IoT, seguire la procedura descritta in [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
