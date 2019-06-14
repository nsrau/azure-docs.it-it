---
title: Gateway del protocollo Azure IoT | Microsoft Docs
description: Come usare un gateway di protocollo IoT di Azure per estendere le funzionalità dell'hub IoT e il supporto del protocollo per consentire ai dispositivi di connettersi all'hub usando protocolli non supportati dall'hub IoT in modo nativo.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 9dbb7905c2a0fed65ede610577e0fa11a1deef92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345397"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Supportare altri protocolli per l'hub IoT

L'hub IoT di Azure supporta in modo nativo la comunicazione tramite i protocolli MQTT, AMQP e HTTPS. In alcuni casi i dispositivi o i gateway sul campo potrebbero non riuscire a usare uno di questi protocolli standard e richiedono l'adattamento del protocollo. In questi casi, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato abilita l'adattamento del protocollo per gli endpoint dell'hub IoT con il bridging del traffico da e verso l'hub IoT. È possibile usare il [gateway del protocollo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) come gateway personalizzato per consentire l'adattamento del protocollo per l'hub IoT.

## <a name="azure-iot-protocol-gateway"></a>gateway del protocollo Azure IoT

Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway di protocollo è un componente pass-through che accetta le connessioni ai dispositivi tramite un protocollo specifico. Colma il traffico all'hub IoT su AMQP 1.0.

È possibile distribuire il gateway di protocollo in Azure in modo estremamente scalabile con Azure Service Fabric, ruoli di lavoro di Servizi cloud di Azure o macchine virtuali di Windows. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway di protocollo Azure IoT include un adattatore di protocollo MQTT che consente di personalizzare il comportamento del protocollo MQTT, se necessario. Poiché l'hub IoT offre il supporto incorporato per il protocollo v3.1.1 MQTT, è opportuno considerare solo l'uso dell'adattatore del protocollo MQTT se sono necessari personalizzazioni del protocollo o requisiti specifici per le funzionalità aggiuntive.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Il modello di programmazione del gateway del protocollo IoT di Azure consente anche di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, la trasformazioni di messaggi, la compressione/decompressione o la crittografia/decrittografia del traffico tra i dispositivi e l'hub IoT.

Ai fini della flessibilità, il gateway del protocollo di Azure IoT e l'implementazione di MQTT vengono forniti come progetto software open source. È possibile usare il progetto open source per aggiungere il supporto per svariati protocolli e versioni dei protocolli o per personalizzare l'implementazione dello scenario. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul gateway del protocollo IoT di Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Confrontare con l'hub eventi](iot-hub-compare-event-hubs.md)

* [La scalabilità, disponibilità elevata e ripristino di emergenza](iot-hub-scaling.md)

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)