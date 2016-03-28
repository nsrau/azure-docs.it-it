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
 ms.date="03/14/2016"
 ms.author="elioda"/>

# Ridimensionamento dell'hub IoT

Hub IoT di Azure può supportare fino a un milione di dispositivi connessi contemporaneamente. Per ulteriori informazioni, vedere [prezzi di dell’hub IoT][lnk-pricing]. Ogni unità hub IoT mette a disposizione, inoltre, un numero di messaggi giornalieri.

Per il ridimensionamento corretto della soluzione, considerare l'uso specifico che viene fatto dell'hub IoT. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT][] e progettare la propria soluzione di conseguenza.

## Velocità effettiva dei messaggi da dispositivo a cloud e da cloud a dispositivo.

Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per unità.

I messaggi da dispositivo a cloud seguono queste linee guida in caso di velocità effettiva sostenuta

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| ---- | -------------------- | ------------------- |
| S1 | Fino a 1.111 KB al minuto per unità<br/>(1,5 GB al giorno per unità) | Una media di 278 messaggi al minuto per unità<br/>(400.000 messaggi al giorno per unità) |
| S2 | Fino a 16 MB al minuto per unità<br/>(22,8 GB al giorno per unità) | Una media di 4.167 messaggi al minuto per unità<br/>(6 milioni di messaggi al giorno per unità) |

Le prestazioni dei messaggi da cloud a dispositivo vengono ridimensionate per dispositivo, con ogni dispositivo che riceve al massimo 5 messaggi al minuto.

## Velocità effettiva delle operazioni del registro delle identità

Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime, in quanto sono per lo più correlate al provisioning dei dispositivi.

Per i dati specifici sulle prestazioni in modalità burst, vedere [Quote e limitazioni dell'hub IoT][].

## Partizionamento orizzontale

Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In tal caso, è consigliabile partizionare i dispositivi in più hub IoT. Più hub IoT appianano i picchi di traffico e ottengono il throughput necessario o i tassi di operazione richiesti.

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione all'hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[quote e limitazioni dell'hub IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0316_2016-->