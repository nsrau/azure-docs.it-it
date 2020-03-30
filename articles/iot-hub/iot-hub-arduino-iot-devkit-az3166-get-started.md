---
title: Collegare l'IoT DevKit A3166 a un hub IoT di AzureConnect IoT DevKit A Az 3166 to an Azure IoT Hub
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 5d6ba2bd52d8f4af4244e8ce1babeb7bc9d37fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235667"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Include una scheda compatibile con Arduino con periferiche e sensori ricchi, un pacchetto di schede open source e una [ricca galleria di esempi.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come connettere l'IoT DevKit a Wi-Fi e configurare la stringa di connessione dell'hub IoT.
* Come inviare i dati di telemetria del sensore DevKit all'hub IoT.
* Come preparare l'ambiente di sviluppo e sviluppare l'applicazione per il DevKit IoT.How to prepare the development environment and develop application for the IoT DevKit.

Non hai ancora DevKit? Provare il [simulatore di DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) o [acquistare DevKit](https://aka.ms/iot-devkit-purchase).

È possibile trovare il codice sorgente per tutte le esercitazioni DevKit dalla [raccolta di esempi](https://docs.microsoft.com/samples/browse/?term=mxchip)di codice .

## <a name="what-you-need"></a>Elementi necessari

* Una scheda MXChip IoT DevKit con cavo Micro-USB. [Scaricalo ora](https://aka.ms/iot-devkit-purchase).
* Un computer che esegue Windows 10, macOS 10.10 o Ubuntu 18.04.
* Una sottoscrizione di Azure attiva. [Attivare un account di valutazione gratuito di 30 giorni](https://azureinfo.microsoft.com/us-freetrial.html)di valutazione di Microsoft Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Preparare l'hardware

Collegare l'hardware seguente al computer:

* Scheda DevKit
* Cavo USB micro

![Requisiti hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Per connettere il DevKit al computer, seguire questa procedura:

1. Collegare l'estremità USB al computer.

2. Collegare l'estremità USB micro al DevKit.

3. Il LED verde dell'alimentazione conferma la connessione.

   ![Connessioni hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Guida introduttiva: Inviare dati di telemetria da DevKit a un hub IoTQuickstart: Send telemetry from DevKit to an IoT Hub

The quickstart uses pre-compiled DevKit firmware to send the telemetry to the IoT Hub. Prima di eseguirlo, creare un hub IoT e registrare un dispositivo con l'hub.

### <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire questo segnaposto con il nome scelto per l'hub IoT.

   **MyNodeDevice**: Il nome del dispositivo che si sta registrando. Usare **MyNodeDevice** come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se viene visualizzato `device-identity`un errore durante l'esecuzione, installare l'estensione [IOT di Azure per l'interfaccia della riga di](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) comando di Azure per altri dettagli.
  
1. Eseguire i comandi seguenti in Azure Cloud Shell per ottenere la stringa di connessione del dispositivo per il dispositivo appena registrato:Run the following commands in Azure Cloud Shell to get the _device connection string_ for the device you just registered:

   **YourIoTHubName**: sostituire questo segnaposto con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

### <a name="send-devkit-telemetry"></a>Inviare dati di telemetria DevKitSend DevKit telemetry

DevKit si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria di temperatura e umidità.

1. Scaricare la versione più recente del [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) per IoT DevKit.

1. Assicurati che IoT DevKit si colleghi al computer tramite USB. Aprire Esplora file c'è un dispositivo di archiviazione di massa USB chiamato **A3166**.

    ![Aprire Esplora risorse](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Trascinare e rilasciare il firmware appena scaricato nel dispositivo di archiviazione di massa e lampeggerà automaticamente.

    ![Copia firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. In DevKit tenere premuto il pulsante **B**, premere e rilasciare il pulsante **Reimposta,** quindi rilasciare il pulsante **B**. DevKit passa alla modalità AP. Per confermare, nella schermata viene visualizzato l'identificatore del set di servizi (SSID) di DevKit e l'indirizzo IP del portale di configurazione.

    ![Pulsante di reimpostazione, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Impostare la modalità AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Utilizzare un browser Web in un dispositivo Wi-Fi abilitato diverso (computer o telefono cellulare) per connettersi all'IoT DevKit SSID visualizzato nel passaggio precedente. Se richiede una password, lasciarla vuota.

    ![Collegare SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Aprire **192.168.0.1** nel browser. Seleziona il Wi-Fi a cui vuoi che l'IoT DevKit si connetta, digita la password Wi-Fi, quindi incolla la stringa di connessione del dispositivo che hai preso nota in precedenza. Fare quindi clic su Salva.

    ![Interfaccia utente di configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > L'IoT DevKit supporta solo una rete a 2,4 GHz. Per informazioni più dettagliate controllare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Le informazioni WiFi e la stringa di connessione del dispositivo verranno archiviate nell'IoT DevKit quando viene visualizzata la pagina dei risultati.

    ![Risultato della configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato.

1. L'IoT DevKit si riavvia in pochi secondi. Nella schermata DevKit viene visualizzato l'indirizzo IP per DevKit seguito dai dati di telemetria, inclusi il valore di temperatura e umidità con il numero di messaggi inviati all'hub IoT di Azure.On the DevKit screen, you see the IP address for the DevKit follows by the telemetry data including temperature and humidity value with message count send to Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Invio di dati](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. To verify the telemetry data sent to Azure, run the following command in Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Seguire questi passaggi per preparare l'ambiente di sviluppo per DevKit:Follow these steps to prepare the development environment for the DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installare il codice di Visual Studio con il pacchetto di estensione degli strumenti IoT di AzureInstall Visual Studio Code with Azure IoT Tools extension package

1. Installare l'[IDE di Arduino](https://www.arduino.cc/en/Main/Software). Fornisce la toolchain necessaria per la compilazione e il caricamento del codice Arduino.
    * **Windows**: usare la versione di Windows Installer. Non installare dall'App Store.
    * **macOS**: trascinare il file **Arduino.app** estratto nella cartella `/Applications`.
    * **Ubuntu**: decomprimere il file nella cartella come `$HOME/Downloads/arduino-1.8.8`

2. Installa [Visual Studio Code](https://code.visualstudio.com/), un editor di codice sorgente multipiattaforma con un potente supporto intellisense, il completamento del codice e il debug, nonché estensioni avanzate che possono essere installate da Marketplace.

3. Avviare Visual Studio Code, cercare **Arduino** nel marketplace delle estensioni e installare l'estensione. Questa estensione offre esperienze ottimizzate per lo sviluppo su piattaforma Arduino.

    ![Installare Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Cercare [Azure IoT Tools](https://aka.ms/azure-iot-tools) nel marketplace delle estensioni e installare l'estensione.

    ![Installare Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Oppure usa questo link diretto:
    > [!div class="nextstepaction"]
    > [Installare il pacchetto di estensione degli strumenti IoT di AzureInstall Azure IoT Tools extension pack](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Il pacchetto di estensione Azure IoT Tools contiene Azure IoT Device Workbench usato per sviluppare ed eseguire il debug in vari dispositivi devkit IoT.The Azure IoT Tools extension pack contains the [Azure IoT Device Workbench](https://aka.ms/iot-workbench) which is used to develop and debug on various IoT devkit devices. [L'estensione dell'hub IoT](https://aka.ms/iot-toolkit)di Azure, inclusa anche nel pacchetto di estensione degli strumenti IoT di Azure, viene usata per gestire e interagire con gli hub IoT di Azure.The Azure IoT Hub extension , also included with the Azure IoT Tools extension pack, is used to manage and interact with Azure IoT Hubs.

5. Configurare Visual Studio Code con le impostazioni di Arduino.

    In Visual Studio Code, fare clic su **File > Preferenze > Impostazioni** (in macOS, Code > Preferenze > **Impostazioni**). Quindi fare clic sull'icona **Apri impostazioni (JSON)** nell'angolo superiore destro della pagina *Impostazioni.*

    ![Installare Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Aggiungere le seguenti righe per configurare Arduino a seconda della piattaforma: 

    * **Finestre di windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Sostituire il segnaposto **{username}** qui di seguito con il proprio nome utente.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Arduino: Board Manager** (Arduino: Gestione lavagna). Cercare **AZ3166** e installare la versione più recente.

    ![Installare l’SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installare i driver ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) è l'interfaccia USB che IoT DevKit usa per comunicare con la macchina di sviluppo. È necessario installarlo in Windows per far lampeggiare il codice del dispositivo compilato nel DevKit.You need to install it on Windows to flash the compiled device code to the DevKit. Seguire i passaggi specifici del sistema operativo per consentire alla macchina l'accesso al dispositivo.

* **Windows**: scaricare e installare i driver USB dal [sito Web STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: nessun driver necessario per macOS.
* **Ubuntu**: Eseguire i comandi nel terminale ed disconnettersi e accedere per rendere effettiva la modifica del gruppo:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

A questo punto tutto pronto con la preparazione e la configurazione dell'ambiente di sviluppo. Costruiamo l'esempio GetStarted appena eseguito.

## <a name="build-your-first-project"></a>Creare il primo progetto

### <a name="open-sample-code-from-sample-gallery"></a>Aprire il codice di esempio dalla raccolta di esempiOpen sample code from sample gallery

Il DevKit IoT contiene una raccolta avanzata di esempi che è possibile usare per apprendere di connettere DevKit a vari servizi di Azure.The IoT DevKit contains a rich gallery of samples that you can use to learn connect the DevKit to various Azure services.

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Fare `F1` clic per aprire la tavolozza dei comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples...**. Quindi selezionare **IoT DevKit** come scheda.

1. Nella pagina di esempi di Workbench IoT, individuare **Get Started**(Introduzione) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.

    ![Aprire esempio](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Effettuare il provisioning dell'hub e del dispositivo Di Azure IoT

Invece di eseguire il provisioning dell'hub ioT di Azure e del dispositivo dal portale di Azure, è possibile farlo nel codice VS senza uscire dall'ambiente di sviluppo.

1. Nella nuova finestra del `F1` progetto aperto fare clic per aprire la tavolozza dei comandi, digitare e selezionare **Azure IoT Device Workbench: Provision Azure Services...**. Seguire la guida dettagliata per completare il provisioning dell'hub IoT di Azure e creare il dispositivo dell'hub IoT.Follow the step by step guide to finish provisioning your Azure IoT Hub and creating the IoT Hub device.

    ![Comando Provision](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se non è stato eseguito l'accesso in Azure.If you have not signed in Azure. Seguire la notifica popup per l'accesso.

1. selezionare la sottoscrizione da usare.

    ![Seleziona sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Selezionare o creare quindi un nuovo [gruppo di risorse.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)

    ![Selezionare il gruppo di risorse](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Nel gruppo di risorse specificato seguire la guida per selezionare o creare un nuovo hub IoT di Azure.In the resource group you specified, follow the guide to select or create a new Azure IoT Hub.

    ![Seleziona i passaggi dell'hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selezionare l'hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selezionato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Nella finestra di output verrà visualizzato il provisioning dell'hub IoT di Azure.In the output window, you will see the Azure IoT Hub provisioned.

    ![Provisioning dell'hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selezionare o creare un nuovo dispositivo nell'hub IoT di Azure di cui è stato eseguito il provisioning.

    ![Selezionare i passaggi del dispositivo IoTSelect IoT Device steps](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selezionare Il dispositivo IoT di cui è stato eseguito il provisioningSelect IoT Device Provisioned](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. A questo punto è stato eseguito il provisioning dell'hub IoT di Azure e il dispositivo creato in esso. Anche la stringa di connessione del dispositivo verrà salvata nel codice VS per configurare l'IoT DevKit in un secondo momento.

    ![Provisioning effettuato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurare e compilare il codice del dispositivoConfigure and compile device code

1. Nella barra di stato in basso a destra verificare che **MXCHIP AZ3166** sia visualizzato come scheda selezionata e che venga usata la porta seriale con **STMicroelectronics**.

    ![Selezionare lavagna e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Fare `F1` clic per aprire la tavolozza dei comandi, digitare e selezionare **Azure IoT Device Workbench: Configure Device Settings...**, quindi selezionare **Config Device Connection String > Select IoT Hub Device Connection String**.

1. In DevKit tenere premuto **il pulsante A**, premere e rilasciare il pulsante **di ripristino,** quindi rilasciare **il pulsante A**. DevKit passa alla modalità di configurazione e salva la stringa di connessione.

    ![Stringa di connessione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Fare `F1` di nuovo clic, digitare e selezionare **Azure IoT Device Workbench: Upload Device Code**. Vengono avviati la compilazione e il caricamento nel codice nel DevKit.

    ![Caricamento di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> In caso di errori o interruzioni, è sempre possibile ripristinare il sistema eseguendo nuovamente il comando.

## <a name="test-the-project"></a>Verificare il progetto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visualizzare i dati di telemetria inviati all'hub IoT di Azure

Fare clic sull'icona del plug di accensione sulla barra di stato per aprire Serial Monitor:

![Monitoraggio seriale](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

L'applicazione di esempio viene eseguita correttamente quando vengono visualizzati i risultati seguenti:

* Il monitor seriale mostra il messaggio inviato all'hub IoT.
* Il LED sul DevKit di IoT MXChip lampeggia.

![Output di monitoraggio seriale](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visualizzare i dati di telemetria ricevuti dall'hub IoT di Azure

È possibile usare il [Toolkit Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per il monitoraggio dei messaggi da dispositivo a cloud (D2C) nell'hob IoT.

1. Accedere al [portale di Azure](https://portal.azure.com/) e cercare l'hub IoT creato.

    ![Portale di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi annotare la stringa di connessione dell'hub IoT.

    ![Stringa di connessione dell'hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. In Codice VS `F1`fare clic su , digitare e selezionare **Hub IoT di Azure: imposta stringa di connessione hub IoT**. Copiare la stringa di connessione nella casella.

    ![Imposta stringa di connessione hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Espandere il riquadro **DISPOSITIVI HUB iOT,** a destra, fare clic con il pulsante destro del mouse sul nome del dispositivo creato e selezionare **Avvia monitoraggio endpoint evento predefinito**.

    ![Monitorare il messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Nel riquadro **OUTPUT** è possibile visualizzare i messaggi da dispositivo a cloud in ingresso nell'hub IoT.

    ![Messaggi da dispositivo a cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Esaminare il codice

Il `GetStarted.ino` è il file di schizzo principale di Arduino.

![Messaggi da dispositivo a cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Per vedere come i dati di telemetria del `utility.cpp` dispositivo vengono inviati all'hub IoT di Azure, aprire il file nella stessa cartella. Visualizza [riferimento api](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) per imparare a usare sensori e periferiche in IoT DevKit.

L'uso `DevKitMQTTClient` è un wrapper del **iothub_client** dagli SDK e dalle librerie IoT di Microsoft Azure per l'interazione di C con l'hub IoT di Azure.The used is a wrapper of the iothub_client from the Microsoft [Azure IoT SdKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) to interact with Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemi e commenti

Se si riscontrano problemi, è possibile cercare una soluzione nelle [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o prendere contatto con noi tramite [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
