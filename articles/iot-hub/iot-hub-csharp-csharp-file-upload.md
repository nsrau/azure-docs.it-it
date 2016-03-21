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
     ms.date="02/03/2016"
     ms.author="elioda"/>

# Esercitazione: caricare file dai dispositivi al cloud con l'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Le esercitazioni precedenti ([Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT]) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT e come accedervi dai dispositivi e dai componenti cloud. [Elaborare messaggi da dispositivo a cloud] ha illustrato come archiviare in modo affidabile i messaggi da dispositivo a cloud nell'archivio BLOB di Azure. In alcuni casi, tuttavia, i dati provenienti dai dispositivi non eseguono facilmente il mapping a messaggi da dispositivo a cloud relativamente piccoli. Alcuni esempi sono i file di grandi dimensioni contenenti immagini, video, dati di esempio sulle vibrazioni ad alta frequenza o contenenti qualche tipo di dati pre-elaborati. Questi dati in genere vengono elaborati in modalità batch con strumenti come [Data factory di Azure] o lo stack [Hadoop]. Quando si preferisce caricare un file da un dispositivo invece di inviare eventi, è ugualmente possibile usare le funzionalità di sicurezza e affidabilità dell'hub IoT.

Questa esercitazione si basa sul codice presentato in [Inviare messaggi da cloud a dispositivo con l'hub IoT] per mostrare come usare i messaggi da cloud a dispositivo per fornire in modo sicuro al dispositivo un URI BLOB di Azure da usare per caricare il file e come usare le conferme di recapito di hub IoT per attivare l'elaborazione del file dal back-end app. Il vantaggio di questo approccio è la possibilità di riutilizzare l'identità del dispositivo dell'hub IoT e la conferma di recapito dei messaggi da cloud a dispositivo per informare il back-end app che il file è stato caricato correttamente.

> [AZURE.NOTE] Lo stesso approccio usato qui può essere adottato per far scaricare ai dispositivi i file dal cloud in modo sicuro.

È possibile trovare altre informazioni sui messaggi da cloud a dispositivo e sulla sicurezza dell'hub IoT nella [Guida per sviluppatori di hub IoT].

Al termine di questa esercitazione si eseguiranno due applicazioni console Windows:

* **SimulatedDevice**, una versione modificata dell'app creata in [Inviare messaggi da cloud a dispositivo con l'hub IoT], che si connette all'hub IoT e riceve messaggi da cloud a dispositivo contenenti URI BLOB di Azure. Per ogni messaggio da cloud a dispositivo ricevuto, attiva un caricamento file nell'URI BLOB specificato.
* **SendCloudToDevice**, che compila un URI BLOB di Azure (come illustrato in [Creare e usare una firma di accesso condiviso con il servizio BLOB](../storage/storage-dotnet-shared-access-signature-part-2.md)), lo invia in un messaggio da cloud a dispositivo al dispositivo simulato con l'hub IoT e quindi ne riceve la conferma di recapito.

> [AZURE.NOTE] L'hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo Azure IoT. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure. Gli SDK del servizio Azure IoT per Java e Node saranno presto disponibili.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.  In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per le app **SimulatedDevice** e **SendCloudToDevice**.

2.  Premere **F5** e dovrebbe essere visualizzato l'avvio di tutte le applicazioni. Selezionare la finestra **SendCloudToDevice** e premere un tasto. Il dispositivo simulato genererà un messaggio dopo il caricamento del file e l'app **SendCloudToDevice** visualizzerà l'avvenuta ricezione del feedback. È possibile usare il [portale di Azure] o Esplora server di Visual Studio per verificare la presenza del file nell'account di archiviazione.

  ![][50]


## Passaggi successivi

In questa esercitazione si è appreso come sfruttare i messaggi da cloud a dispositivo per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT con l'esercitazione seguente:

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
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0309_2016-->