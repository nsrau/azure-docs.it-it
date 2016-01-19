<properties
	pageTitle="Introduzione all'hub IoT di Azure | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare l'hub IoT di Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/14/2015"
     ms.author="dobett"/>

# Esercitazione: Introduzione all'hub IoT di Azure

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione. Una delle maggiori difficoltà con i progetti IoT consiste nel connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. Per affrontare questa sfida, l'hub IoT:

- Offre messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
- Rende possibili comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
- Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questa esercitazione illustra come:

- Usare il portale di Azure per creare un hub IoT.
- Creare un'identità del dispositivo nell'hub IoT.
- Creare un dispositivo simulato che invia dati di telemetria al back-end cloud e riceve i comandi dal back-end cloud.

Al termine di questa esercitazione si avranno tre applicazioni console Windows:

* **CreateDeviceIdentity**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **ReadDeviceToCloudMessages**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **SimulatedDevice**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo.

> [AZURE.NOTE]L'articolo [IoT Hub SDK][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel back-end della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2015

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

## Creare un hub IoT

È necessario creare un hub IoT a cui si connette il dispositivo simulato. La procedura seguente illustra come completare questa attività con il portale di Azure.

1. Accedere al [portale di Azure][lnk-portal].

2. Nell'indice fare clic su **Nuovo**, quindi su **Internet of Things** e infine su **Hub IoT di Azure**.

    ![][1]

3. Nel pannello **Hub IoT** scegliere la configurazione per l'hub IoT.

    ![][2]

    * Nella casella **Nome** immettere un nome per l'hub IoT. Se il **Nome** è valido e disponibile, appare un segno di spunta verde nella casella **Nome**.
    * Selezionare un **piano tariffario e un livello di scalabilità**. Per questa esercitazione non è necessario un livello specifico.
    * **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure][lnk-resource-groups].
    * In **Percorso** selezionare il percorso per ospitare l'hub IoT.  

4. Dopo aver scelto le opzioni di configurazione dell'hub IoT, fare clic su **Crea**. La creazione dell'hub IoT da parte di Azure può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale o nel pannello Notifiche.

    ![][3]

5. Al termine della creazione dell'hub IoT, aprire il pannello del nuovo hub IoT, prendere nota del **Nome host** e fare clic sull'icona **Chiavi**.

    ![][4]

6. Fare clic sui criteri **iothubowner**, quindi copiare e prendere nota della stringa di connessione nel pannello **iothubowner**.

    ![][5]

L'hub IoT è stato così creato e ora sono disponibili il nome host e la stringa di connessione necessari per completare il resto di questa esercitazione.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio**. Selezionare **Progetti di avvio multipli**, quindi selezionare **Avvio** come **Azione** per entrambi i progetti**ReadDeviceToCloudMessages** e **SimulatedDevice**.

   	![][41]

2.	Premere **F5** avviare entrambe le app in esecuzione. L'output della console dall'app **SimulatedDevice** mostra i messaggi inviati dal dispositivo simulato all'hub IoT e l'output della console dall'app **ReadDeviceToCloudMessages** mostra i messaggi ricevuti dall'hub IoT.

   	![][42]

## Passaggi successivi

In questa esercitazione si è configurato un nuovo hub IoT nel portale e quindi è stata creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata in un dispositivo simulato che invia all'hub messaggi da dispositivo a cloud ed è stata creata un'altra app che visualizza i messaggi ricevuti dall'hub. È possibile continuare a esplorare le funzionalità dell'hub IoT e altri scenari IoT nelle esercitazioni seguenti:

- [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial] illustra come inviare messaggi ai dispositivi ed elaborare i commenti sul recapito generati dall'hub IoT.
- [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial] illustra come elaborare in modo affidabile dati di telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricare file dai dispositivi al cloud con l'hub IoT][lnk-upload-tutorial] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

È possibile trovare altre informazioni sull'hub IoT negli articoli seguenti:

* [Panoramica dell'hub IoT][lnk-hub-overview]
* [Guida per sviluppatori dell'hub IoT][lnk-hub-dev-guide]
* [Progettare la soluzione hub IoT][lnk-hub-guidance]
* [Piattaforme e linguaggi di dispositivi supportati][lnk-supported-devices]
* [Centro per sviluppatori Azure IoT][lnk-dev-center]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-hub-guidance]: iot-hub-guidance.md
[lnk-hub-dev-guide]: iot-hub-devguide.md
[lnk-supported-devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-dev-center]: http://www.azure.com/develop/iot
[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!----HONumber=AcomDC_0114_2016-->