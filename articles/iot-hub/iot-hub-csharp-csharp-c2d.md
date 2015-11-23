<properties
	pageTitle="Inviare messaggi da cloud a dispositivo con l’hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per informazioni su come inviare messaggi da cloud a dispositivo utilizzando l’hub IoT Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Esercitazione: inviare messaggi da cloud a dispositivo con l'hub IoT

## Introduzione

L’hub IoT Azure è un servizio completamente gestito che consente la comunicazione bidirezionale affidabile e sicura tra milioni di dispositivi IoT e un'applicazione back-end. L’esercitazione [Introduzione all’hub IoT] illustra come creare un hub IoT, eseguire il provisioning dell’identità di un dispositivo al suo interno e creare il codice di un dispositivo simulato che invia messaggi da dispositivo a cloud.

Questa esercitazione si basa sull'[Introduzione all'hub IoT] e illustra come inviare messaggi da cloud a dispositivo a un unico dispositivo, come richiedere la conferma del recapito (*feedback*) e riceverla dal back-end cloud dell'applicazione.

È possibile trovare ulteriori informazioni sui messaggi da cloud a dispositivo nella [Guida per sviluppatori di hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione si eseguiranno due applicazioni console Windows:

* **SimulatedDevice**, una versione modificata dell'app creata in [Introduzione all’hub IoT], che si connette all'hub IoT e riceve messaggi da cloud a dispositivo.
* **SendCloudToDevice**, che invia un messaggio da cloud a dispositivo al dispositivo simulato tramite hub IoT e riceve quindi la conferma del recapito.

> [AZURE.NOTE]L’hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo IoT Azure. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure. Gli SDK del servizio Azure IoT per Java e Node saranno presto disponibili.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Versione di prova gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## Passaggi successivi

In questa esercitazione è stato descritto come inviare e ricevere messaggi da cloud a dispositivo. È possibile continuare a esplorare le funzionalità e lo scenario dell’hub IoT con le esercitazioni seguenti:

- [Elaborare messaggi da dispositivo a cloud], illustra come elaborare in modo affidabile telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricamento di file da dispositivi], descrive un modello che utilizza i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell’hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell’hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure preview portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell’hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell’hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Introduzione all’hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=Nov15_HO3-->