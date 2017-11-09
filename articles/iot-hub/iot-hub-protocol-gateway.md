---
title: Gateway del protocollo Azure IoT | Microsoft Docs
description: "Come usare un gateway di protocollo IoT di Azure per estendere le funzionalità dell'hub IoT e il supporto del protocollo per consentire ai dispositivi di connettersi all'hub usando protocolli non supportati dall'hub IoT in modo nativo."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 1ed8ec28b95bbc91b731fd7bb7b3f1f6654e7fcf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2017
---
# Supportare altri protocolli per l'hub IoT
L'hub IoT di Azure supporta in modo nativo la comunicazione tramite i protocolli MQTT, AMQP e HTTPS. In alcuni casi i dispositivi o i gateway sul campo potrebbero non riuscire a usare uno di questi protocolli standard e richiedono l'adattamento del protocollo. In questi casi, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato abilita l'adattamento del protocollo per gli endpoint dell'hub IoT con il bridging del traffico da e verso l'hub IoT. È possibile usare il [gateway del protocollo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) come gateway personalizzato per consentire l'adattamento del protocollo per l'hub IoT.

## gateway del protocollo Azure IoT
Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway di protocollo è un componente pass-through che accetta le connessioni ai dispositivi tramite un protocollo specifico. Colma il traffico all'hub IoT su AMQP 1.0. 

È possibile distribuire il gateway di protocollo in Azure in modo estremamente scalabile con Azure Service Fabric, ruoli di lavoro di Servizi cloud di Azure o macchine virtuali di Windows. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway di protocollo Azure IoT include un adattatore di protocollo MQTT che consente di personalizzare il comportamento del protocollo MQTT, se necessario. Poiché l'hub IoT offre il supporto incorporato per il protocollo v3.1.1 MQTT, è opportuno considerare solo l'uso dell'adattatore del protocollo MQTT se sono necessari personalizzazioni del protocollo o requisiti specifici per le funzionalità aggiuntive.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Il modello di programmazione del gateway del protocollo IoT di Azure consente anche di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, la trasformazioni di messaggi, la compressione/decompressione o la crittografia/decrittografia del traffico tra i dispositivi e l'hub IoT.

Ai fini della flessibilità, il gateway del protocollo di Azure IoT e l'implementazione di MQTT vengono forniti come progetto software open source. È possibile usare il progetto open source per aggiungere il supporto per svariati protocolli e versioni dei protocolli o per personalizzare l'implementazione dello scenario. 

## Passaggi successivi
Per ulteriori informazioni sul gateway del protocollo IoT di Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Eseguire il confronto con Hub eventi][lnk-compare]
* [Ridimensionamento, disponibilità elevata e ripristino di emergenza][lnk-scaling]
* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
