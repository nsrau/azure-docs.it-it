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
     ms.date="01/05/2016"
     ms.author="dobett"/>

# Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Altre esercitazioni, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT], illustrano come usare le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT.

Questa esercitazione è basata sul codice mostrato nell'esercitazione [Introduzione all'hub IoT] e illustra due modelli scalabili che è possibile usare per elaborare i messaggi da dispositivo a cloud:

- L'archiviazione affidabile dei messaggi da dispositivo a cloud nell'[Archivio BLOB di Azure]. Questo scenario è molto comune quando si implementa l'analisi dei *percorsi non critici*, in cui i dati archiviati nei BLOB vengono usati come input per processi di analisi basata su strumenti come [Data factory di Azure] o lo stack [HDInsight (Hadoop)].

- L'elaborazione affidabile di messaggi da dispositivo a cloud *interattivi*. I messaggi da dispositivo a cloud sono detti interattivi quando sono trigger immediati per un set di azioni nel back-end dell'applicazione, contrariamente ai messaggi di *punti dati* che vengono inseriti in un motore di analisi. Ad esempio, un avviso proveniente da un dispositivo che deve attivare l'inserimento di un ticket in un sistema CRM è un messaggio da dispositivo a cloud interattivo, mentre i dati di telemetria come i campioni di temperatura rappresentano un messaggio di punti dati.

Dal momento che l'hub IoT espone un endpoint compatibile con Hub eventi per ricevere i messaggi da dispositivo a cloud, questa esercitazione usa un'istanza di [EventProcessorHost] che:

* Archivia in modo affidabile i messaggi di *punti dati* nei BLOB di Azure.
* Inoltra i messaggi da dispositivo a cloud *interattivi* a una [coda del bus di servizio] per l'elaborazione immediata.

Il bus di servizio è un ottimo modo per assicurare un'elaborazione affidabile dei messaggi interattivi, perché fornisce checkpoint per ogni messaggio e la deduplicazione basata su finestre temporali.

Al termine di questa esercitazione si eseguiranno tre applicazioni console Windows:

* **SimulatedDevice**, una versione modificata dell'app creata nell'esercitazione [Introduzione all'hub IoT], che invia messaggi da dispositivo a cloud di punti dati ogni secondo e messaggi da dispositivo a cloud interattivi ogni 10 secondi.
* **ProcessDeviceToCloudMessages**, che usa la classe [EventProcessorHost] per recuperare i messaggi dall'endpoint compatibile con Hub eventi e quindi archiviare in modo affidabile i messaggi di punti dati in un BLOB di Azure e per inoltrare i messaggi interattivi a una coda del bus di servizio.
* **ProcessD2CInteractiveMessages**, che rimuove i messaggi interattivi dalla coda del bus di servizio.

> [AZURE.NOTE] L'hub IoT offre il supporto SDK per molte piattaforme e linguaggi, inclusi C, Java e JavaScript. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e, in generale, come connettere dispositivi all'hub IoT di Azure.

Questa esercitazione è direttamente applicabile ad altri modi di utilizzare i messaggi compatibili con Hub eventi, ad esempio i progetti [HDInsight (Hadoop)]. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT di Azure, sezione Dispositivo a cloud].

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

È richiesta una conoscenza di base di [Archiviazione di Azure] e del [bus di servizio di Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per i progetti **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Premere **F5** per avviare le tre applicazioni console. L'applicazione **ProcessD2CInteractiveMessages** deve elaborare tutti i messaggi interattivi inviati dall'applicazione **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel file BLOB, potrebbe essere necessario ridurre la costante **MAX\_BLOCK\_SIZE** nella classe **StoreEventProcessor** a un valore inferiore, ad esempio **1024**. Questo perché raggiungere il limite di dimensione del blocco con i dati inviati dal dispositivo simulato può richiedere tempo. Con una dimensione del blocco ridotta, la creazione e l'aggiornamento del BLOB richiedono meno tempo. Tuttavia, una dimensione del blocco maggiore rende l'applicazione più scalabile.

## Passaggi successivi

In questa esercitazione si è appreso come elaborare in modo affidabile i messaggi da dispositivo a cloud di punti dati e interattivi con la classe [EventProcessorHost].

L'[esercitazione sul caricamento di file da dispositivi] si basa su questa esercitazione e fa uso della logica di elaborazione dei messaggi analoghi. L'esercitazione descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file da dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Archivio BLOB di Azure]: https://azure.microsoft.com/it-IT/documentation/articles/storage-dotnet-how-to-use-blobs/
[Data factory di Azure]: https://azure.microsoft.com/it-IT/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/it-IT/documentation/services/hdinsight/
[coda del bus di servizio]: https://azure.microsoft.com/it-IT/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx


[Gestione degli errori temporanei]: https://msdn.microsoft.com/it-IT/library/hh680901(v=pandp.50).aspx

[Guida per gli sviluppatori dell'hub IoT di Azure, sezione Dispositivo a cloud]: https://azure.microsoft.com/it-IT/documentation/articles/iot-hub-devguide/#d2c

[Archiviazione di Azure]: https://azure.microsoft.com/it-IT/documentation/services/storage/
[bus di servizio di Azure]: https://azure.microsoft.com/it-IT/documentation/services/service-bus/



[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[esercitazione sul caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[Introduzione all'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: https://azure.microsoft.com/develop/iot

<!---HONumber=AcomDC_0204_2016-->