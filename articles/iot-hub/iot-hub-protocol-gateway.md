---
title: Gateway del protocollo Azure IoT | Microsoft Docs
description: "Come usare un gateway di protocollo IoT di Azure per estendere le funzionalità dell&quot;hub IoT e il supporto del protocollo per consentire ai dispositivi di connettersi all&quot;hub usando protocolli non supportati dall&quot;hub IoT in modo nativo."
services: iot-hub
documentationcenter: 
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: kdotchko
translationtype: Human Translation
ms.sourcegitcommit: 1a8dd3d024fbe1e85a43c5b4f98901ab59c5797f
ms.openlocfilehash: e0a7c813da53bc6ab49a456f13227b62725c5fc4


---
# <a name="support-additional-protocols-for-iot-hub"></a>Supportare altri protocolli per l'hub IoT
L'hub IoT di Azure supporta in modo nativo la comunicazione tramite i protocolli MQTT, AMQP e HTTP. In alcuni casi i dispositivi o gateway campo potrebbero non essere in grado di utilizzare uno di questi protocolli standard e richiederanno l'adattamento del protocollo. In questi casi, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato può abilitare l’adattamento del protocollo per gli endpoint dell’hub IoT con il bridging del traffico da e verso l’hub IoT. È possibile usare il [gateway del protocollo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) come gateway personalizzato per consentire l'adattamento del protocollo per l'hub IoT.

## <a name="azure-iot-protocol-gateway"></a>gateway del protocollo Azure IoT
Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway di protocollo è un componente pass-through che accetta le connessioni ai dispositivi tramite un protocollo specifico. Colma il traffico all'hub IoT su AMQP 1.0. Il gateway di protocollo IoT di Azure è disponibile come un progetto software open source che offre la flessibilità necessaria per aggiungere supporto per diversi protocolli e versioni di protocollo.

È possibile distribuire il gateway di protocollo in Azure in modo estremamente scalabile con Azure Service Fabric, ruoli di lavoro di Servizi cloud di Azure o macchine virtuali di Windows. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway di protocollo Azure IoT include un adattatore di protocollo MQTT che consente di personalizzare il comportamento del protocollo MQTT, se necessario. Poiché l'hub IoT offre il supporto incorporato per il protocollo v3.1.1 MQTT, è opportuno considerare solo l'uso dell'adattatore del protocollo MQTT se sono necessari personalizzazioni del protocollo o requisiti specifici per le funzionalità aggiuntive.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Il modello di programmazione del gateway del protocollo IoT di Azure consente anche di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, la trasformazioni di messaggi, la compressione/decompressione o la crittografia/decrittografia del traffico tra i dispositivi e l'hub IoT.

Ai fini della flessibilità, il gateway del protocollo e l'implementazione di MQTT vengono forniti come un progetto software open source. In questo modo è possibile personalizzare l'implementazione in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sul gateway del protocollo IoT di Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Eseguire il confronto con Hub eventi][lnk-compare]
* [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]
* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md



<!--HONumber=Jan17_HO2-->


