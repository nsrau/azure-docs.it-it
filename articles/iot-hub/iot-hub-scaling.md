---
title: Ridimensionamento dell&quot;hub IoT di Azure | Microsoft Docs
description: "Come ridimensionare l&quot;hub IoT per supportare la velocità effettiva dei messaggi prevista. Include un riepilogo della velocità effettiva supportata per ogni livello e le opzioni per il partizionamento orizzontale."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 03eeecc575be3073066a203cdd4ec6944bb23afc
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="scale-your-iot-hub-solution"></a>Ridimensionare la soluzione hub IoT
Hub IoT di Azure può supportare fino a un milione di dispositivi connessi contemporaneamente. Per altre informazioni, vedere i [prezzi dell'hub IoT][lnk-pricing]. Ogni unità hub IoT mette a disposizione un certo numero di messaggi giornalieri.

Per il ridimensionamento corretto della soluzione, considerare l'uso specifico che viene fatto dell'hub IoT. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles] e progettare la propria soluzione di conseguenza.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Velocità effettiva dei messaggi da dispositivo a cloud e da cloud a dispositivo.
Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per unità.

I messaggi da dispositivo a cloud seguono queste linee guida in caso di velocità effettiva sostenuta

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| --- | --- | --- |
| S1 |Fino a 1.111 KB al minuto per unità<br/>(1,5 GB al giorno per unità) |Una media di 278 messaggi al minuto per unità<br/>(400.000 messaggi al giorno per unità) |
| S2 |Fino a 16 MB al minuto per unità<br/>(22,8 GB al giorno per unità) |Una media di 4.167 messaggi al minuto per unità<br/>(6 milioni di messaggi al giorno per unità) |
| S3 |Fino a 814 MB al minuto per unità<br/>(1144,4 GB al giorno per unità) |Una media di 208,333 messaggi al minuto per unità<br/>(300 milioni di messaggi al giorno per unità) |

## <a name="identity-registry-operation-throughput"></a>Velocità effettiva delle operazioni del registro delle identità
Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime perché sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere le [quote e limitazioni dell'hub IoT][IoT Hub quotas and throttles].

## <a name="sharding"></a>Partizionamento orizzontale
Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In tal caso, è consigliabile partizionare i dispositivi in più hub IoT. Più hub IoT appianano i picchi di traffico e ottengono il throughput necessario o i tassi di operazione richiesti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con Azure IoT Edge][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

