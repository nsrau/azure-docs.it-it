---
title: Connettere DevKit di IoT AZ3166 a un hub IoT di Azure
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: d5147d6d7f1bd1fcd43835f6a66196c7642ad4ff
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151947"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Il DevKit include una scheda compatibile con Arduino con periferiche e sensori avanzati, un pacchetto della scheda open source e una [raccolta di esempi](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) avanzata.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come connettere il DevKit IoT al Wi-Fi e configurare la stringa di connessione all'hub IoT.
* Come inviare i dati di telemetria del sensore DevKit all'hub IoT.
* Come preparare l'ambiente di sviluppo e sviluppare un'applicazione per il DevKit IoT.

Non hai ancora DevKit? Provare il [simulatore di DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) o [acquistare DevKit](https://aka.ms/iot-devkit-purchase).

È possibile trovare il codice sorgente per tutte le esercitazioni di DevKit dalla [raccolta di esempi di codice](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Elementi necessari

* Una scheda DevKit IoT MXChip con un cavo USB micro. [Ottienila ora](https://aka.ms/iot-devkit-purchase).
* Un computer che esegue Windows 10, macOS 10.10+ o Ubuntu 18.04+.
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Guida introduttiva: Inviare dati di telemetria da DevKit a un hub IoT

L'avvio rapido usa il firmware DevKit precompilato per inviare i dati di telemetria all'hub IoT. Prima di eseguirlo, creare un hub IoT e registrare un dispositivo con l'hub.

### <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyNodeDevice**: nome del dispositivo da registrare. Usare **MyNodeDevice** come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se si riceve un errore durante l'esecuzione di `device-identity`, installare l'[estensione Azure IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici dell'hub IoT, di IoT Edge e del servizio Device Provisioning (DPS) IoT all'interfaccia della riga di comando di Azure.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

### <a name="send-devkit-telemetry"></a>Inviare dati di telemetria DevKit

Il DevKit si connette a un endpoint specifico del dispositivo nell'hub IoT e invia dati di telemetria relativi a temperatura e umidità.

1. Scaricare la versione più recente del [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) per DevKit IoT.

1. Verificare che DevKit IoT sia connesso al computer tramite USB. Aprire Esplora file e verificare se è presente un dispositivo di archiviazione di massa USB denominato **AZ3166**.

    ![Aprire Esplora risorse](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Trascinare e rilasciare il firmware appena scaricato nel dispositivo di archiviazione di massa che lampeggerà automaticamente.

    ![Copiare il firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Sul DevKit, tenere premuto il pulsante **B**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il pulsante **B**. Il DevKit entra nella modalità AP. Per conferma, la schermata visualizza l'identificatore SSID del DevKit e l'indirizzo IP del portale di configurazione.

    ![Pulsante di reimpostazione, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Impostare la modalità AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Usare un Web browser su un altro dispositivo abilitato per il Wi-Fi (computer o telefono cellulare) per connettersi al SSID del DevKit IoT (mostrato nel passaggio precedente). Se viene richiesta una password, lasciare il campo vuoto.

    ![Connettere l'SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Aprire **192.168.0.1** nel browser. Selezionare il Wi-Fi a cui si vuole connettere il DevKit IoT, digitare la password del Wi-Fi e quindi incollare la stringa di connessione del dispositivo annotata in precedenza. Fare quindi clic su Salva.

    ![Interfaccia utente di configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Il DevKit IoT è supportato solo dalla rete a 2,4 GHz. Per informazioni più dettagliate controllare le [Domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Le informazioni sul Wi-Fi e la stringa di connessione del dispositivo verranno archiviate nel DevKit IoT quando viene visualizzata la pagina dei risultati.

    ![Risultati di configurazione](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato.

1. Il DevKit IoT viene riavviato in pochi secondi. Nella schermata DevKit si noterà che l'indirizzo IP per il DevKit segue i dati di telemetria, inclusi il valore di temperatura e umidità con il numero di messaggi inviati all'hub IoT di Azure.

    ![IP WiFi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Invio di dati](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Per verificare i dati di telemetria inviati ad Azure, eseguire il comando seguente in Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Seguire questa procedura per preparare l'ambiente di sviluppo per il DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installare Visual Studio Code con il pacchetto dell'estensione Azure IoT Tools

1. Installare l'[IDE di Arduino](https://www.arduino.cc/en/Main/Software). Fornisce la toolchain necessaria per la compilazione e il caricamento del codice Arduino.
    * **Windows**: usare la versione di Windows Installer. Non installarlo dall'App Store.
    * **macOS**: trascinare il file **Arduino.app** estratto nella cartella `/Applications`.
    * **Ubuntu**: decomprimere il file nella cartella come `$HOME/Downloads/arduino-1.8.8`

2. Installare [Visual Studio Code](https://code.visualstudio.com/), un editor di codice sorgente multipiattaforma con il potente supporto del completamento e del debug del codice IntelliSense, nonché estensioni avanzate che possono essere installate dal marketplace.

3. Avviare Visual Studio Code, cercare **Arduino** nel marketplace delle estensioni e installare l'estensione. Questa estensione offre esperienze ottimizzate per lo sviluppo su piattaforma Arduino.

    ![Installare Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Cercare [Azure IoT Tools](https://aka.ms/azure-iot-tools) nel marketplace delle estensioni e installare l'estensione.

    ![Screenshot che Mostra gli strumenti di Azure Internet nel Marketplace di estensione.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    In alternativa, copiare e incollare l'URL in una finestra del browser: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Il pacchetto di estensione Azure IoT Tools contiene la [Azure IoT Device Workbench](https://aka.ms/iot-workbench) che consente di sviluppare ed eseguire il debug su vari dispositivi Devkit IoT. L'[estensione dell'hub IoT di Azure](https://aka.ms/iot-toolkit), disponibile anche nel pacchetto di estensione Azure IoT Tools, viene usata per gestire e interagire con l'hub IoT di Azure.

5. Configurare Visual Studio Code con le impostazioni di Arduino.

    In Visual Studio Code fare clic su **File > Preferenze > Impostazioni** (in macOS, **Codice > Preferenze > Impostazioni**). Fare quindi clic sull'icona **Apri impostazioni (JSON)** nell'angolo in alto a destra della pagina *Impostazioni*.

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

    ![Installare l'SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installare i driver ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) è l'interfaccia USB che IoT DevKit usa per comunicare con la macchina di sviluppo. È necessario installarlo in Windows per eseguire il flashing del codice del dispositivo compilato nel DevKit. Seguire i passaggi specifici del sistema operativo per consentire alla macchina l'accesso al dispositivo.

* **Windows**: scaricare e installare i driver USB dal [sito Web STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: non è necessario alcun driver per macOS.
* **Ubuntu**: eseguire i comandi nel terminale e quindi disconnettersi e riconnettersi affinché la modifica di gruppo abbia effetto:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

A questo punto tutto pronto con la preparazione e la configurazione dell'ambiente di sviluppo. Creare l'esempio GetStarted appena eseguito.

## <a name="build-your-first-project"></a>Creare il primo progetto

### <a name="open-sample-code-from-sample-gallery"></a>Aprire il codice di esempio dalla raccolta di esempi

Il DevKit IoT contiene una raccolta completa di esempi che è possibile usare per imparare a connettere il DevKit a diversi servizi di Azure.

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples** (Azure IoT Device Workbench: Apri esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Nella pagina di esempi di Workbench IoT, individuare **Get Started**(Introduzione) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.

    ![Aprire esempio](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Effettuare il provisioning dell'hub IoT di Azure e del dispositivo

Anziché effettuare il provisioning dell'hub IoT di Azure e del dispositivo dal portale di Azure, è possibile farlo nel VS Code senza uscire dall'ambiente di sviluppo.

1. Nella nuova finestra del progetto aperta premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Provision Azure Services** (Azure IoT Device Workbench: Effettua il provisioning dei servizi di Azure). Seguire la guida passo passo per completare il provisioning dell'hub IoT di Azure e creare il dispositivo dell'hub IoT.

    ![Comando di provisioning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se non è stato effettuato l'accesso ad Azure. Seguire la notifica popup per l'accesso.

1. selezionare la sottoscrizione da usare.

    ![Selezionare sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Quindi selezionare un [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) o crearne uno nuovo.

    ![Selezionare il gruppo di risorse](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Nel gruppo di risorse specificato, seguire la guida per selezionare o creare un nuovo hub IoT di Azure.

    ![Passaggi di selezione dell'hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selezionare l'hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selezionato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Nella finestra di output si noterà che è stato effettuato il provisioning dell'hub IoT di Azure.

    ![Hub IoT con provisioning effettuato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selezionare o creare un nuovo dispositivo nell'hub IoT Azure di cui è stato effettuato il provisioning.

    ![Passaggi di selezione del dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selezionare il dispositivo IoT di cui è stato effettuato il provisioning](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. A questo punto è stato effettuato il provisioning dell'hub IoT di Azure e del dispositivo creato al suo interno. Inoltre, la stringa di connessione del dispositivo verrà salvata in VS Code per configurare il DevKit IoT in un secondo momento.

    ![Provisioning effettuato](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurare e compilare il codice del dispositivo

1. Nella barra di stato in basso a destra verificare che **MXCHIP AZ3166** sia visualizzato come scheda selezionata e che venga usata la porta seriale con **STMicroelectronics**.

    ![Selezionare lavagna e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Configure Device Settings** (Azure IoT Device Workbench: Configura impostazioni del dispositivo) e quindi selezionare **Config Device Connection String > Select IoT Hub Device Connection String** (Configura stringa di connessione del dispositivo > Seleziona stringa di connessione del dispositivo hub IoT).

1. Su DevKit, tenere premuto il **pulsante A**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante A**. DevKit passa alla modalità di configurazione e salva la stringa di connessione.

    ![Stringa di connessione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Premere nuovamente `F1`, digitare e selezionare **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Carica il codice del dispositivo). Vengono avviati la compilazione e il caricamento nel codice nel DevKit.

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

> [!NOTE]
> È possibile che si verifichi un errore durante i test in cui il LED non lampeggia, il portale di Azure non visualizza i dati in ingresso dal dispositivo, ma lo schermo OLED del dispositivo viene visualizzato come **In esecuzione...** . Per risolvere il problema, nel portale di Azure andare al dispositivo nell'hub IoT e inviare un messaggio al dispositivo. Se viene visualizzata la risposta seguente nel monitor seriale in VS Code, è possibile che la comunicazione diretta dal dispositivo sia bloccata a livello di router. Controllare le regole del firewall e del router configurate per i dispositivi connessi. Assicurarsi anche che la porta in uscita 1833 sia aperta.
> 
> ERRORE: mqtt_client. c (In 454): Errore: errore durante l'apertura della connessione all'endpoint  
> INFO: >>>Stato della connessione: disconnesso  
> ERRORE: tlsio_mbedtls. c (In 604): Apertura I/O sottostante non riuscita  
> ERRORE: mqtt_client. c (In 1042): Errore: io_open failed  
> Errore: iothubtransport_mqtt_common. c (In 2283): errore durante la connessione all'indirizzo atcsliothub.azure-devices.net.  
> INFO: >>>Riconnettersi.  
> INFO: Versione dell'hub IoT: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visualizzare i dati di telemetria ricevuti dall'hub IoT di Azure

È possibile usare il [Toolkit Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per il monitoraggio dei messaggi da dispositivo a cloud (D2C) nell'hob IoT.

1. Accedere al [portale di Azure](https://portal.azure.com/) e cercare l'hub IoT creato.

    ![Portale di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi annotare la stringa di connessione dell'hub IoT.

    ![Stringa di connessione dell'hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. In Visual Studio Code fare clic su `F1`, digitare e selezionare **Azure IoT Hub: Set IoT Hub Connection String** (Hub IoT di Azure: Imposta la stringa di connessione dell'hub IoT). Copiare la stringa di connessione nella casella.

    ![Impostare la stringa di connessione hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Espandere il riquadro **AZURE IOT HUB DEVICES** (DISPOSITIVI HUB IOT DI AZURE) a sinistra, fare clic con il pulsante destro del mouse sul nome del dispositivo creato e scegliere **Avvia monitoraggio endpoint eventi predefinito**.

    ![Monitorare il messaggio D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Nel riquadro **OUTPUT** è possibile visualizzare i messaggi da dispositivo a cloud in ingresso nell'hub IoT.

    ![Screenshot che mostra i messaggi D2C in ingresso nell'hub Internet.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Esaminare il codice

`GetStarted.ino` è il file schema Arduino principale.

![Messaggio da dispositivo a cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Per vedere come i dati di telemetria del dispositivo sono inviati all'hub IoT di Azure, aprire il file `utility.cpp` nella stessa cartella. Visualizzare [Riferimento API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) per scoprire come usare i sensori e le periferiche su DevKit IoT.

Il `DevKitMQTTClient` usato è un wrapper del **iothub_client** dagli [SDK di Microsoft Azure IoT e librerie per C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) per l'interazione con l'hub IoT di Azure.

## <a name="problems-and-feedback"></a>Problemi e commenti

Se si riscontrano problemi, è possibile cercare una soluzione nelle [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o prendere contatto con noi tramite [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]