<properties
	pageTitle="Introduzione all'hub IoT di Azure per Node.js | Microsoft Azure"
	description="Esercitazione introduttiva all'hub IoT di Azure con Node.js. Usare l'hub IoT di Azure e Node.js con gli SDK IoT di Microsoft Azure per implementare una soluzione Internet delle cose."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introduzione all'hub IoT di Azure per Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduzione

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT (Internet delle cose) e un back-end della soluzione. Una delle maggiori difficoltà con i progetti IoT consiste nel connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. Per affrontare questa sfida, l'hub IoT:

- Offre messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
- Rende possibili comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
- Comprende librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questa esercitazione illustra come:

- Usare il portale di Azure per creare un hub IoT.
- Creare un'identità del dispositivo nell'hub IoT.
- Creare un dispositivo simulato che invia dati di telemetria al back-end cloud.

Al termine di questa esercitazione si avranno tre applicazioni console di Node.js:

* **CreateDeviceIdentity.js**, che crea un'identità del dispositivo e la chiave di sicurezza associata per connettere il dispositivo simulato.
* **ReadDeviceToCloudMessages.js**, che consente di visualizzare i dati di telemetria inviati dal dispositivo simulato.
* **SimulatedDevice.js**, che si connette all'hub IoT con l'identità del dispositivo creata in precedenza e invia un messaggio di telemetria ogni secondo usando il protocollo AMQPS.

> [AZURE.NOTE] L'articolo [SDK Hub IoT][lnk-hub-sdks] fornisce informazioni sui vari SDK che consentono di compilare entrambe le applicazioni da eseguire nei dispositivi e nel backend della soluzione.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

+ Node. js 0.12.x o versione successiva. <br/> L'articolo relativo alla [preparazione dell'ambiente di sviluppo][lnk-dev-setup] descrive come installare Node.js per questa esercitazione in Windows o Linux.

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Infine, fare clic su **Impostazioni** nel pannello dell'hub IoT e quindi su **Messaggistica** nel pannello **Impostazioni**. Nel pannello **Messaggistica** prendere nota del **Nome compatibile con l'hub eventi** e dell'**Endpoint compatibile con l'hub eventi**. Questi valori serviranno quando si creerà l'applicazione **read-d2c-messages**.

    ![][6]

È stato creato l'hub IoT e sono disponibili il nome host dell'hub IoT, la stringa di connessione dell'hub IoT, il nome compatibile con l'hub eventi e l'endpoint compatibile con l'hub eventi, valori necessari per completare il resto dell'esercitazione.

[AZURE.INCLUDE [iot-hub-get-started-cloud-node](../../includes/iot-hub-get-started-cloud-node.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-node](../../includes/iot-hub-get-started-device-node.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. Eseguire questo comando al prompt dei comandi nella cartella **readdevicetocloudmessages** per iniziare il monitoraggio dell'hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Eseguire questo comando al prompt dei comandi nella cartella **simulateddevice** per iniziare a inviare i dati di telemetria all'hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. Il riquadro **Utilizzo** nel [portale di Azure][lnk-portal] mostra il numero di messaggi inviati all'hub:

    ![][43]

## Passaggi successivi

In questa esercitazione si è configurato un nuovo hub IoT nel portale e quindi è stata creata un'identità del dispositivo nel registro delle identità dell'hub. Questa identità del dispositivo è stata usata per consentire all'app del dispositivo simulato di inviare all'hub messaggi da dispositivo a cloud ed è stata creata un'app che visualizza i messaggi ricevuti dall'hub. È possibile continuare a esplorare le funzionalità dell'hub IoT e altri scenari IoT nelle esercitazioni seguenti:

- [Inviare messaggi da cloud a dispositivo con l'hub IoT][lnk-c2d-tutorial] illustra come inviare messaggi ai dispositivi ed elaborare i commenti sul recapito generati dall'hub IoT.
- [Elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-process-d2c-tutorial] illustra come elaborare in modo affidabile dati di telemetria e messaggi interattivi provenienti dai dispositivi.
- [Caricare file dai dispositivi al cloud con l'hub IoT][lnk-upload-tutorial] descrive un modello che usa i messaggi da cloud a dispositivo per facilitare il caricamento di file dai dispositivi.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0413_2016-->