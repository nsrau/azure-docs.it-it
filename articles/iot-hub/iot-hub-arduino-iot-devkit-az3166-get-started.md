---
title: Connettere DevKit AZ3166 a un hub di Azure
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 3cdeecd5b7698274b899832d7a66a5572a114e4b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954507"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Include una lavagna compatibile con Arduino con periferiche e sensori avanzati, un pacchetto lavagna open source e una [raccolta di esempi](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)avanzata.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come connettere il DevKit Internet delle cose a Wi-Fi e configurare la stringa di connessione dell'hub Internet.
* Come inviare i dati di telemetria del sensore DevKit all'hub Internet.
* Come preparare l'ambiente di sviluppo e sviluppare un'applicazione per le DevKit.

Non hai ancora DevKit? Provare il [simulatore di DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) o [acquistare DevKit](https://aka.ms/iot-devkit-purchase).

È possibile trovare il codice sorgente per tutte le esercitazioni di DevKit dalla [raccolta di esempi di codice](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Elementi necessari

* Una lavagna MXChip DevKit con un cavo micro-USB. [Ottienila ora](https://aka.ms/iot-devkit-purchase).
* Un computer che esegue Windows 10, macOS 10.10 + o Ubuntu 18.04 +.
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Guida introduttiva: inviare dati di telemetria da DevKit a un hub Internet

La Guida introduttiva usa il firmware DevKit precompilato per inviare i dati di telemetria all'hub Internet delle cose. Prima di eseguirlo, è necessario creare un hub Internet e registrare un dispositivo con l'hub.

### <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire il comando seguente in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire questo segnaposto con il nome scelto per l'hub IoT.

   **MyNodeDevice**: il nome del dispositivo che si sta registrando. Usare **MyNodeDevice** come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se viene ricevuto un errore durante l'esecuzione di `device-identity`, installare l' [estensione Azure per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) della riga di comando di Azure per altri dettagli.
  
1. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire questo segnaposto con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

### <a name="send-devkit-telemetry"></a>Inviare dati di telemetria DevKit

Il DevKit si connette a un endpoint specifico del dispositivo nell'hub Internet e invia dati di telemetria relativi a temperatura e umidità.

1. Scaricare la versione più recente del [firmware getstarted](https://aka.ms/devkit/prod/getstarted/latest) per le cose devkit.

1. Verificare che DevKit sia connesso al computer tramite USB. Aprire Esplora file è disponibile un dispositivo di archiviazione di massa USB denominato **AZ3166**.

    ![Apri Esplora risorse](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Trascinare il firmware appena scaricato nel dispositivo di archiviazione di massa e lampeggerà automaticamente.

    ![Copia firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. In DevKit, tenendo premuto il pulsante **b**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il pulsante **b**. Il DevKit accede alla modalità AP. Per confermare, la schermata Visualizza l'identificatore del set di servizi (SSID) del DevKit e l'indirizzo IP del portale di configurazione.

    ![Pulsante di reimpostazione, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Impostare la modalità AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Usare un Web browser in un altro dispositivo abilitato per la connessione Wi-Fi (computer o telefono cellulare) per connettersi al SSID DevKit del tutto visualizzato nel passaggio precedente. Se viene richiesta una password, lasciarla vuota.

    ![Connetti SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Aprire **192.168.0.1** nel browser. Selezionare il Wi-Fi a cui si vuole connettere il DevKit, digitare la password Wi-Fi e quindi incollare la stringa di connessione del dispositivo annotata in precedenza. Fare quindi clic su Salva.

    ![Interfaccia utente di configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Il DevKit è supportato solo dalla rete a 2,4 GHz. Per informazioni più dettagliate controllare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Le informazioni sul Wi-Fi e la stringa di connessione del dispositivo verranno archiviate in DevKit quando viene visualizzata la pagina dei risultati.

    ![Risultato della configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato.

1. Il numero di DevKit viene riavviato in pochi secondi. Nella schermata DevKit si noterà che l'indirizzo IP per il DevKit segue i dati di telemetria, inclusi il valore di temperatura e umidità con il numero di messaggi inviati all'hub di Azure.

    ![IP WiFi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Invio di dati](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Per verificare i dati di telemetria inviati ad Azure, eseguire il comando seguente in Azure Cloud Shell:

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per preparare l'ambiente di sviluppo per DevKit, seguire questa procedura:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installare Visual Studio Code con il pacchetto di estensione strumenti di Azure

1. Installare l'[IDE di Arduino](https://www.arduino.cc/en/Main/Software). Fornisce la toolchain necessaria per la compilazione e il caricamento del codice Arduino.
    * **Windows**: usare la versione di Windows Installer. Non installare dall'App Store.
    * **macOS**: trascinare il file **Arduino.app** estratto nella cartella `/Applications`.
    * **Ubuntu**: decomprimere il file nella cartella come `$HOME/Downloads/arduino-1.8.8`

2. Installa [Visual Studio Code](https://code.visualstudio.com/), un editor di codice sorgente multipiattaforma con funzionalità avanzate di IntelliSense, il completamento del codice e il supporto per il debug, oltre a estensioni avanzate che possono essere installate dal Marketplace.

3. Avviare Visual Studio Code, cercare **Arduino** nel marketplace delle estensioni e installare l'estensione. Questa estensione offre esperienze ottimizzate per lo sviluppo su piattaforma Arduino.

    ![Installare Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Cercare [Azure IoT Tools](https://aka.ms/azure-iot-tools) nel marketplace delle estensioni e installare l'estensione.

    ![Installare Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    In alternativa, usare questo collegamento diretto:
    > [!div class="nextstepaction"]
    > [Installare il pacchetto di estensione degli strumenti di Azure.](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Il pacchetto di estensione strumenti di Azure per gli strumenti di Azure include il [Workbench per dispositivi Azure](https://aka.ms/iot-workbench) , usato per sviluppare ed eseguire il debug in diversi dispositivi devkit. Il [Toolkit Azure](https://aka.ms/iot-toolkit)per gli hub di Azure, incluso anche nel pacchetto di estensione strumenti di Azure, viene usato per gestire e interagire con hub Azure.

5. Configurare Visual Studio Code con le impostazioni di Arduino.

    In Visual Studio Code fare clic su **File > preferenze > impostazioni** (in MacOS, **codice > Preferenze > Impostazioni**). Fare quindi clic sull'icona **Open Settings (JSON)** nell'angolo superiore destro della pagina *Settings (impostazioni* ).

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

6. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Arduino: Board Manager** (Arduino: Gestione lavagna). Cercare **AZ3166** e installare la versione più recente.

    ![Installare DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installare i driver ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) è l'interfaccia USB che IoT DevKit usa per comunicare con la macchina di sviluppo. È necessario installarlo in Windows per eseguire il flashing del codice del dispositivo compilato in DevKit. Seguire i passaggi specifici del sistema operativo per consentire alla macchina l'accesso al dispositivo.

* **Windows**: scaricare e installare i driver USB dal [sito Web STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: nessun driver necessario per macOS.
* **Ubuntu**: eseguire i comandi nel terminale e disconnettersi e accedere per rendere effettive le modifiche apportate al gruppo:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

A questo punto tutto pronto con la preparazione e la configurazione dell'ambiente di sviluppo. Creiamo l'esempio getstarted appena eseguito.

## <a name="build-your-first-project"></a>Creare il primo progetto

### <a name="open-sample-code-from-sample-gallery"></a>Aprire il codice di esempio dalla raccolta di esempi

Il DevKit Internet delle cose contiene una raccolta completa di esempi che è possibile usare per imparare a connettere DevKit a diversi servizi di Azure.

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Azure Azure per dispositivi Workbench: aprire esempi..** .. Selezionare quindi **DevKit** come lavagna.

1. Nella pagina di esempi di Workbench IoT, individuare **Get Started**(Introduzione) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.

    ![Apri esempio](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Eseguire il provisioning dell'hub e del dispositivo di Azure

Anziché eseguire il provisioning dell'hub e del dispositivo di Azure dall'portale di Azure, è possibile farlo nel VS Code senza uscire dall'ambiente di sviluppo.

1. Nella finestra nuovo progetto aperto, fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare Azure Internet per **dispositivi Workbench: provisioning dei servizi di Azure...** . Seguire la guida dettagliata per completare il provisioning dell'hub Azure e la creazione del dispositivo hub Internet.

    ![Comando di provisioning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se non è stato effettuato l'accesso ad Azure. Seguire la notifica popup per l'accesso.

1. selezionare la sottoscrizione da usare.

    ![Seleziona Sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Quindi selezionare o creare un nuovo [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Selezionare il gruppo di risorse](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Nel gruppo di risorse specificato, seguire la guida per selezionare o creare un nuovo hub Azure.

    ![Selezionare i passaggi dell'hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Seleziona hub Internet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub tutto selezionato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Nella finestra output si noterà che è stato effettuato il provisioning dell'hub Azure.

    ![Provisioning dell'hub Internet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Consente di selezionare o creare un nuovo dispositivo nell'hub Azure Internet con provisioning.

    ![Selezionare i passaggi del dispositivo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selezionare il dispositivo di cui è stato effettuato il provisioning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. A questo punto è stato effettuato il provisioning dell'hub Azure e il dispositivo è stato creato. Inoltre, la stringa di connessione del dispositivo verrà salvata in VS Code per la configurazione di DevKit in un secondo momento.

    ![Provisioning completato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurare e compilare il codice del dispositivo

1. Nella barra di stato in basso a destra verificare che **MXCHIP AZ3166** sia visualizzato come scheda selezionata e che venga usata la porta seriale con **STMicroelectronics**.

    ![Selezionare lavagna e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Azure Internet Azure per dispositivi Workbench: configurare le impostazioni del dispositivo...** e quindi selezionare la **stringa di connessione del dispositivo di configurazione > selezionare la stringa di connessione del dispositivo dell'hub Internet**.

1. In DevKit, tenendo premuto il pulsante **a**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante a**. Il DevKit entra in modalità di configurazione e salva la stringa di connessione.

    ![Stringa di connessione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Fare di nuovo clic su `F1`, digitare e selezionare **Azure Azure per dispositivi Workbench: caricare il codice del dispositivo**. Vengono avviati la compilazione e il caricamento nel codice nel DevKit.

    ![Caricamento Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

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

    ![portale di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi annotare la stringa di connessione dell'hub IoT.

    ![Stringa di connessione dell'hub Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. In VS Code fare clic su `F1`, digitare e selezionare **Hub Azure Internet: impostare la stringa di connessione dell'hub Internet**. Copiare la stringa di connessione nella casella.

    ![Impostare la stringa di connessione dell'hub Internet Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Espandere il riquadro **dispositivi dell'hub Azure** , a destra, fare clic con il pulsante destro del mouse sul nome del dispositivo creato e scegliere **Avvia monitoraggio endpoint evento predefinito**.

    ![Monitoraggio del messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Nel riquadro **OUTPUT** è possibile visualizzare i messaggi da dispositivo a cloud in ingresso nell'hub IoT.

    ![Messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Esaminare il codice

Il `GetStarted.ino` è il file di sketch Arduino principale.

![Messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Per vedere come vengono inviati i dati di telemetria del dispositivo all'hub Azure, aprire il file `utility.cpp` nella stessa cartella. Vedere informazioni di [riferimento sulle API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) per informazioni su come usare i sensori e le periferiche nei devkit.

Il `DevKitMQTTClient` usato è un wrapper della **iothub_client** dagli [SDK e dalle librerie di Microsoft Azure Internet per](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interagire con l'hub Azure.

## <a name="problems-and-feedback"></a>Problemi e commenti

Se si riscontrano problemi, è possibile cercare una soluzione nelle [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o prendere contatto con noi tramite [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
