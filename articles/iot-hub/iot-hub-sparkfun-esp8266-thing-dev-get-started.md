---
title: Da ESP8266 al cloud - Connettere Sparkfun ESP8266 Thing Dev all'hub IoT di Azure | Microsoft Docs
description: Informazioni su come configurare e connettere Sparkfun ESP8266 Thing Dev all'hub IoT in modo che invii i dati alla piattaforma cloud di Azure in questa esercitazione.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Connettere Sparkfun ESP8266 Thing Dev all'hub IoT di Azure nel cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![connessione tra DHT22, Thing Dev e hub IoT](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

Connettere Sparkfun ESP8266 Thing Dev a un hub IoT che l'utente creerà. Quindi eseguire un'applicazione di esempio in ESP8266 per raccogliere dati di temperatura e umidità da un sensore DHT22. Infine inviare i dati del sensore all'hub IoT.

> [!NOTE]
> Se si usano altre schede ESP8266, è comunque possibile seguire questi passaggi per connetterle all'hub IoT. A seconda della scheda ESP8266 in uso, potrebbe essere necessario riconfigurare il `LED_PIN`. Ad esempio, se si usa ESP8266 da AI-Thinker, è possibile modificarlo da `0` a `2`. Non si dispone ancora di un kit? Fare clic [qui](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione

* Come creare un hub IoT e registrare un dispositivo per Thing Dev.
* Come connettere Thing Dev con il sensore e il computer.
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Thing Dev.
* Come inviare i dati del sensore all'hub IoT.

## <a name="what-you-will-need"></a>Prerequisiti

![Parti necessarie per l'esercitazione](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Per completare questa operazione, è necessario disporre dei componenti seguenti dello starter kit di Thing Dev:

* La scheda Sparkfun ESP8266 Thing Dev.
* Un cavo USB Micro/tipo A.

Per l'ambiente di sviluppo sono necessari anche gli elementi seguenti:

* Una sottoscrizione di Azure attiva. Se non si ha un account di Azure, [creare un account di Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Mac o PC che esegue Windows o Ubuntu.
* Rete wireless per Sparkfun ESP8266 Thing Dev a cui connettersi.
* Connessione Internet per scaricare lo strumento di configurazione.
* [IDE di Arduino](https://www.arduino.cc/en/main/software) versione 1.6.8 (o successive), le versioni precedenti non funzioneranno con la libreria AzureIoT.

Gli elementi seguenti sono facoltativi nel caso in cui non si disponga di un sensore. È possibile anche usare dati di sensori simulati.

* Un sensore di temperatura e umidità Adafruit DHT22.
* Una basetta sperimentale.
* Cavi ponticello M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Connettere ESP8266 Thing Dev con il sensore e il computer

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Connettere un sensore di temperatura e umidità DHT22 a ESP8266 Thing Dev

Usare la basetta sperimentale e i cavi ponticello per stabilire la connessione come indicato di seguito. Se non si dispone di un sensore, ignorare questa sezione in quanto è possibile usare i dati di sensori simulati.

![Riferimento per le connessioni](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Per i pin dei sensori verranno usati i collegamenti seguenti:

| Inizio (sensore)           | Fine (scheda)           | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3V (Pin 8A)           | Cavo rosso     |
| DATA (Pin 28F)           | GPIO 2 (Pin 9A)       | Cavo bianco    |
| GND (Pin 30F)            | GND (Pin 7J)          | Cavo nero   |


- Per altre informazioni, vedere: [installazione del sensore DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) e [specifiche di Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711)

Ora Sparkfun ESP8266 Thing Dev è connesso con un sensore funzionante.

![collegare DHT22 con ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Collegare Sparkfun ESP8266 Thing Dev al computer

Usare il cavo USB Micro/tipo A per connettere Sparkfun ESP8266 Thing Dev al computer come segue.

![Connettere Feather HUZZAH al computer](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Aggiungere le autorizzazioni per la porta seriale: solo in Ubuntu

Se si usa Ubuntu, assicurarsi che un normale utente abbia le autorizzazioni per operare sulla porta USB di Sparkfun ESP8266 Thing Dev. Per aggiungere autorizzazioni sulla porta seriale per un utente normale, seguire questi passaggi:

1. Eseguire i comandi seguenti in un terminale:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Si ottiene uno degli output seguenti:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Nell'output si osservi `uucp` o `dialout` che rappresenta il nome del proprietario gruppo della porta USB.

1. Aggiungere l'utente al gruppo eseguendo il comando seguente:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` è il nome del proprietario gruppo ottenuto nel passaggio precedente. `<username>` è il nome utente di Ubuntu.

1. Eseguire la disconnessione da Ubuntu e quindi un nuovo accesso per rendere effettive le modifiche.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Raccogliere i dati del sensore e inviarli all'hub IoT

In questa sezione si distribuisce e si esegue un'applicazione di esempio in Sparkfun ESP8266 Thing Dev. L'applicazione di esempio fa lampeggiare il LED in Sparkfun ESP8266 Thing Dev e invia i dati di temperatura e umidità raccolti dal sensore DHT22 all'hub IoT.

### <a name="get-the-sample-application-from-github"></a>Ottenere l'applicazione di esempio da Github

L'applicazione di esempio è ospitata in GitHub. Clonare il repository di esempio che contiene l'applicazione di esempio da GitHub. Per clonare il repository di esempio, seguire questi passaggi:

1. Aprire un prompt dei comandi o una finestra del terminale.
1. Passare alla cartella in cui archiviare l'applicazione di esempio.
1. Eseguire il comando seguente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Installare il pacchetto per Sparkfun ESP8266 Thing Dev nell'IDE di Arduino:

1. Aprire la cartella in cui è archiviata l'applicazione di esempio.
1. Aprire il file app.ino nella cartella dell'app nell'IDE di Arduino.

   ![Aprire l'applicazione di esempio nell'IDE di Arduino](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Nell'IDE di Arduino fare clic su **File** > **Preferenze**.
1. Nella finestra di dialogo **Preferenze** fare clic sull'icona accanto alla casella di testo **Additional Boards Manager URLs** (URL di gestione bacheche aggiuntivo).
1. Nella finestra a comparsa immettere l'URL seguente e quindi fare clic su **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Puntare all'URL di un pacchetto nell'IDE di Arduino](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. Nella finestra di dialogo **Preferenza** fare clic su **OK**.
1. Fare clic su **Strumenti** > **Bacheca** > **Boards Manager** (Gestione bacheche) e quindi cercare esp8266.
   Deve essere installato ESP8266 versione 2.2.0 o successiva.

   ![Il pacchetto esp8266 è installato](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Fare clic su **Strumenti** > **Scheda** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Installare le librerie necessarie

1. Nell'IDE di Arduino fare clic su **Schizzo** > **Include Library** (Includi libreria)  > **Gestisci librerie**.
1. Cercare i seguenti nomi di libreria uno alla volta. Per ciascuna delle librerie individuate fare clic su **Installa**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Non si dispone di un sensore DHT22 reale?

L'applicazione di esempio consente di simulare i dati di temperatura e umidità nel caso non si disponga di un sensore DHT22 reale. Per abilitare l'applicazione di esempio all'uso di dati simulati, seguire questi passaggi:

1. Aprire il file `config.h` nella cartella `app`.
1. Individuare la seguente riga di codice e modificare il valore da `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurare l'applicazione di esempio per l'uso di dati simulati](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Salvare con `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Distribuire l'applicazione di esempio in Sparkfun ESP8266 Thing Dev

1. Nell'IDE di Arduino fare clic su **Strumento** > **Porta** e quindi fare clic sulla porta seriale di Sparkfun ESP8266 Thing Dev.
1. Fare clic su **Schizzo** > **Carica** per compilare e distribuire l'applicazione di esempio in Sparkfun ESP8266 Thing Dev.

> [!Note]
> Se si usa macOS è possibile visualizzare i messaggi seguenti durante il caricamento. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Aprire la finestra del terminal e completare le azioni riportate di seguito per risolvere questo problema.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Immettere le credenziali

Dopo aver completato il caricamento, seguire i passaggi per immettere le credenziali:

1. Nell'IDE di Arduino fare clic su **Strumenti** > **Serial Monitor** (Monitoraggio seriale).
1. Nella finestra del monitoraggio seriale notare i due elenchi a discesa nella parte inferiore destra.
1. Selezionare **No line ending** (Senza terminazione di riga) per la casella di riepilogo a discesa a sinistra.
1. Selezionare **115200 baud** per la casella di riepilogo a discesa a destra.
1. Nella casella di input nella parte superiore della finestra del monitoraggio seriale immettere le informazioni seguenti, se viene richiesto di fornirle, e quindi fare clic su **Invia**.
   * Wi-Fi SSID
   * Password Wi-Fi
   * Stringa di connessione del dispositivo

> [!Note]
> Le informazioni sulle credenziali sono archiviate in EEPROM di Sparkfun ESP8266 Thing Dev. Se si fa clic sul pulsante di reset della scheda di Sparkfun ESP8266 Thing Dev, l'applicazione di esempio chiede se si desidera cancellare le informazioni. Immettere `Y` per cancellare le informazioni e verrà chiesto di fornirle nuovamente.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificare che l'applicazione di esempio venga eseguita correttamente

Se si vede il seguente output dalla finestra di monitoraggio seriale e il LED di Sparkfun ESP8266 Thing Dev lampeggia, significa che l'applicazione di esempio viene eseguita correttamente.

![Output finale nell'IDE di Arduino](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passaggi successivi

Sparkfun ESP8266 Thing Dev si è connessa correttamente all'hub IoT e i dati acquisiti dal sensore sono stati inviati all'hub IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
