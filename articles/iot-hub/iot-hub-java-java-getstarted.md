<properties
	pageTitle="Introduzione all'hub IoT di Azure | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare l'hub IoT di Azure con Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/21/2015"
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
- Creare un dispositivo simulato che invia la telemetria al back-end cloud.

Al termine di questa esercitazione si avranno tre applicazioni console Java:

* **create-device-identity**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **read-d2c-messages**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **simulated-device**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo.

> [AZURE.NOTE]L'articolo [IoT Hub SDK][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel back-end della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Java SE 8. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Java per questa esercitazione in Windows o Linux.

+ Maven 3. <br/> [Preparare l'ambiente di sviluppo][lnk-dev-setup] descrive come installare Maven per questa esercitazione in Windows o Linux.

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

7. Fare clic su **Impostazioni** nel pannello dell'hub IoT, quindi su **Messaggistica** nel pannello **Impostazioni**. Nel pannello **Messaggistica** prendere nota del **Nome compatibile con l'hub eventi** e dell'**Endpoint compatibile con l'hub eventi**. Questi valori serviranno quando si creerà l'applicazione **read-d2c-messages**.

    ![][6]

Si è appena creato l'hub IoT e si conoscono il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, il nome compatibile con l'hub eventi e l'endpoint compatibile con l'hub eventi necessari per completare il resto dell'esercitazione.

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Al prompt dei comandi nella cartella read-d2c eseguire il comando seguente per iniziare a monitorare l'hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. Al prompt dei comandi nella cartella simulated-device eseguire il comando seguente per iniziare a inviare i dati di telemetria all'hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

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
[1]: ./media/iot-hub-java-java-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-java-java-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-java-java-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-java-java-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-java-java-getstarted/create-iot-hub5.png
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
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

<!---HONumber=AcomDC_0114_2016-->