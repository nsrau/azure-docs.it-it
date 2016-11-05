---
title: Hub IoT Azure Scalabilità | Microsoft Docs
description: Descrive come ridimensionare Hub IoT Azure.
services: iot-hub
documentationcenter: ''
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda

---
# Ridimensionamento dell'hub IoT
Hub IoT di Azure può supportare fino a un milione di dispositivi connessi contemporaneamente. Per ulteriori informazioni, vedere [prezzi di dell’hub IoT][lnk-pricing]. Ogni unità hub IoT mette a disposizione un certo numero di messaggi giornalieri.

Per il ridimensionamento corretto della soluzione, considerare l'uso specifico che viene fatto dell'hub IoT. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT][quote e limitazioni dell'hub IoT] e progettare la propria soluzione di conseguenza.

## Velocità effettiva dei messaggi da dispositivo a cloud e da cloud a dispositivo.
Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per unità.

I messaggi da dispositivo a cloud seguono queste linee guida in caso di velocità effettiva sostenuta

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| --- | --- | --- |
| S1 |Fino a 1.111 KB al minuto per unità<br/>(1,5 GB al giorno per unità) |Una media di 278 messaggi al minuto per unità<br/>(400.000 messaggi al giorno per unità) |
| S2 |Fino a 16 MB al minuto per unità<br/>(22,8 GB al giorno per unità) |Una media di 4.167 messaggi al minuto per unità<br/>(6 milioni di messaggi al giorno per unità) |
| S3 |Fino a 814 MB al minuto per unità<br/>(1144,4 GB al giorno per unità) |Una media di 208,333 messaggi al minuto per unità<br/>(300 milioni di messaggi al giorno per unità) |

## Velocità effettiva delle operazioni del registro delle identità
Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime, in quanto sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere [Quote e limitazioni dell'hub IoT][Quote e limitazioni dell'hub IoT].

## Partizionamento orizzontale
Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In tal caso, è consigliabile partizionare i dispositivi in più hub IoT. Più hub IoT appianano i picchi di traffico e ottengono il throughput necessario o i tassi di operazione richiesti.

## Passaggi successivi
Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori][lnk-devguide]
* [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
* [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
* [Gestire hub IoT tramite il portale di Azure][lnk-portal]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[quote e limitazioni dell'hub IoT]: iot-hub-devguide.md#throttling

[lnk-design]: iot-hub-guidance.md
[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0921_2016-->