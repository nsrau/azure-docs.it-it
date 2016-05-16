<properties
	pageTitle="Elaborare messaggi da dispositivo a cloud dell'hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per conoscere utili modelli di elaborazione dei messaggi da dispositivo a cloud dell'hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Altre esercitazioni, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Introduzione all'hub IoT] e illustra due modelli scalabili che è possibile usare per elaborare i messaggi da dispositivo a cloud:

- L'archiviazione affidabile dei messaggi da dispositivo a cloud nell'[Archivio BLOB di Azure]. Questo scenario è molto comune quando si analizzano *percorsi non critici*, in cui i dati di telemetria vengono archiviati nei BLOB per essere usati come input per processi di analisi basati su strumenti come [Azure Data Factory] o lo stack [HDInsight (Hadoop)].

- L'elaborazione affidabile di messaggi da dispositivo a cloud *interattivi*. I messaggi da dispositivo a cloud sono detti interattivi quando sono trigger immediati per un set di azioni nel back-end dell'applicazione, contrariamente ai messaggi di *punti dati* che vengono inseriti in un motore di analisi. Ad esempio, un allarme proveniente da un dispositivo che deve attivare l'inserimento di un ticket in un sistema CRM è un messaggio interattivo, mentre i dati di telemetria sulla temperatura di un dispositivo che devono essere archiviati per una successiva analisi sono un messaggio di punti dati.

Dal momento che l'hub IoT espone un endpoint compatibile con [Hub eventi][lnk-event-hubs] per ricevere i messaggi da dispositivo a cloud, questa esercitazione usa un'istanza di [EventProcessorHost] che:

* Archivia in modo affidabile i messaggi di *punti dati* nei BLOB di Azure.
* Inoltra i messaggi da dispositivo a cloud *interattivi* a una [coda del bus di servizio] per l'elaborazione immediata.

Il bus di servizio è un ottimo modo per assicurare un'elaborazione affidabile dei messaggi interattivi, perché fornisce checkpoint per ogni messaggio e la deduplicazione basata su finestre temporali.

> [AZURE.NOTE] Un'istanza **EventProcessorHost** è solo un modo per elaborare i messaggi interattivi. Altre opzioni includono [Azure Service Fabric][lnk-service-fabric] e [Analisi di flusso di Azure][lnk-stream-analytics].

Al termine di questa esercitazione verranno eseguite tre applicazioni console Windows:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Introduzione all'hub IoT], che invia messaggi di punti dati da dispositivo a cloud ogni secondo e messaggi interattivi da dispositivo a cloud ogni 10 secondi. Questa applicazione usa il protocollo AMQPS per comunicare con l'hub IoT.
* **ProcessDeviceToCloudMessages**, che usa la classe [EventProcessorHost] per recuperare i messaggi dall'endpoint compatibile con Hub eventi e quindi archiviare in modo affidabile i messaggi di punti dati in un BLOB di Azure e per inoltrare i messaggi interattivi a una coda del bus di servizio.
* **ProcessD2CInteractiveMessages**, che rimuove i messaggi interattivi dalla coda del bus di servizio.

> [AZURE.NOTE] L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Fare riferimento al [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e, in generale, su come connettere dispositivi a un hub IoT.

Il contenuto di questa esercitazione è direttamente applicabile ad altri modi di usare i messaggi compatibili con Hub eventi, ad esempio i progetti [HDInsight (Hadoop)]. Per altre informazioni, vedere [Guida per gli sviluppatori dell'hub IoT di Azure - Dispositivo a cloud].

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessaria una conoscenza di base dell'[Archiviazione di Azure] e del [bus di servizio di Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **inizio** per i progetti **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Premere **F5** per avviare le tre applicazioni console. L'applicazione **ProcessD2CInteractiveMessages** deve elaborare tutti i messaggi interattivi inviati dall'applicazione **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel file BLOB, potrebbe essere necessario ridurre la costante **MAX\_BLOCK\_SIZE** nella classe **StoreEventProcessor** a un valore inferiore, ad esempio **1024**. Questo perché raggiungere il limite di dimensione del blocco con i dati inviati dal dispositivo simulato può richiedere tempo. Con una dimensione del blocco ridotta, la creazione e l'aggiornamento del BLOB richiedono meno tempo. Tuttavia, una dimensione del blocco maggiore rende l'applicazione più scalabile.

## Passaggi successivi

Questa esercitazione ha illustrato come elaborare in modo affidabile i messaggi di punti dati e interattivi da dispositivo a cloud con la classe [EventProcessorHost].

L'esercitazione sul [caricamento di file da dispositivi] si basa su questa esercitazione e fa uso di una logica di elaborazione dei messaggi analoga. Descrive anche un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file da dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Archivio BLOB di Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[coda del bus di servizio]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guida per gli sviluppatori dell'hub IoT di Azure - Dispositivo a cloud]: iot-hub-devguide.md#d2c

[Archiviazione di Azure]: https://azure.microsoft.com/documentation/services/storage/
[bus di servizio di Azure]: https://azure.microsoft.com/documentation/services/service-bus/



[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0504_2016-->