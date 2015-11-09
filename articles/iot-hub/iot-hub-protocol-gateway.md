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

L’hub IoT supporta in modo nativo la comunicazione tramite i protocolli HTTPS e AMQP. In alcuni casi i dispositivi o gateway campo potrebbero non essere in grado di utilizzare uno di questi protocolli standard e richiederanno l'adattamento del protocollo. In questi casi, un gateway personalizzato può abilitare l’adattamento del protocollo per gli endpoint dell’hub IoT con il bridging del traffico da e verso l’hub IoT. Il gateway del protocollo IoT Azure consente l'adattamento del protocollo per l’hub IoT e implementa un adattatore del protocollo MQTT per consentire la comunicazione tra l’hub IoT e il dispositivo IoT tramite il protocollo MQTT.

## Gateway del protocollo IoT Azure

Il gateway del protocollo IoT Azure è un framework per l'adattamento del protocollo progettato per la comunicazione bidirezionale dei dispositivi su vasta scala con hub IoT. Il gateway del protocollo è un componente pass-through che accetta connessioni dei dispositivi tramite un protocollo specifico e che esegue il bridging del traffico all'hub IoT Hub tramite AMQP 1.0. Il gateway del protocollo IoT è disponibile come progetto software open-source (OSS) per fornire flessibilità per l'aggiunta del supporto per una vasta gamma di protocolli e versioni del protocollo.

Il gateway del protocollo può essere distribuito in Azure in modo altamente scalabile utilizzando i ruoli di lavoro dei servizi cloud. Inoltre, il gateway del protocollo può essere distribuito in ambienti locali come gateway di campo.

Il gateway del protocollo IoT Azure include un adattatore MQTT per semplificare la comunicazione con i dispositivi tramite il protocollo MQTT v3.1.1. Il gateway del protocollo e l'implementazione di MQTT vengono forniti come progetto OSS per la flessibilità per consentire la personalizzazione dell'implementazione in base alle esigenze.

L'adattatore MQTT viene dimostra inoltre il modello di programmazione per la creazione di adattatori del protocollo per altri protocolli. Inoltre, il modello di programmazione del gateway del protocollo IoT consente di collegare componenti personalizzati per l'elaborazione specializzata, ad esempio l'autenticazione personalizzata, trasformazioni di messaggi, compressione/decompressione o crittografia/decrittografia del traffico tra i dispositivi e l’hub IoT.

## Passaggi successivi

Per ulteriori informazioni sul gateway del protocollo IoT Azure e su come utilizzarlo e distribuirlo come parte della soluzione IoT, vedere:

* [Archivio gateway del protocollo IoT Azure su GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guida per sviluppatori del gateway del protocollo IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=Nov15_HO1-->