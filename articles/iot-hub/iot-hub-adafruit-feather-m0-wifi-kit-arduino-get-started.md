---
title: 'Da M0 al cloud: connettere Feather M0 Wi-Fi ad Azure IoT Hub | Microsoft Docs'
description: Spiega come connettere un dispositivo Arduino denominato Adafruit Feather M0 Wi-Fi all&quot;hub IoT di Azure, un servizio Microsoft Cloud che consente di gestire gli asset IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e2d44f821635ce9d91b67ecdc0653e2ba9c99b01
ms.lasthandoff: 04/13/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Connettere Adafruit Feather M0 Wi-Fi ad Azure IoT Hub nel cloud
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Connessione tra BME280, Feather M0 Wi-Fi e Hub IoT](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Questa esercitazione illustra le nozioni di base per l'uso della scheda Arduino. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Operazioni da fare

Connettere Adafruit Feather M0 Wi-Fi a un hub IoT che è stato creato. Dopodiché, eseguire un'applicazione di esempio in M0 Wi-Fi per raccogliere i dati di temperatura e umidità da BME280. Infine inviare i dati del sensore all'hub IoT.



## <a name="what-you-learn"></a>Contenuto dell'esercitazione

* Come creare un hub IoT e registrare un dispositivo per Feather M0 Wi-Fi
* Come connettere Feather M0 Wi-Fi al sensore e al computer
* Come raccogliere i dati del sensore eseguendo un'applicazione di esempio in Feather M0 Wi-Fi
* Come inviare i dati del sensore all'hub IoT

## <a name="what-you-need"></a>Elementi necessari

![Componenti necessari per l'esercitazione](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Per completare questa operazione è necessario disporre dei componenti seguenti di Feather M0 Wi-Fi Starter Kit:

* Scheda Feather M0 Wi-Fi
* Cavo USB Micro/tipo A

Per l'ambiente di sviluppo sono necessari anche gli elementi seguenti:

* Mac o PC che esegue Windows o Ubuntu.
* Rete wireless a cui Feather M0 Wi-Fi deve connettersi.
* Connessione Internet per scaricare lo strumento di configurazione.
* [IDE Arduino](https://www.arduino.cc/en/main/software) 1.6.8 o versione successiva. Le versioni precedenti non funzionano con la libreria AzureIoT.


Gli elementi seguenti sono facoltativi nel caso in cui non si disponga di un sensore. È possibile anche usare dati di sensori simulati.

* Sensore di temperatura e umidità BME280
* Basetta sperimentale
* Cavi ponticello M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Connettere Feather M0 Wi-Fi al sensore e al computer
In questa sezione si connettono i sensori alla scheda. Quindi si collega il dispositivo al computer per poterlo usare.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Connettere un sensore di temperatura e umidità DHT22 a Feather M0 Wi-Fi

Usare la basetta sperimentale e i cavi ponticello per stabilire la connessione come indicato di seguito. Se non si dispone di un sensore, ignorare questa sezione in quanto è possibile usare i dati di sensori simulati.

![Riferimento per le connessioni](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Per i pin dei sensori usare i collegamenti seguenti:


| Inizio (sensore)           | Fine (scheda)            | Colore del cavo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3 V (Pin 3A)            | Cavo rosso     |
| GND (Pin 29A)            | GND (Pin 6A)           | Cavo nero   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Cavo giallo  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Cavo bianco   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Cavo blu    |
| CS (Pin 33A)             | GPIO 5 (Pin 15J)       | Cavo arancione  |

Per ulteriori informazioni, vedere [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) (Informazioni sul sensore di temperatura + pressione barometrica + umidità Adafruit BME280) e la [piedinatura di Adafruit Feather M0 Wi-Fi](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Ora Feather M0 Wi-Fi è connesso con un sensore funzionante.

![Connettere DHT22 a Feather HUZZAH](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Connettere Feather M0 Wi-Fi al computer

Come mostrato di seguito, usare il cavo USB Micro/tipo A per connettere Feather M0 Wi-Fi al computer.

![Connettere Feather HUZZAH al computer](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Aggiungere le autorizzazioni per la porta seriale (solo Ubuntu)

Se si usa Ubuntu, assicurarsi di avere le autorizzazioni per operare sulla porta USB di Feather M0 Wi-Fi. Per aggiungere autorizzazioni sulla porta seriale, seguire questi passaggi:


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

In questa sezione si distribuisce e si esegue un'applicazione di esempio in Feather M0 Wi-Fi. L'applicazione di esempio fa lampeggiare il LED sulla scheda Feather M0 Wi-Fi e invia i dati di temperatura e umidità raccolti dal sensore BME280 all'hub IoT.

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>Ottenere l'applicazione di esempio da Github e preparare l'IDE di Arduino

L'applicazione di esempio è ospitata in GitHub. Clonare il repository di esempio che contiene l'applicazione di esempio da GitHub. Per clonare il repository di esempio, seguire questi passaggi:

1. Aprire un prompt dei comandi o una finestra del terminale.
1. Passare alla cartella in cui archiviare l'applicazione di esempio.
1. Eseguire il comando seguente:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Installare il pacchetto per Feather M0 Wi-Fi nell'IDE Arduino:

1. Aprire la cartella in cui è archiviata l'applicazione di esempio.
1. Aprire il file app.ino nella cartella dell'app nell'IDE Arduino.

   ![Aprire l'applicazione di esempio nell'IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. Fare clic su **Strumenti** > **Scheda** > **Boards Manager** (Gestione schede) e quindi installare `Arduino SAMD Boards` versione `1.6.2` o versione successiva 

   Boards Manager (Gestione schede) indica che è installato `Arduino SAMD Boards` con una versione di `1.6.2` o successiva.

   ![Il pacchetto esp8266 è installato](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. Fare clic su **Strumenti** > **Schede** > **Adafruit M0 Wi-Fi**.

1. Installare i driver (solo Windows) quando si collega Feather, probabilmente sarà necessario installare un driver, fare clic su [qui](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) per scaricare il programma di installazione di driver.
   Seguire la procedure per installare i driver che si desidera installare.

### <a name="install-necessary-libraries"></a>Installare le librerie necessarie

1. Nell'IDE di Arduino fare clic su **Schizzo** > **Include Library** (Includi libreria)  > **Gestisci librerie**.
1. Cercare i seguenti nomi di libreria uno alla volta. Per ogni libreria trovata fare clic su **Install** (Installa).
   * `Adafruit_WINC1500`
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-bme280-sensor"></a>Non si dispone di un sensore BME280 reale?

L'applicazione di esempio consente di simulare i dati di temperatura e umidità nel caso non si disponga di un sensore BME280 reale. Per abilitare l'applicazione di esempio all'uso di dati simulati, seguire questa procedura:

1. Aprire il file `config.h` nella cartella `app`.
1. Individuare la seguente riga di codice e modificare il valore da `false` a `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurare l'applicazione di esempio per l'uso di dati simulati](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. Salvare il file con `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Distribuire l'applicazione di esempio in Feather M0 Wi-Fi

1. Nell'IDE di Arduino fare clic su **Strumento** > **Porta** e quindi fare clic sulla porta seriale per Feather M0 Wi-Fi.
1. Fare clic su **Schizzo** > **Carica** per compilare e distribuire l'applicazione di esempio in Feather M0 Wi-Fi.

### <a name="enter-your-credentials"></a>Immettere le credenziali

Una volta completato l'upload, seguire questa procedura per immettere le credenziali:

1. Nell'IDE di Arduino fare clic su **Strumenti** > **Serial Monitor** (Monitoraggio seriale).
1. Nella finestra del monitoraggio seriale notare i due elenchi a discesa nell'angolo inferiore destro.
1. Selezionare **No line ending** (Senza terminazione di riga) per la casella di riepilogo a discesa a sinistra.
1. Selezionare **115200 baud** per la casella di riepilogo a discesa a destra.
1. Nella casella di input nella parte superiore della finestra del monitoraggio seriale immettere le informazioni seguenti, se viene richiesto di fornirle, e quindi fare clic su **Invia**.
   * Wi-Fi SSID
   * Password Wi-Fi
   * Stringa di connessione del dispositivo

> [!Note]
> Le informazioni sulle credenziali sono archiviate in EEPROM di Feather M0 Wi-Fi. Se si fa clic sul pulsante di reset della scheda Feather M0 Wi-Fi, l'applicazione di esempio chiede se si desidera cancellare le informazioni. Immettere `Y` per cancellare le informazioni. Viene chiesto di specificare nuovamente le informazioni.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificare che l'applicazione di esempio venga eseguita correttamente

Se si vede l'output seguente dalla finestra di monitoraggio seriale e il LED di Feather M0 Wi-Fi lampeggia, significa che l'applicazione di esempio si esegue correttamente.

![Output finale nell'IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passaggi successivi

La scheda Feather M0 Wi-Fi è stata connessa all'hub IoT correttamente e i dati acquisiti dal sensore sono stati inviati all'hub IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


