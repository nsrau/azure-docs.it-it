<properties
	pageTitle="Elaborare messaggi da dispositivo a cloud dell'hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per conoscere utili modelli di elaborazione dei messaggi da dispositivo a cloud dell'hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Le esercitazioni precedenti ([Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT]) illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT e come accedervi dai dispositivi e dai componenti cloud.

Questa esercitazione si basa sul codice fornito in [Introduzione all'hub IoT] per presentare due modelli di elaborazione dei messaggi da dispositivo a cloud.

Il primo modello prevede l'archiviazione affidabile dei messaggi da dispositivo a cloud nei [BLOB di Azure]. Questo scenario è molto comune quando si implementa l'analisi dei *percorsi a freddo*, in cui i dati archiviati nei BLOB vengono usati come input per l'analisi basata su strumenti come [Data factory di Azure] o lo stack [Hadoop].

Il secondo modello prevede l'elaborazione affidabile di messaggi da dispositivo a cloud *interattivi*. I messaggi da dispositivo a cloud sono detti *interattivi* quando sono trigger immediati per un set di azioni nel back-end applicazioni, contrariamente a un messaggio di *punto dati* che viene inserito in un motore di analisi. Ad esempio, un avviso proveniente da un dispositivo che deve attivare l'inserimento di un ticket in un sistema CRM è un messaggio da dispositivo a cloud *interattivo*, contrariamente a un messaggio relativo alla telemetria contenente esempi di temperatura, che è un messaggio di *punto dati*.

Poiché l'hub IoT espone un endpoint compatibile con Hub eventi per ricevere i messaggi da dispositivo a cloud, questa esercitazione usa [EventProcessorHost] per ospitare una classe di processori di eventi, che:

* Archivia in modo affidabile i messaggi di *punto dati* nei BLOB di Azure
* Inoltra i messaggi da dispositivo a cloud *interattivi* a una [coda del bus di servizio] per l'elaborazione immediata.

Il [bus di servizio][Service Bus Queue] è un ottimo modo per assicurare un'elaborazione affidabile dei messaggi interattivi, perché fornisce checkpoint per ogni messaggio e la deduplicazione basata su finestre temporali.

Al termine di questa esercitazione si eseguiranno tre applicazioni console Windows:

* **SimulatedDevice**, una versione modificata dell'app creata in [Introduzione all'hub IoT], che invia i messaggi da dispositivo a cloud dei *punti dati* ogni secondo e i messaggi da dispositivo a cloud *interattivi* ogni 10 secondi.
* **ProcessDeviceToCloudMessages**, che usa [EventProcessorHost] per archiviare in modo affidabile i messaggi dei *punti dati* in un BLOB di Azure e inoltra i messaggi *interattivi* a una coda del bus di servizio.
* **ProcessD2cInteractiveMessages**, che rimuove dalla coda i messaggi.

> [AZURE.NOTE]L'hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo Azure IoT. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure.

> [AZURE.NOTE]Il contenuto di questa esercitazione è direttamente applicabile ad altri modi di utilizzare i messaggi compatibili con Hub eventi, ad esempio i progetti [Hadoop] come Storm. Per altre informazioni, vedere [Linee guida dell'hub IoT - Compatibilità con Hub eventi].

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

È richiesta anche una certa conoscenza del servizio di [Archiviazione di Azure] e del [Bus di servizio di Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2cInteractiveMessages**.

2.	Premere **F5**. Tutte le applicazioni verranno avviate e ogni messaggio interattivo inviato dal dispositivo simulato verrà elaborato dal processore di messaggi interattivi.

  ![][50]

> [AZURE.NOTE]Per visualizzare il file BLOB mentre viene aggiornato, potrebbe essere necessario impostare la costante `MAX_BLOCK_SIZE` in `StoreEventProcessor` su un valore più basso (ad esempio, `1024`). Infatti il raggiungimento del limite di dimensione del blocco con i dati inviati dal dispositivo simulato può richiedere qualche minuto. Con tale modifica, dovrebbe essere possibile visualizzare la creazione e l'aggiornamento di un BLOB nel contenitore di archiviazione.

## Passaggi successivi

In questa esercitazione si è appreso come elaborare in modo affidabile i messaggi da dispositivo a cloud dei *punti dati* e *interattivi* con [EventProcessorHost]. Una logica analoga di elaborazione dei messaggi può essere implementata con:

- [Caricamento di file da dispositivi] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[BLOB di Azure]: https://azure.microsoft.com/it-IT/documentation/articles/storage-dotnet-how-to-use-blobs/
[Data factory di Azure]: https://azure.microsoft.com/it-IT/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/it-IT/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/it-IT/documentation/articles/service-bus-dotnet-how-to-use-queues/
[coda del bus di servizio]: https://azure.microsoft.com/it-IT/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/it-IT/library/hh680901(v=pandp.50).aspx
[Gestione degli errori temporanei]: https://msdn.microsoft.com/it-IT/library/hh680901(v=pandp.50).aspx

[Linee guida dell'hub IoT - Compatibilità con Hub eventi]: iot-hub-guidance.md#eventhubcompatible

[Archiviazione di Azure]: https://azure.microsoft.com/it-IT/documentation/services/storage/
[Bus di servizio di Azure]: https://azure.microsoft.com/it-IT/documentation/services/service-bus/

[Azure portal]: https://portal.azure.com/

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->