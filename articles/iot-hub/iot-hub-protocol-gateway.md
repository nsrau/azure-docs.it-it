---
title: Gateway del protocollo Azure IoT | Microsoft Docs
description: "Viene descritto come utilizzare il gateway del protocollo IoT Azure per estendere le funzionalità e il supporto del protocollo dell’hub IoT Azure"
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
ms.date: 08/23/2016
ms.author: kdotchko
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cae05552afa665eb93a0fee5c49d8d8ec5a6a400


---
# <a name="supporting-additional-protocols-for-iot-hub"></a>Supporto di protocolli aggiuntivi per hub IoT
L'hub IoT di Azure supporta in modo nativo la comunicazione tramite i protocolli MQTT, AMQP e HTTP. In alcuni casi i dispositivi o gateway campo potrebbero non essere in grado di utilizzare uno di questi protocolli standard e richiederanno l'adattamento del protocollo. In questi casi, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato può abilitare l’adattamento del protocollo per gli endpoint dell’hub IoT con il bridging del traffico da e verso l’hub IoT. È possibile usare il [gateway del protocollo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) come gateway personalizzato per consentire l'adattamento del protocollo per l'hub IoT.

## <a name="azure-iot-protocol-gateway"></a>gateway del protocollo Azure IoT
Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway di protocollo è un componente pass-through che accetta le connessioni ai dispositivi tramite un protocollo specifico. Colma il traffico all'hub IoT su AMQP 1.0. Il gateway di protocollo IoT di Azure è disponibile come un progetto software open source per fornire la flessibilità per l'aggiunta di supporto per un'ampia gamma di protocolli e versioni del protocollo.

Il gateway del protocollo può essere distribuito in Azure in modo altamente scalabile utilizzando i ruoli di lavoro dei servizi cloud di Azure. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway del protocollo Azure IoT include un adattatore di protocollo MQTT che consente di personalizzare il comportamento del protocollo MQTT, se necessario. Poiché l'hub IoT fornisce il supporto incorporato per il protocollo v3.1.1 MQTT, è opportuno considerare solo l'uso dell'adattatore del protocollo MQTT se sono richieste personalizzazioni del protocollo o requisiti specifici per la funzionalità aggiuntive.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Il modello di programmazione del gateway del protocollo IoT di Azure consente anche di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, la trasformazioni di messaggi, la compressione/decompressione o la crittografia/decrittografia del traffico tra i dispositivi e l'hub IoT.

Ai fini della flessibilità, il gateway del protocollo e l'implementazione di MQTT vengono forniti come un progetto software open source. In questo modo è possibile personalizzare l'implementazione in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sul gateway del protocollo IoT di Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere:

* [Eseguire il confronto con Hub eventi][lnk-compare]
* [Scalabilità, disponibilità elevata e ripristino di emergenza][lnk-scaling]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO3-->


