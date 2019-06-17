---
title: DevKit di IoT al cloud -- Connettere DevKit di IoT AZ3166 all'hub IoT di Azure | Microsoft Docs
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 2f86b74299b5d47a87ed0b8e89a992f0f91a84be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924642"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Include una scheda compatibile con Arduino con periferiche avanzate e sensori, un pacchetto della scheda open source e un rich [raccolta di esempi](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come connettere il DevKit di IoT al Wi-Fi e configurare la stringa di connessione dell'IoT Hub.
* Come inviare i dati di telemetria dal sensore DevKit all'hub IoT.
* Come preparare l'ambiente di sviluppo e sviluppare applicazioni per il DevKit di IoT.

Non hai ancora DevKit? Provare il [simulatore di DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) o [acquistare DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-need"></a>Elementi necessari

* Una scheda DevKit di IoT MXChip con un cavo USB Micro. [Ottienila ora](https://aka.ms/iot-devkit-purchase).
* Un computer che eseguono Windows 10, 18.04 + Ubuntu o macOS 10.10 +.
* Una sottoscrizione di Azure attiva. [Attivare una versione di valutazione gratuita di 30 giorni dell'account Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Avvio rapido: Inviare dati di telemetria a un IoT Hub DevKit

Uso della pre-compilate del firmware DevKit per inviare i dati di telemetria all'IoT Hub. Prima dell'esecuzione, si crea un hub IoT e registrare un dispositivo con l'hub.

### <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyNodeDevice**: nome del dispositivo da registrare. Usare **MyNodeDevice** come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > Se si verifica un errore durante l'esecuzione `device-identity`, installare il [l'estensione Azure IOT per Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) per altri dettagli.
  
1. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

### <a name="send-devkit-telemetry"></a>Inviare dati di telemetria di DevKit

Il DevKit si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria su temperatura e umidità.

1. Scaricare la versione più recente di [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) per DevKit di IoT.

1. Assicurarsi che DevKit di IoT di connettersi al computer tramite USB. Aprire Esplora File è un dispositivo di archiviazione di massa USB chiamato **AZ3166**.
    ![Aprire Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Verrà automaticamente lampeggiante trascinare e rilasciare il firmware appena scaricato nel dispositivo di archiviazione di massa e.
    ![Copiare il firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Il devkit, premuto il pulsante **B**, premere e rilasciare il **Reimposta** pulsante e quindi rilasciare il pulsante **B**. Il DevKit passerà alla modalità AP. Per confermare, la schermata Visualizza l'identificatore del set di servizi (SSID) del DevKit e l'indirizzo IP del portale di configurazione.
    ![Reimposta pulsante, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Impostare la modalità AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Usare un Web browser in un diverso Wi-Fi abilitato dispositivi (computer o telefono cellulare) per connettersi al SSID del DevKit di IoT visualizzato nel passaggio precedente. Se richiede una password, lasciare il campo vuoto.
    ![Connettere SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Aprire **192.168.0.1** nel browser. Selezionare il Wi-Fi che si desidera connettere il DevKit di IoT per digitare la password Wi-Fi e quindi incollare la stringa di connessione sono state nota in precedenza di. Fare quindi clic su Salva.
    ![Configurazione dell'interfaccia utente](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Il DevKit di IoT supporta solo la rete a 2,4 GHz. Per informazioni più dettagliate controllare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. La stringa di connessione Wi-Fi informazioni e dispositivi sarà archiviata nel DevKit di IoT quando viene visualizzata la pagina di risultati.
    ![Risultato configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato.

1. IoT DevKit si riavvia in pochi secondi. Nella schermata di DevKit, vengono visualizzati l'indirizzo IP per il DevKit segue in base ai dati di telemetria inclusi temperatura e umidità valore con numero di messaggi di invio all'IoT Hub di Azure.
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Invio di dati](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Seguire questi passaggi per preparare l'ambiente di sviluppo del devkit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installare Visual Studio Code con il pacchetto di estensione strumenti di Azure IoT

1. Installare l'[IDE di Arduino](https://www.arduino.cc/en/Main/Software). Fornisce la toolchain necessaria per la compilazione e il caricamento del codice Arduino.
    * **Windows**: usare la versione di Windows Installer. Non viene installata da Store l'App.
    * **macOS**: trascinare il file **Arduino.app** estratto nella cartella `/Applications`.
    * **Ubuntu**: decomprimere il file nella cartella come `$HOME/Downloads/arduino-1.8.8`

2. Installare [Visual Studio Code](https://code.visualstudio.com/), un editor di codice sorgente cross-platform con funzionalità avanzate di intellisense, completamento del codice e debug di supporto, nonché estensioni avanzate può essere installato dal marketplace.

3. Avviare Visual Studio Code, cercare **Arduino** nel marketplace delle estensioni e installare l'estensione. Questa estensione offre esperienze ottimizzate per lo sviluppo su piattaforma Arduino.
    ![Installare Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Cercare [Azure IoT Tools](https://aka.ms/azure-iot-tools) nel marketplace delle estensioni e installare l'estensione.
    ![Installare Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    In alternativa, usare questo collegamento diretto:
    > [!div class="nextstepaction"]
    > [Installare il pacchetto di estensioni di strumenti di Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Il pacchetto di estensione strumenti di IoT di Azure contiene le [Workbench di Azure IoT Device](https://aka.ms/iot-workbench) che consente di sviluppare ed eseguire il debug nei vari dispositivi devkit di IoT. Il [Hub IoT di Azure Toolkit](https://aka.ms/iot-toolkit), incluso anche con il pacchetto di estensione strumenti di IoT di Azure, viene usato per gestire e interagire con l'hub IoT di Azure.

5. Configurare Visual Studio Code con le impostazioni di Arduino.

    In Visual Studio Code fare clic su **File > Preferenze > Impostazioni**. Quindi fare clic su di **...** e **Apri settings.json**.
    ![Installare Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Aggiungere le seguenti righe per configurare Arduino a seconda della piattaforma: 

    * **Windows**:
      
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

6. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Arduino: Board Manager**. Cercare **AZ3166** e installare la versione più recente.
    ![Installare l’SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installare i driver ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) è l'interfaccia USB che IoT DevKit usa per comunicare con la macchina di sviluppo. È necessario installarlo in Windows per eseguire il codice del dispositivo compilate nel devkit. Seguire i passaggi specifici del sistema operativo per consentire alla macchina l'accesso al dispositivo.

* **Windows**: Scaricare e installare i driver USB fornito dal [sito Web STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) oppure [collegamento diretto](https://aka.ms/stlink-v2-windows).
* **macOS**: non è necessario alcun driver per macOS.
* **Ubuntu**: Eseguire i comandi nel terminale e disconnettersi e accedere rendere effettiva la modifica gruppo:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

A questo punto tutto pronto con la preparazione e la configurazione dell'ambiente di sviluppo. Invia i tuoi commenti compilare l'esempio GetStarted che sono stati eseguiti.

## <a name="build-your-first-project"></a>Creare il primo progetto

### <a name="open-sample-code-from-sample-gallery"></a>Aprire il codice di esempio dalla raccolta di esempi

IoT DevKit contiene una completa raccolta di esempi che è possibile usare per informazioni su connettere il DevKit per vari servizi di Azure.

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples** (Azure IoT Device Workbench: Apri esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Nella pagina di esempi di Workbench IoT, individuare **Get Started**(Introduzione) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.
    ![Aprire esempio](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Effettuare il provisioning dell'IoT Hub di Azure e dispositivo

Invece di provisioning dell'IoT Hub di Azure e dispositivo dal portale di Azure, è possibile farlo in Visual Studio Code senza uscire dall'ambiente di sviluppo.

1. Nella nuova finestra del progetto aperta premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Provision Azure Services** (Azure IoT Device Workbench: Effettua il provisioning dei servizi di Azure). Seguire la guida passo passo per completare il provisioning dell'hub IoT di Azure e creare il dispositivo dell'hub IoT.
    ![Comando effettuare il provisioning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se non è stato firmato in Azure. Seguire la notifica popup per l'accesso.

1. selezionare la sottoscrizione da usare.
    ![Sub SELECT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Quindi selezionare o creare una nuova [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Selezionare gruppo di risorse](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Nel gruppo di risorse specificato, seguire la Guida per selezionare o creare un nuovo IoT Hub di Azure.
    ![Selezionare i passi dell'IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selezionare l'Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selezionato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Nella finestra di output, verrà visualizzato l'IoT Hub di Azure il provisioning ![effettuato il provisioning dell'Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selezionare o creare un nuovo dispositivo nell'IoT Hub di Azure è stato effettuato il provisioning.
    ![Selezionare questa procedura per dispositivi IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selezionare il provisioning per dispositivi IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Dopo aver eseguito il provisioning dell'IoT Hub di Azure e dispositivo creato in esso. La stringa di connessione verrà salvata anche in Visual Studio Code per configurare il DevKit di IoT in un secondo momento.
    ![Effettuare il provisioning eseguito](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurare e compilare il codice di dispositivo

1. Nella barra di stato in basso a destra verificare che **MXCHIP AZ3166** sia visualizzato come scheda selezionata e che venga usata la porta seriale con **STMicroelectronics**.
    ![Selezionare lavagna e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Configure Device Settings** (Azure IoT Device Workbench: Configura impostazioni del dispositivo) e quindi selezionare **Config Device Connection String > Select IoT Hub Device Connection String** (Configura stringa di connessione del dispositivo > Seleziona stringa di connessione del dispositivo hub IoT).

1. Su DevKit, tenere premuto il **pulsante A**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante A**. DevKit passa alla modalità di configurazione e salva la stringa di connessione.
    ![Stringa di connessione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Premere nuovamente `F1`, digitare e selezionare **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Carica il codice del dispositivo). Il comando avvia la compilazione e il caricamento del codice nel DevKit.
    ![Caricamento di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> In caso di errori o interruzioni, è sempre possibile ripristinare il sistema eseguendo nuovamente il comando.

## <a name="test-the-project"></a>Verificare il progetto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visualizzare i dati di telemetria inviati all'hub IoT di Azure

Fare clic sull'icona del plug di accensione sulla barra di stato per aprire Serial Monitor: ![Monitoraggio seriale](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

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

1. In Visual Studio Code fare clic su `F1`, digitare e selezionare **Azure IoT Hub: Set IoT Hub Connection String** (Hub IoT di Azure: Imposta la stringa di connessione dell'hub IoT). Copiare la stringa di connessione nella casella.
    ![Imposta stringa di connessione hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Espandere il riquadro **AZURE IOT HUB DEVICES** (DISPOSITIVI HUB IOT DI AZURE) a destra, fare clic con il pulsante destro del mouse sul nome del dispositivo creato e scegliere **Start Monitoring D2C Message** (Avvia il monitoraggio del messaggio D2C).
    ![Monitorare il messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Nel riquadro **OUTPUT** è possibile visualizzare i messaggi da dispositivo a cloud in ingresso nell'hub IoT.
    ![Messaggi da dispositivo a cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Esaminare il codice

Il `GetStarted.ino` file sketch Arduino principale.

![Messaggi D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Per visualizzare la telemetria del dispositivo viene inviato all'IoT Hub di Azure, aprire il `utility.cpp` file nella stessa cartella. Vista [riferimento all'API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) per imparare a usare IoT DevKit sensori e periferiche.

Il `DevKitMQTTClient` utilizzato è un wrapper del **iothub_client** dal [librerie per C e Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) per interagire con l'IoT Hub di Azure.

## <a name="problems-and-feedback"></a>Problemi e commenti

Se si riscontrano problemi, è possibile cercare una soluzione nelle [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o prendere contatto con noi tramite [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
