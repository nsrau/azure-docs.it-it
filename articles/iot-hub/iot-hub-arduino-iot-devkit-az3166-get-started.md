---
title: DevKit di IoT al cloud -- Connettere DevKit di IoT AZ3166 all'hub IoT di Azure | Microsoft Docs
description: Questa esercitazione descrive come configurare e connettere DevKit di IoT AZ3166 all'hub IoT di Azure in modo che invii i dati alla piattaforma cloud di Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 6ecddefd264bf4a6f57dd7fcd09c3a8cc10ec54a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377311"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Connettere DevKit di IoT AZ3166 all'hub IoT di Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

È possibile usare il [DevKit di IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure. Il DevKit include una scheda compatibile con Arduino con periferiche e sensori avanzati, un pacchetto della scheda open source e un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) sempre in crescita.

## <a name="what-you-do"></a>Operazioni da fare

Connettere il [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) a un hub IoT di Azure creato, raccogliere i dati sulla temperatura e l'umidità rilevati dai sensori e inviarli all'hub IoT.

Non hai ancora DevKit? Provare il [simulatore di DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) o [acquistare DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come connettere il DevKit di IoT al punto di accesso wireless e preparare l'ambiente di sviluppo.
* Come creare un hub IoT e registrare un dispositivo per il DevKit di IoT MXChip.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio nel DevKit di IoT MXChip.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

* Una scheda DevKit di IoT MXChip con un cavo USB micro. [Ottienila ora](https://aka.ms/iot-devkit-purchase).
* Un computer che esegue Windows 10 o macOS 10.10+.
* Una sottoscrizione di Azure attiva. [Attivare una versione di valutazione gratuita di 30 giorni dell'account Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  
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

## <a name="configure-wi-fi"></a>Configurazione Wi-Fi

I progetti IoT si basano sulla connettività Internet. Usare le istruzioni seguenti per configurare il DevKit per la connessione al Wi-Fi.

### <a name="enter-ap-mode"></a>Passare alla modalità AP

Tenere premuto il pulsante B, premere e rilasciare il pulsante di reimpostazione e quindi rilasciare il pulsante B. Il DevKit passerà alla modalità AP per la configurazione del Wi-Fi. La schermata visualizza l'identificatore SSID del DevKit e l'indirizzo IP del portale di configurazione.

![Pulsante di reimpostazione, pulsante B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Eseguire la connessione a DevKit AP

Usare a questo punto un altro dispositivo abilitato per il Wi-Fi (computer o telefono cellulare) per connettersi al SSID del DevKit (evidenziato nell'immagine precedente). Lasciare vuoto il campo della password.

![Informazioni di rete e pulsante Connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurare il Wi-Fi per il DevKit

Aprire l'indirizzo IP mostrato nella schermata del DevKit nel browser del computer o del telefono cellulare, selezionare la rete Wi-Fi a cui si desidera connettere il DevKit e quindi digitare la password. Selezionare **Connessione**.

![Campo della password e pulsante Connect (Connetti)](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dopo avere stabilito la connessione, il DevKit si riavvia in pochi secondi. Verranno visualizzati il nome della rete Wi-Fi e l'indirizzo IP:

![Nome della rete Wi-Fi e indirizzo IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L'indirizzo IP visualizzato nella foto potrebbe non corrispondere all'indirizzo IP effettivo assegnato e visualizzato nella schermata di DevKit. Si tratta di un comportamento normale perché il Wi-Fi usa il protocollo DHCP che assegna gli indirizzi IP in modo dinamico.

Dopo che la configurazione del Wi-Fi è completata, le credenziali usate verranno mantenute nel dispositivo per tale connessione, anche se il dispositivo viene scollegato. Se, ad esempio, si configura il DevKit per il Wi-Fi di casa e poi lo si sposta in ufficio, sarà necessario riconfigurare la modalità AP (a partire dal passaggio "Passare alla modalità AP") per connettere il DevKit alla rete Wi-Fi dell'ufficio. 

## <a name="start-using-the-devkit"></a>Iniziare a usare il DevKit

L'app predefinita in esecuzione nel DevKit controlla se è installata la versione più recente del firmware e visualizza alcuni dati di diagnostica dei sensori.

### <a name="upgrade-to-the-latest-firmware"></a>Eseguire l'aggiornamento al firmware più recente

> [!NOTE]
> A partire dalla versione 1.1 DevKit abilita ST-SAFE nel bootloader. È necessario eseguire l'aggiornamento del firmware se si esegue una versione precedente alla 1.1.

Se è necessario aggiornare il firmware, la schermata visualizza la versione del firmware attualmente installata e quella più recente. Per eseguire l'aggiornamento, seguire le istruzioni nella guida [Upgrade firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) (Aggiornare il firmware).

![Visualizzazione delle versioni attuale e più recente del firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Questa operazione viene eseguita una sola volta. Dopo avere iniziato a sviluppare nel DevKit e avere caricato l'app, il firmware più recente sarà presente nell'app.

### <a name="test-various-sensors"></a>Testare i diversi sensori

Premere il pulsante B per testare i sensori. Continuare a premere e rilasciare il pulsante B per passare di sensore in sensore.

![Visualizzazione del pulsante B e del sensore](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

### <a name="install-azure-iot-workbench"></a>Installare Workbench di Azure IoT

Si consiglia di utilizzare l’estensione [Workbench di Azure IoT](https://aka.ms/iot-workbench) per lo sviluppo di Visual Studio Code su DevKit.

Workbench di Azure IoT offre un'esperienza integrata per lo sviluppo di soluzioni IoT. Contribuisce allo sviluppo sia suo dispositivi che su cloud utilizzando Azure IoT e altri servizi. È possibile guardare questo video di Channel9 per avere una panoramica delle funzionalità.

Seguire questa procedura per preparare l'ambiente di sviluppo per DevKit:

1. Scaricare e installare [Arduino IDE](https://www.arduino.cc/en/Main/Software). Fornisce la toolchain necessaria per la compilazione e il caricamento del codice Arduino.
    * **Windows**: usare la versione di Windows Installer.
    * **macOS**: trascinare il file **Arduino.app** estratto nella cartella `/Applications`.
    * **Ubuntu**: decomprimere il file nella cartella come `$HOME/Downloads/arduino-1.8.5`

1. Installare [Visual Studio Code](https://code.visualstudio.com/), un editor di codice sorgente multipiattaforma con potenti strumenti per gli sviluppatori, come il completamento e il debug del codice IntelliSense.

1. Cercare **Workbench di Azure IoT** nel marketplace delle estensioni e installarlo.
    ![Installare Workbench di Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png): insieme ad Azure IoT Workbench, verranno installati altre estensioni dipendenti.

1. Aprire **File > Preferenze > Impostazioni** e aggiungere le righe successive per configurare Arduino.
    * **Windows**:
    ```javascript
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **macOS**:
    ```javascript
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **Ubuntu**:
    ```javascript
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

1. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **Arduino: Board Manager** (Arduino: Gestione lavagna). Cercare **AZ3166** e installare la versione più recente.
    ![Installare l’SDK DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Installare i driver ST-Link

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) è l'interfaccia USB che IoT DevKit usa per comunicare con la macchina di sviluppo. Seguire i passaggi specifici del sistema operativo per consentire alla macchina l'accesso al dispositivo.

* **Windows**: scaricare e installare i driver USB dal [sito Web STMicroelectronics](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: nessun driver necessario per macOS.
* **Ubuntu**: eseguire il comando seguente nel terminale e disconnettersi e riconnettersi affinché la modifica di gruppo abbia effetto:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

A questo punto tutto pronto con la preparazione e la configurazione dell'ambiente di sviluppo. Compiliamo l’esempio "Hello World" per IoT: inviamo i dati di telemetria della temperatura all'hub IoT di Azure.

## <a name="build-your-first-project"></a>Creare il primo progetto

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Nella barra di stato in basso a destra, verificare che **MXCHIP AZ3166** sia visualizzato come scheda selezionata e che sia utilizzata la porta seriale con **STMicroelectronics**.
    ![Selezionare lavagna e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **IoT Workbench: Examples** (Workbench IoT: Esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Nella pagina di esempi di Workbench IoT, individuare **Get Started**(Introduzione) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.
    ![Aprire esempio](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Se l'estensione di Arduino non è installata in Visual Studio Code, fare clic su **Installa** nel riquadro di notifica.
    ![Installare l'estensione di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. Nella nuova finestra del progetto aperta, fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **IoT Workbench: Cloud** (Workbench IoT: cloud), quindi selezionare **Azure Provision** (Provisioning di Azure). Seguire la guida passo passo per completare il provisioning dell'hub IoT di Azure e creare il dispositivo.
    ![Provisioning cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Fare clic su `F1` per aprire il riquadro comandi, digitare e selezionare **IoT Workbench: Device** (Workbench IoT: dispositivo), quindi selezionare **Config Device Settings > Select IoT Hub Device Connection String** (Configurare le impostazioni del dispositivo > Selezionare Stinga di connessione dispositivo hub IoT).

1. Su DevKit, tenere premuto il **pulsante A**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante A**. DevKit passa alla modalità di configurazione e salva la stringa di connessione.
    ![Stringa di connessione](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Fare clic di nuovo su `F1`, digitare e selezionare **IoT Workbench: Device** (Workbench IoT: dispositivo), quindi selezionare **Device Upload** (Caricamento dispositivo).
    ![Caricamento di Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

> [!NOTE]
> In caso di errori o interruzioni, è sempre possibile ripristinare il sistema eseguendo nuovamente il comando.

## <a name="test-the-project"></a>Verificare il progetto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visualizzare i dati di telemetria inviati all'hub IoT di Azure

Fare clic sull'icona della spina di alimentazione sulla barra di stato per aprire il monitor seriale: ![Serial monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png) (Monitor seriale)

L'applicazione di esempio viene eseguita correttamente quando vengono visualizzati i risultati seguenti:

* Il monitor seriale mostra il messaggio inviato all'hub IoT.
* Il LED sul DevKit di IoT MXChip lampeggia.

![Output di monitoraggio seriale](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visualizzare i dati di telemetria ricevuti dall'hub IoT di Azure

È possibile usare il [Toolkit Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per il monitoraggio dei messaggi da dispositivo a cloud (D2C) nell'hob IoT.

1. In Visual Studio Code, cercare **Toolkit di Azure IoT** nel marketplace delle estensioni e installarlo.

1. Accedere al [portale di Azure](https://portal.azure.com/) e cercare l'IoT Hub creato.
    ![portale di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi annotare la stringa di connessione dell'hub IoT.
    ![Stringa di connessione dell'hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. In Visual Studio Code, espandere **AZURE IOT HUB DEVICES** (DISPOSITIVI HUB IOT AZURE) nell'angolo inferiore sinistro e fare clic su **Set IoT Hub Connection String** (Imposta stringa di connessione hub IoT).
    ![Imposta stringa di connessione hub IoT di Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Fare clic su **IoT: Start monitoring D2C message** (IoT: Avvia monitoraggio messaggio da dispositivo a cloud) nel menu di scelta rapida.

1. Nel riquadro **OUTPUT** è possibile visualizzare i messaggi da dispositivo a cloud in ingresso nell'hub IoT.
    ![Messaggi da dispositivo a cloud](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

Se si riscontrano problemi, è possibile cercare una soluzione nelle [domande frequenti di IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o prendere contatto con noi tramite [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). È possibile anche possibile lasciare commenti e suggerimenti personali in questa pagina.

## <a name="next-steps"></a>Passaggi successivi

La scheda DevKit di IoT MXChip è stata a questo punto connessa all'hub IoT e i dati acquisiti dai sensori sono stati inviati all'hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
