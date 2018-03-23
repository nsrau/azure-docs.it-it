---
title: Da ESP8266 al cloud - Connettere Feather HUZZAH ESP8266 ad Azure IoT Hub | Microsoft Docs
description: Informazioni su come configurare e connettere Adafruit Feather HUZZAH ESP8266 all'hub IoT in modo che invii i dati alla piattaforma cloud di Azure in questa esercitazione.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: ''
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: xshi
ms.openlocfilehash: e8f0397797d8f4c3349afcdfb02de071a6593c72
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Connettere Adafruit Feather HUZZAH ESP8266 ad Azure IoT Hub nel cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Connessione tra DHT22, Feather HUZZAH ESP8266 e l'hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Operazioni da fare


Connettere Adafruit Feather HUZZAH ESP8266 a un hub IoT che è stato creato. Eseguire un'applicazione di esempio in ESP8266 per raccogliere dati di temperatura e umidità da un sensore DHT22. Infine inviare i dati del sensore all'hub IoT.

> [!NOTE]
> Se si usano altre schede ESP8266, è comunque possibile seguire questa procedura per connetterle all'hub IoT. A seconda della scheda ESP8266 usata, potrebbe essere necessario riconfigurare il `LED_PIN`. Ad esempio se si usa ESP8266 di AI-Thinker, è possibile modificarne l'impostazione da `0` a `2`. Se non si ha ancora un kit, è possibile ottenerlo nel [sito Web Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per Feather HUZZAH ESP8266
* Come connettere Feather HUZZAH ESP8266 al sensore e al computer
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Feather HUZZAH ESP8266
* Come inviare i dati del sensore all'hub IoT

## <a name="what-you-need"></a>Elementi necessari

![Componenti necessari per l'esercitazione](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Per completare questa operazione è necessario disporre dei componenti seguenti di Feather HUZZAH ESP8266 Starter Kit:

* Scheda Feather HUZZAH ESP8266
* Cavo USB Micro/tipo A

Per l'ambiente di sviluppo sono necessari anche gli elementi seguenti:

* Una sottoscrizione di Azure attiva. Se non si ha un account di Azure, [creare un account di Azure gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* Mac o PC che esegue Windows o Ubuntu.
* Rete wireless a cui Feather HUZZAH ESP8266 deve connettersi.
* Connessione Internet per scaricare lo strumento di configurazione.
* [Estensione di Visual Studio Code per Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Per l'IDE Arduino usato dall'estensione di Visual Studio Code per Arduino è richiesta la versione 1.6.8 o successiva. Le versioni precedenti non funzionano con la libreria AzureIoT.

Gli elementi seguenti sono facoltativi nel caso in cui non si disponga di un sensore. È possibile anche usare dati di sensori simulati.

* Sensore di temperatura e umidità Adafruit DHT22
* Basetta sperimentale
* Cavi ponticello M/M


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Connettere Feather HUZZAH ESP8266 con il sensore e il computer
In questa sezione si connettono i sensori alla scheda. Quindi si collega il dispositivo al computer per poterlo usare.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Connettere un sensore di temperatura e umidità DHT22 a Feather HUZZAH ESP8266

Usare la basetta sperimentale e i cavi ponticello per stabilire la connessione come indicato di seguito. Se non si dispone di un sensore, ignorare questa sezione in quanto è possibile usare i dati di sensori simulati.

![Riferimento per le connessioni](media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)


Per i pin dei sensori usare i collegamenti seguenti:


| Inizio (sensore)           | Fine (scheda)           | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3V (Pin 58H)           | Cavo rosso     |
| DATI (Pin 32F)           | GPIO 2 (Pin 46A)       | Cavo blu    |
| GND (Pin 34F)            | GND (PIn 56I)          | Cavo nero   |

Per altre informazioni, vedere la [configurazione del sensore Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e la [piedinatura di Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Ora Feather Huzzah ESP8266 è connesso con un sensore funzionante.

![Connettere DHT22 a Feather HUZZAH](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Connettere Feather HUZZAH ESP8266 al computer

Usare il cavo USB Micro/tipo A per connettere Feather HUZZAH ESP8266 al computer.

![Connettere Feather HUZZAH al computer](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Aggiungere le autorizzazioni per la porta seriale (solo Ubuntu)


Se si usa Ubuntu, assicurarsi di avere le autorizzazioni per operare sulla porta USB di Feather HUZZAH ESP8266. Per aggiungere autorizzazioni sulla porta seriale, seguire questi passaggi:


1. Eseguire i comandi seguenti in un terminale:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Si ottiene uno degli output seguenti:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Nell'output osservare che `uucp` o `dialout` è il nome del proprietario del gruppo della porta USB.

1. Aggiungere l'utente al gruppo eseguendo il comando seguente:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` è il nome del proprietario gruppo ottenuto nel passaggio precedente. `<username>` è il nome utente di Ubuntu.

1. Disconnettere Ubuntu, quindi accedere di nuovo per visualizzare la modifica.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Raccogliere i dati del sensore e inviarli all'hub IoT

In questa sezione si distribuisce un'applicazione di esempio in Feather HUZZAH ESP8266. L'applicazione di esempio fa lampeggiare il LED sulla scheda Feather HUZZAH ESP8266 e invia i dati di temperatura e umidità raccolti dal sensore DHT22 all'hub IoT.

### <a name="get-the-sample-application-from-github"></a>Ottenere l'applicazione di esempio da Github

L'applicazione di esempio è ospitata in GitHub. Clonare il repository di esempio che contiene l'applicazione di esempio da GitHub. Per clonare il repository di esempio, seguire questi passaggi:

1. Aprire un prompt dei comandi o una finestra del terminale.
1. Passare alla cartella in cui archiviare l'applicazione di esempio.
1. Eseguire il comando seguente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Installare il pacchetto per Feather HUZZAH ESP8266 in Visual Studio Code:

1. Aprire la cartella in cui è archiviata l'applicazione di esempio.
1. Aprire il file app.ino nella cartella dell'app in Visual Studio Code.

   ![Aprire l'applicazione di esempio in Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

1. In Visual Studio Code immettere `F1`.
1. Digitare **Arduino** e selezionare **Arduino: Board Manager**.
1. Nella scheda **Arduino Board Manager** fare clic su **Additional URLs** (Altri URL).

   ![Arduino Board Manager di Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

1. Nella finestra **User Settings** (Impostazioni utente) copiare e incollare il codice seguente alla fine del file.

   `"arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"`

   ![Configurare l'URL del pacchetto Arduino in Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

1. Salvare il file e chiudere la scheda **User Settings** (Impostazioni utente).
1. Fare clic su **Refresh Package Indexes** (Aggiorna indici di pacchetto). Al termine dell'aggiornamento, cercare **esp8266**.
1. Fare clic sul pulsante **Install** (Installa) per esp8266.

   Boards Manager indica che è installata ESP8266 con una versione 2.2.0 o successiva.

   ![Il pacchetto esp8266 è installato](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

1. Immettere `F1` e quindi digitare **Arduino** e selezionare **Arduino: Board Config** (Arduino: Configurazione scheda).
1. Fare clic sulla casella accanto a **Selected Board** (Scheda selezionata), digitare **esp8266** e quindi selezionare **Adafruit HUZZAH ESP8266 (esp8266)**.

   ![Selezionare la scheda esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Installare le librerie necessarie

1. In Visual Studio Code immettere `F1` e quindi digitare **Arduino** e selezionare **Arduino: Library Manager**.
1. Cercare i seguenti nomi di libreria uno alla volta. Per ogni libreria trovata fare clic su **Install** (Installa).
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Non si dispone di un sensore DHT22 reale?

L'applicazione di esempio consente di simulare i dati di temperatura e umidità nel caso non si disponga di un sensore DHT22 reale. Per abilitare l'applicazione di esempio all'uso di dati simulati, seguire questa procedura:

1. Aprire il file `config.h` nella cartella `app`.
1. Individuare la seguente riga di codice e modificare il valore da `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurare l'applicazione di esempio per l'uso di dati simulati](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

1. Salvare il file.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Distribuire l'applicazione di esempio in Feather HUZZAH ESP8266

1. In Visual Studio Code fare clic su **<Select Serial Port>** sulla barra di stato e quindi sulla porta seriale per Feather HUZZAH ESP8266.
1. Immettere `F1` e quindi digitare **Arduino** e selezionare **Arduino: Upload** per creare e distribuire l'applicazione di esempio in Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Immettere le credenziali

Una volta completato l'upload, seguire questa procedura per immettere le credenziali:

1. Aprire l'IDE di Arduino e fare clic su **Tools** > **Serial Monitor** (Strumenti, Monitoraggio seriale).
1. Nella finestra del monitoraggio seriale notare i due elenchi a discesa nell'angolo inferiore destro.
1. Selezionare **No line ending** (Senza terminazione di riga) per la casella di riepilogo a discesa a sinistra.
1. Selezionare **115200 baud** per la casella di riepilogo a discesa a destra.
1. Nella casella di input nella parte superiore della finestra del monitoraggio seriale immettere le informazioni seguenti, se viene richiesto di fornirle, e quindi fare clic su **Send** (Invia).
   * Wi-Fi SSID
   * Password Wi-Fi
   * Stringa di connessione del dispositivo

> [!Note]
> Le informazioni sulle credenziali sono archiviate in EEPROM di Feather HUZZAH ESP8266. Se si fa clic sul pulsante di reset della scheda Feather HUZZAH ESP8266, l'applicazione di esempio chiede se si vuole cancellare le informazioni. Immettere `Y` per cancellare le informazioni. Viene chiesto di specificare nuovamente le informazioni.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificare che l'applicazione di esempio venga eseguita correttamente

Se si vede il seguente output dalla finestra di monitoraggio seriale e il LED di Feather HUZZAH ESP8266 lampeggia, significa che l'applicazione di esempio si esegue correttamente.

![Output finale nell'IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passaggi successivi

La scheda Feather HUZZAH ESP8266 è stata connessa all'hub IoT e i dati acquisiti dal sensore sono stati inviati all'hub IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

