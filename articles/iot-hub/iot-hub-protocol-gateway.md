<properties
   pageTitle="Gateway del protocollo IoT Azure | Microsoft Azure"
   description="Viene descritto come utilizzare il gateway del protocollo IoT Azure per estendere le funzionalità e il supporto del protocollo dell’hub IoT Azure"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchko"/>

# Supporto di protocolli aggiuntivi per hub IoT

L’hub IoT di Azure supporta in modo nativo la comunicazione tramite i protocolli HTTPS e AMQP. In alcuni casi i dispositivi o gateway campo potrebbero non essere in grado di utilizzare uno di questi protocolli standard e richiederanno l'adattamento del protocollo. In questi casi, è possibile utilizzare un gateway personalizzato. Un gateway personalizzato può abilitare l’adattamento del protocollo per gli endpoint dell’hub IoT con il bridging del traffico da e verso l’hub IoT. Un gateway di protocollo Azure Internet delle cose (IoT) consente l'adattamento del protocollo per l'IoT Hub. Implementa inoltre un adattatore di protocollo MQTT per consentire la comunicazione tra un dispositivo IoT e l’hub IoT tramite il protocollo MQTT.

## Gateway del protocollo IoT Azure

Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway di protocollo è un componente pass-through che accetta le connessioni ai dispositivi tramite un protocollo specifico. Colma il traffico all'hub IoT su AMQP 1.0. Il gateway di protocollo IoT è disponibile come un progetto software open source per fornire la flessibilità per l'aggiunta di supporto per un'ampia gamma di protocolli e versioni del protocollo.

Il gateway del protocollo può essere distribuito in Azure in modo altamente scalabile utilizzando i ruoli di lavoro dei servizi cloud di Azure. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway del protocollo IoT Azure include un adattatore MQTT per semplificare la comunicazione con i dispositivi tramite il protocollo MQTT v3.1.1. Il gateway di protocollo e l'implementazione di MQTT vengono forniti come un progetto software open source per la flessibilità. In questo modo è possibile personalizzare l'implementazione in base alle esigenze.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Inoltre, il modello di programmazione del gateway del protocollo IoT consente di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, la trasformazioni di messaggi, la compressione/decompressione o la crittografia/decrittografia del traffico tra i dispositivi e l’hub IoT.

## Passaggi successivi

Per ulteriori informazioni sul gateway del protocollo IoT di Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1223_2015-->