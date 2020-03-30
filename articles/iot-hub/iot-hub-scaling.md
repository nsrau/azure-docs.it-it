---
title: Ridimensionamento dell'hub IoT di Azure | Microsoft Docs
description: Come ridimensionare l'hub IoT per supportare la velocità effettiva dei messaggi prevista e le funzionalità desiderate. Include un riepilogo della velocità effettiva supportata per ogni livello e le opzioni per il partizionamento orizzontale.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ecc1ae5138fe5a1e42fed9be2e31b5afa8b6d5b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497513"
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

Per ogni hub IoT è possibile scegliere un solo tipo di [edizione](https://azure.microsoft.com/pricing/details/iot-hub/) all'interno di un livello. Ad esempio, è possibile creare un hub IoT con più unità di S1, ma non con una combinazione di unità di edizioni diverse, ad esempio S1 e S2.

| Funzionalità | Livello Basic | Livello gratuito/Standard |
| ---------- | ---------- | ------------- |
| [Telemetria da dispositivo a cloud](iot-hub-devguide-messaging.md) | Sì | Sì |
| [Identità per dispositivo](iot-hub-devguide-identity-registry.md) | Sì | Sì |
| [Routing dei messaggi,](iot-hub-devguide-messages-read-custom.md)arricchimenti dei messaggi e integrazione di [Griglia di eventiMessage](iot-hub-event-grid.md) routing , [message enrichmentments,](iot-hub-message-enrichments-overview.md)and Event Grid integration | Sì | Sì |
| [Protocolli HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | Sì | Sì |
| [Servizio di provisioning dispositivi](../iot-dps/about-iot-dps.md) | Sì | Sì |
| [Monitoraggio e diagnostica](iot-hub-monitor-resource-health.md) | Sì | Sì |
| [Messaggistica da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) |   | Sì |
| [Dispositivi gemelli](iot-hub-devguide-device-twins.md), [Moduli gemelli](iot-hub-devguide-module-twins.md)e [Gestione dispositivi](iot-hub-device-management-overview.md) |   | Sì |
| [Flussi del dispositivo (anteprima)](iot-hub-device-streams-overview.md) |   | Sì |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Sì |
| [Collegamento IoT e anteprima di riproduzione](../iot-pnp/overview-iot-plug-and-play.md) |   | Sì |

L'hub IoT offre inoltre un livello gratuito adatto per il testing e la valutazione. Include tutte le funzionalità del livello standard, ma offre un supporto limitato delle funzionalità di messaggistica. Non è possibile eseguire l'aggiornamento dal livello gratuito al livello Basic o Standard.

## <a name="partitions"></a>Partizioni

Gli hub IoT di Azure contengono molti componenti di base di [Hub eventi di Azure](../event-hubs/event-hubs-features.md), tra cui le [partizioni](../event-hubs/event-hubs-features.md#partitions). I flussi di eventi per gli hub IoT vengono in genere popolati con dati di telemetria in ingresso che vengono segnalati da vari dispositivi IoT. Il partizionamento del flusso di eventi consente di ridurre le contese che si verificano durante la lettura e la scrittura simultanee nei flussi di eventi.

Il limite di partizioni viene scelto al momento della creazione dell'hub IoT Hub e non può essere modificato. Il limite massimo di partizioni per l'hub IoT di livello base e l'hub IoT di livello standard è 32.The maximum partition limit for basic tier IoT Hub and standard tier IoT Hub is 32. La maggior parte degli hub IoT richiede solo 4 partizioni. Per altre informazioni sulla determinazione delle partizioni, vedere la domanda frequente su Hub eventi [Quante partizioni sono necessarie?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Aggiornamento del livello

Dopo aver creato l'hub IoT, è possibile eseguire l'aggiornamento dal livello Basic al livello Standard senza interrompere le attività in corso. Per altre informazioni, vedere [Come aggiornare l'hub IoT](iot-hub-upgrade.md).

Con la migrazione dal livello di base al livello standard la configurazione della partizione rimane invariata.

> [!NOTE]
> Il livello gratuito non supporta l'aggiornamento a base o standard.

## <a name="iot-hub-rest-apis"></a>API REST dell'hub IoT

La differenza a livello di funzionalità supportate tra i livelli Basic e Standard dell'hub IoT è che alcune chiamate API non funzionano con gli hub di livello Basic. La tabella seguente illustra le API disponibili:

| API | Livello Basic | Livello gratuito/Standard |
| --- | ---------- | ------------- |
| [Eliminare un dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Sì | Sì |
| [Recupero dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Sì | Sì |
| [Eliminazione modulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Sì | Sì |
| [Recupero modulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Sì | Sì |
| [Recupero statistiche del Registro di sistema](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Sì | Sì |
| [Ottenere le statistiche dei serviziGet services statistics](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Sì | Sì |
| [Creare o aggiornare un dispositivo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Sì | Sì |
| [Crea o aggiorna modulo](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Sì | Sì |
| [Query su hub IoT](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Sì | Sì |
| [Creazione URI di firma di accesso condiviso per il caricamento di file](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Sì | Sì |
| [Ricezione notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Sì | Sì |
| [Invio evento dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Sì | Sì |
| Invio evento modulo | Solo AMQP e MQTT | Solo AMQP e MQTT |
| [Aggiornamento stato di caricamento file](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Sì | Sì |
| [Operazioni in blocco su dispositivi](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Sì, ad eccezione delle funzionalità di IoT Edge | Sì |
| [Annullare il processo di esportazione di importazione](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Sì | Sì |
| [Crea processo di esportazione di importazione](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Sì | Sì |
| [Ottenere il processo di esportazione di importazioneGet import export job](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Sì | Sì |
| [Ottenere processi di esportazione di importazioneGet import export jobs](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Sì | Sì |
| [Eliminazione coda di comandi](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Sì |
| [Recuperare il dispositivo gemello](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Sì |
| [Recupero modulo gemello](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Sì |
| [Richiamo metodo dispositivo](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Sì |
| [Aggiornamento dispositivo gemello](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Sì |
| [Aggiornamento modulo gemello](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Sì |
| [Abbandono notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Sì |
| [Completamento notifica di dispositivo associato](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Sì |
| [Annulla processo](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Sì |
| [Crea processo](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Sì |
| [Recupero processo](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Sì |
| [Query sui processi](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Sì |

## <a name="message-throughput"></a>Velocità effettiva dei messaggi

Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per unità. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Il traffico viene misurato per l'hub IoT in base all'unità. Quando si crea un hub IoT, si sceglie il livello e l'edizione e si imposta il numero di unità disponibili. È possibile acquistare fino a 200 unità per l'edizione B1, B2, S1 o S2 oppure fino a 10 unità per l'edizione B3 o S3. Dopo aver creato l'hub IoT, è possibile modificare il numero di unità disponibili all'interno dell'edizione, l'aggiornamento o il downgrade tra le edizioni all'interno del livello (da B1 a B2) o l'aggiornamento dal livello di base al livello standard (da B1 a S1) senza interrompere le operazioni esistenti. Per altre informazioni, vedere [Come aggiornare l'hub IoT](iot-hub-upgrade.md).  

Come esempio delle capacità di traffico di ogni livello, i messaggi da dispositivo a cloud seguono queste linee guida di velocità effettiva sostenuta:

| Edizione di livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| --- | --- | --- |
| B1, S1 |Fino a 1.111 KB al minuto per unità<br/>(1,5 GB al giorno per unità) |Una media di 278 messaggi al minuto per unità<br/>(400.000 messaggi al giorno per unità) |
| B2, S2 |Fino a 16 MB al minuto per unità<br/>(22,8 GB al giorno per unità) |Una media di 4.167 messaggi al minuto per unità<br/>(6 milioni di messaggi al giorno per unità) |
| B3, S3 |Fino a 814 MB al minuto per unità<br/>(1144,4 GB al giorno per unità) |Una media di 208,333 messaggi al minuto per unità<br/>(300 milioni di messaggi al giorno per unità) |

La velocità effettiva da dispositivo a cloud è solo una delle metriche da considerare quando si progetta una soluzione IoT.Device-to-cloud throughput is only one of the metrics you need to consider when designing an IoT solution. Per informazioni più complete, vedere [Quote e limitazioni dell'hub IoT](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Velocità effettiva delle operazioni del registro delle identità

Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime perché sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere le [quote e limitazioni dell'hub IoT](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Scalabilità automatica

Se si sta avvicinando il limite consentito di messaggi nell'hub IoT, è possibile usare questi passaggi per ridimensionare automaticamente per incrementare un'unità hub IoT nello stesso livello hub IoT.If you are approaching the allowed message limit on your IoT hub, you can use these [steps to automatically scale](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) to increment an IoT Hub unit in the same IoT Hub tier.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle funzionalità dell'hub IoT e per dettagli sulle prestazioni, vedere [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub) o [Quote e limitazioni dell'hub IoT](iot-hub-devguide-quotas-throttling.md).

* Per cambiare il livello dell'hub IoT, seguire la procedura descritta in [How to upgrade your IoT hub](iot-hub-upgrade.md) (Come eseguire l'aggiornamento dell'hub IoT).
