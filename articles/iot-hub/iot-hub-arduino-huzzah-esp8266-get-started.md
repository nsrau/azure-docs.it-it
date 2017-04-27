---
title: Da ESP8266 al cloud - Connettere Feather HUZZAH ESP8266 ad Azure IoT Hub | Documentazione Microsoft
description: Visualizza come connettere un dispositivo Arduino denominato Adafruit Feather HUZZAH ESP8266 all&quot;hub IoT di Azure, un servizio Microsoft Cloud che consente di gestire gli asset IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3650f628747f8a9e743711f5c7a175d2a2523565
ms.lasthandoff: 04/12/2017


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

* Mac o PC che esegue Windows o Ubuntu.
* Rete wireless a cui Feather HUZZAH ESP8266 deve connettersi.
* Connessione Internet per scaricare lo strumento di configurazione.
* [IDE Arduino](https://www.arduino.cc/en/main/software) 1.6.8 o versione successiva. Le versioni precedenti non funzionano con la libreria AzureIoT.





Gli elementi seguenti sono facoltativi nel caso in cui non si disponga di un sensore. È possibile anche usare dati di sensori simulati.

* Sensore di temperatura e umidità Adafruit DHT22
* Basetta sperimentale
* Cavi ponticello M/M

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>Creare un hub IoT e registrare un dispositivo per Feather HUZZAH ESP8266

### <a name="to-create-your-iot-hub-in-the-azure-portal-follow-these-steps"></a>Per creare l'hub IoT nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **Nuovo** > **Internet delle cose** > **Hub IoT**.

   ![Creare l'hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. Nel riquadro **Hub IoT** immettere le informazioni necessarie per l'hub IoT:

   ![Informazioni di base per la creazione dell'hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **Nome**: il nome dell'hub IoT. Se il nome immesso è valido, viene visualizzato un segno di spunta verde.
   * **Piano tariffario (piano DTU)**: per questa demo selezionare il livello F1 gratuito. Vedere [il piano tariffario e il livello di scalabilità](https://azure.microsoft.com/pricing/details/iot-hub/).
   * **Gruppo di risorse**: creare un gruppo di risorse host per l'hub IoT o usare un gruppo esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-portal.md).
   * **Percorso**: selezionare la posizione più vicina all'utente in cui viene creato l'hub IoT.
   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

1. Fare clic su **Crea**. La creazione dell'hub IoT può richiedere alcuni minuti. È possibile visualizzare lo stato di avanzamento nel riquadro **Notifiche**.

   ![Monitorare lo stato della creazione dell'hub IoT nel riquadro di notifica](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. Dopo aver creato l'hub IoT, selezionarlo nel dashboard. Annotare il valore **Nome host** che verrà usato più avanti, quindi fare clic su **Criteri di accesso condiviso**.

   ![Ottenere il nome host dell'hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. Nel riquadro **Criteri di accesso condiviso** fare clic sulla norma **iothubowner**, quindi copiare e salvare il valore **Stringa di connessione** dell'hub IoT. Il valore verrà usato più avanti in questo articolo. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md).

   ![Ottenere la stringa di connessione dell'hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

L'hub IoT è stato creato. Assicurarsi di salvare i valori **Nome host** e **Stringa di connessione**. Tali valori vengono usati più avanti in questo articolo.


### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>Registrare un dispositivo per Feather HUZZAH ESP8266 nell'hub IoT

Ogni hub IoT ha un registro delle identità in cui sono archiviate le informazioni sui dispositivi a cui è consentito connettersi all'hub IoT. Perché un dispositivo possa connettersi a un hub IoT, è necessario che nel registro delle identità dell'hub IoT sia presente una voce relativa al dispositivo.


In questa sezione si usa lo strumento dell'interfaccia della riga di comando *iothub explorer*. Lo strumento consente di registrare un dispositivo per Feather HUZZAH ESP8266 nel registro delle identità dell'hub IoT.



> [!NOTE]
> Per funzionare correttamente, iothub explorer richiede Node.js 4.x o versioni successive.

Per registrare un dispositivo per Feather HUZZAH ESP8266, seguire questi passaggi:

1. [Scaricare](https://nodejs.org/en/download/) e installare la versione LTS più recente di Node.js, incluso NPM.
1. Installare iothub explorer tramite NPM.

   * Windows 7 o versioni successive:

     Avviare il prompt dei comandi come amministratore. Installare iothub explorer eseguendo il comando seguente:

     ```bash
     npm install -g iothub-explorer
     ```

   * Ubuntu 16.04 o versioni successive:

     Aprire un terminale usando la scelta rapida da tastiera CTRL+ALT+T, quindi eseguire il comando seguente:

     ```bash
     sudo npm install -g iothub-explorer
     ```

   * MacOS 10.1 o versioni successive:

     Aprire un terminale e quindi eseguire il comando seguente:

     ```bash
     npm install -g iothub-explorer
     ```

3. Accedere all'hub IoT eseguendo il comando seguente:

   ```bash
   iothub-explorer login [your IoT hub connection string]
   ```

4. Registrare un nuovo dispositivo. Nell'esempio seguente, `deviceID` è `new-device`. Ottenere la stringa di connessione usando il comando seguente.

   ```bash
   iothub-explorer create new-device --connection-string
   ```

Prendere nota della stringa di connessione del dispositivo registrato. Verrà usata in un secondo momento.


> [!NOTE]
> Per visualizzare la stringa di connessione dei dispositivi registrati, eseguire il comando `iothub-explorer list`.


## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Connettere Feather HUZZAH ESP8266 con il sensore e il computer
In questa sezione si connettono i sensori alla scheda. Quindi si collega il dispositivo al computer per poterlo usare.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Connettere un sensore di temperatura e umidità DHT22 a Feather HUZZAH ESP8266

Usare la basetta sperimentale e i cavi ponticello per stabilire la connessione come indicato di seguito. Se non si dispone di un sensore, ignorare questa sezione in quanto è possibile usare i dati di sensori simulati.

![Riferimento per le connessioni](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


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

Installare il pacchetto per Feather HUZZAH ESP8266 nell'IDE Arduino:

1. Aprire la cartella in cui è archiviata l'applicazione di esempio.
1. Aprire il file app.ino nella cartella dell'app nell'IDE Arduino.

   ![Aprire l'applicazione di esempio nell'IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. Nell'IDE di Arduino fare clic su **File** > **Preferenze**.
1. Nella finestra di dialogo **Preferences** (Preferenze) fare clic sull'icona accanto alla casella **Additional Boards Manager URLs** (URL di gestione schede aggiuntivi).
1. Nella finestra a comparsa immettere l'URL seguente e quindi fare clic su **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Puntare all'URL di un pacchetto nell'IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. Nella finestra di dialogo **Preferenza** fare clic su **OK**.
1. Fare clic su **Strumenti** > **Bacheca** > **Boards Manager** (Gestione bacheche) e quindi cercare esp8266.

   Boards Manager indica che è installata ESP8266 con una versione 2.2.0 o successiva.

   ![Il pacchetto esp8266 è installato](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Fare clic su **Strumenti** > **Bacheca** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Installare le librerie necessarie

1. Nell'IDE di Arduino fare clic su **Schizzo** > **Include Library** (Includi libreria)  > **Gestisci librerie**.
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
   ![Configurare l'applicazione di esempio per l'uso di dati simulati](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Salvare il file con `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Distribuire l'applicazione di esempio in Feather HUZZAH ESP8266

1. Nell'IDE di Arduino fare clic su **Strumento** > **Porta** e quindi fare clic sulla porta seriale per Feather HUZZAH ESP8266.
1. Fare clic su **Schizzo** > **Carica** per compilare e distribuire l'applicazione di esempio in Feather HUZZAH ESP8266.

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
> Le informazioni sulle credenziali sono archiviate in EEPROM di Feather HUZZAH ESP8266. Se si fa clic sul pulsante di reset della scheda Feather HUZZAH ESP8266, l'applicazione di esempio chiede se si vuole cancellare le informazioni. Immettere `Y` per cancellare le informazioni. Viene chiesto di specificare nuovamente le informazioni.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificare che l'applicazione di esempio venga eseguita correttamente

Se si vede il seguente output dalla finestra di monitoraggio seriale e il LED di Feather HUZZAH ESP8266 lampeggia, significa che l'applicazione di esempio si esegue correttamente.

![Output finale nell'IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passaggi successivi

La scheda Feather HUZZAH ESP8266 è stata connessa all'hub IoT e i dati acquisiti dal sensore sono stati inviati all'hub IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


