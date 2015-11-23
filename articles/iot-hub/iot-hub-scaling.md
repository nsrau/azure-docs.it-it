<properties
 pageTitle="Hub IoT Azure Scalabilità | Microsoft Azure"
 description="Descrive come ridimensionare Hub IoT Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Ridimensionamento dell'hub IoT

L'hub IoT può supportare fino a un milione di dispositivi connessi simultaneamente incrementando il numero di unità S1 o S2 dell'hub IoT fino a 2,000. Per ulteriori informazioni, vedere [prezzi di IoT Hub][lnk-pricing].

Ogni unità dell'hub IoT consente un certo numero di identità del dispositivo nel relativo registro, che possono essere tutte connesse simultaneamente, e un certo numero di messaggi giornalieri.

Per il ridimensionamento corretto della soluzione, considerare l'uso specifico che viene fatto dell'hub IoT. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT][] e progettare la propria soluzione di conseguenza.

## Velocità effettiva dei messaggi da dispositivo a cloud e da cloud a dispositivo.

Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per dispositivo.

I messaggi da dispositivo a cloud seguono queste linee guida in caso di velocità effettiva sostenuta

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| ---- | -------------------- | ------------------- |
| S1 | Fino a 8 KB/ora per dispositivo | Media di 4 messaggi/ora per dispositivo |
| S2 | Fino a 4 KB/ora per dispositivo | Media di 2 messaggi/minuto per dispositivo |

Quando si ricevono messaggi da dispositivo a cloud, il back-end dell'applicazione può prevedere la velocità effettiva massima seguente (su tutti i lettori).

| Livello | Velocità effettiva sostenuta |
| ---- | -------------------- |
| S1 | Fino a 120 KB/minuto per untià, con un di minimo 2 MB/secondo |
| S2 | Fino a 4 MB/secondo per unità, con un massimo di 2 MB/secondo |

Le prestazioni dei messaggi da cloud a dispositivo vengono ridimensionate per dispositivo, con ogni dispositivo che riceve al massimo 5 messaggi al minuto.

## Velocità effettiva delle operazioni del registro delle identità

Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime, in quanto sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere [Quote e limitazioni dell'hub IoT][].

## Partizionamento orizzontale

Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In questo caso, è consigliabile partizionare i dispositivi in più hub IoT, per supportare le condizioni di burst del traffico e ottenere la velocità effettiva richiesta o le velocità operative richieste.

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[quote e limitazioni dell'hub IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Nov15_HO3-->