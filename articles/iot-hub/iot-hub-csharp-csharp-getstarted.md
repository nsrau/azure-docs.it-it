<properties
	pageTitle="Introduzione all'hub IoT | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare l'hub IoT di Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Introduzione all'hub IoT

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni. Una delle maggiori difficoltà con i progetti IoT è quella di connettere in modo affidabile e sicuro i dispositivi al back-end applicazioni. Per semplificare questo scenario, l'hub IoT di Azure offre messaggistica da dispositivo a cloud e da cloud a dispositivo affidabile su scala elevatissima, consente comunicazioni sicure con credenziali di sicurezza e controllo di accesso per ogni dispositivo e include librerie di dispositivi per i linguaggi e le piattaforme più comuni.

Questa esercitazione illustra non solo come usare il portale di Azure per creare un hub IoT, ma anche come creare un'identità di dispositivo nell'hub IoT e come creare un dispositivo simulato che invia messaggi da dispositivo a cloud e riceve questi messaggi dal back-end cloud.

Al termine di questa esercitazione risulteranno create tre applicazioni console Windows:

* **CreateDeviceIdentity**, che crea un'identità di dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato
* **ReadDeviceToCloudMessages**, che legge i messaggi da dispositivo a cloud e ne visualizza il contenuto
* **SimulatedDevice**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio da dispositivo a cloud ogni secondo

> [AZURE.NOTE]L'hub IoT dispone del supporto SDK per molte piattaforme e linguaggi (inclusi C, Java e Javascript) tramite gli SDK del dispositivo Azure IoT. Consultare il [Centro per sviluppatori Azure IoT] per istruzioni dettagliate su come connettere il dispositivo al codice dell'esercitazione e in generale all'hub IoT di Azure. Gli SDK del servizio Azure IoT per Java e Node saranno presto disponibili.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank").

## Creare un hub IoT

1. Accedere al [portale di anteprima di Azure].

2. Nell'indice fare clic su **Nuovo**, quindi su **Internet delle cose** e infine su **Hub IoT**.

   	![][1]

3. Nel pannello **Nuovo hub IoT** specificare la configurazione desiderata per l'hub IoT.

   	![][2]

    * Nella casella **Nome** immettere un nome per identificare l'hub IoT. Quando il **Nome** viene convalidato, appare un segno di spunta verde nella casella **Nome**.
    * Modificare il valore di **Livello di prezzo e di scalabilità** come si preferisce. Per questa esercitazione non è necessario un livello specifico.
    * Nella casella **Gruppo di risorse** creare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](resource-group-portal.md).
    * Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'hub IoT.  


4. Dopo aver configurato le opzioni del nuovo hub IoT, fare clic su **Crea**. La creazione dell'hub IoT può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale o dalla sezione Notifiche.

    ![][3]


5. Al termine della creazione dell'hub IoT, aprire il pannello del nuovo hub IoT, prendere nota dell'URI e selezionare l'icona **Chiave** in alto.

   	![][4]

6. Selezionare il criterio di accesso condiviso **iothubowner**, quindi copiare e trascrivere la stringa di connessione nel pannello destro.

   	![][5]

L'hub IoT è stato creato e l'URI e la stringa di connessione necessari per completare questa esercitazione sono ora disponibili.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare l'azione di **avvio** per le app **ProcessDeviceToCloudMessages** e **SimulatedDevice**.

   	![][41]

2.	Premere **F5**. Entrambe le applicazioni verranno avviate e i messaggi verranno inviati dall'app simulata e ricevuti dall'app processore.

   	![][42]

## Passaggi successivi

In questa esercitazione è stato configurato un nuovo hub IoT, è stata creata un'identità di dispositivo nel registro delle identità dell'hub e questa identità è stata usata per programmare un dispositivo simulato che invia messaggi da dispositivo a cloud. È possibile continuare a esplorare le funzionalità e lo scenario dell'hub IoT con le esercitazioni seguenti:

- [Inviare messaggi da cloud a dispositivo con l'hub IoT] illustra come inviare messaggi ai dispositivi ed elaborare il feedback di recapito generato dall'hub IoT.
- [Elaborare messaggi da dispositivo a cloud] illustra come elaborare in modo affidabile telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricamento di file da dispositivi] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

Altre informazioni sull'hub IoT:

* [Panoramica dell'hub IoT]
* [Guida per sviluppatori di hub IoT]
* [Linee guida dell'hub IoT]
* [Piattaforme e linguaggi di dispositivi supportati][Supported devices]
* [Centro per sviluppatori Azure IoT]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[portale di anteprima di Azure]: https://portal.azure.com/

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: iot-hub-csharp-csharp-c2d.md
[Elaborare messaggi da dispositivo a cloud]: iot-hub-csharp-csharp-process-d2c.md
[Caricamento di file da dispositivi]: iot-hub-csharp-csharp-file-upload.md

[Panoramica dell'hub IoT]: iot-hub-what-is-iot-hub.md
[Linee guida dell'hub IoT]: iot-hub-guidance.md
[Guida per sviluppatori di hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro per sviluppatori Azure IoT]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->