<properties
	pageTitle="Caricare file dai dispositivi con l'hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per informazioni su come caricare file dai dispositivi usando l'hub IoT di Azure con C#."
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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Esercitazione: caricare file dai dispositivi al cloud con l'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Le esercitazioni precedenti ([Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT]) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT, mentre l'esercitazione [Elaborare i messaggi da dispositivo a cloud] fornisce istruzioni sull'archiviazione affidabile dei messaggi da dispositivo a cloud nell'archiviazione BLOB di Azure. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Gli esempi includono file di grandi dimensioni contenenti immagini, video, dati di esempio sulle vibrazioni ad alta frequenza o contenenti qualche tipo di dati pre-elaborati. Questi dati in genere vengono elaborati nel cloud con strumenti come [Data factory di Azure] o lo stack [Hadoop]. Quando si preferisce caricare un file da un dispositivo invece di inviare eventi, è ugualmente possibile usare le funzionalità di sicurezza e affidabilità dell'hub IoT.

Questa esercitazione si basa sul codice dell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT] che illustra come usare le funzionalità di caricamento di file dell'hub IoT. L'esercitazione illustra come fornire a un dispositivo, in modo sicuro, un URI del BLOB di Azure per caricare un file e come usare le notifiche di caricamento file dell'hub IoT per attivare l'elaborazione del file nel back-end dell'app.

Al termine di questa esercitazione si eseguiranno due applicazioni console Windows:

* **SimulatedDevice**: una versione modificata dell'applicazione creata nell'esercitazione [Inviare messaggi da cloud a dispositivo con l'hub IoT] che consente di caricare un file nell'archiviazione tramite un URI con firma di accesso condiviso fornito dall'hub IoT.
* **ReadFileUploadNotification** riceve le notifiche di caricamento file dall'hub IoT.

> [AZURE.NOTE] L'hub IoT supporta numerose piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli Azure IoT SDK per dispositivi. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile crearne uno gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Da Visual Studio fare clic con il pulsante destro sulla soluzione e selezionare **Imposta progetti di avvio...**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per **ReadFileUploadNotification** e **SimulatedDevice**.

2. Premere **F5**. Si avviano entrambe le applicazioni. Verrà visualizzato il messaggio di completamento del caricamento in un'applicazione console e il messaggio di notifica di caricamento ricevuto da altre app console. È possibile usare il [portale di Azure] o Esplora server di Visual Studio per verificare la presenza del file caricato nell'account di archiviazione.

  ![][50]


## Passaggi successivi

In questa esercitazione si è appreso come sfruttare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT con l'esercitazione seguente:

- [Elaborare messaggi da dispositivo a cloud] illustra come elaborare in modo affidabile telemetria e messaggi interattivi provenienti dai dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[portale di Azure]: https://portal.azure.com/

[Data factory di Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Elaborare i messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->