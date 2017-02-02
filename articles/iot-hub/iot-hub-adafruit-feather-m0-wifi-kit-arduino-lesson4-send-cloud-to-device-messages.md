---
title: Eseguire un&quot;applicazione di esempio per ricevere messaggi da cloud a dispositivo dall&quot;hub IoT di Azure | Microsoft Docs
description: "Un&quot;applicazione di esempio viene eseguita nel dispositivo Adafruit Feather M0 WiFi e monitora i messaggi in ingresso dall&quot;hub IoT. Una nuova attività gulp invia messaggi al dispositivo Adafruit Feather M0 WiFi dall&quot;hub IoT per far lampeggiare il LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: led di controllo arduino dal web, led di controllo arduino tramite web
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 55eda75c1e3290cedda1c4433db67c3cf320df20


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire un'applicazione di esempio per ricevere messaggi da cloud a dispositivo
In questo articolo si distribuisce un'applicazione di esempio nella scheda Arduino Adafruit Feather M0 WiFi.

L'applicazione di esempio monitora i messaggi in ingresso provenienti dall'hub IoT. Per inviare messaggi dall'hub IoT alla scheda Arduino è anche possibile eseguire un'attività gulp nel computer. Alla ricezione dei messaggi, l'applicazione di esempio fa lampeggiare il LED. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Connettere l'applicazione di esempio all'hub IoT.
* Distribuire ed eseguire l'applicazione di esempio.
* Inviare messaggi dall'hub IoT alla scheda Arduino per far lampeggiare il LED.

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come monitorare i messaggi in ingresso dall'hub IoT.
* Come inviare i messaggi da cloud a dispositivo dall'hub IoT alla scheda Arduino.

## <a name="what-you-need"></a>Elementi necessari
* La scheda Arduino pronta all'uso. Per informazioni su come impostare la scheda Arduino, vedere [Configurare il dispositivo][configure-your-device].
* Un hub IoT creato nella propria sottoscrizione di Azure. Per informazioni sulla creazione dell'hub IoT, vedere l'articolo su come [creare l'hub IoT di Azure][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Connettere l'applicazione di esempio all'hub IoT

1. Assicurarsi che sia aperta la cartella `iot-hub-c-feather-m0-getting-started` del repository.

   Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```

   Si noti il file `app.ino` nella sottocartella `app`. `app.ino` è il file di origine chiave che contiene il codice per il monitoraggio dei messaggi in ingresso dall'hub IoT. La funzione `blinkLED` fa lampeggiare il LED.

   ![Struttura del repository nell'applicazione di esempio][repo-structure]

2. Ottenere la porta seriale del dispositivo usando l'interfaccia della riga di comando per l'individuazione del dispositivo:

   ```bash
   devdisco list --usb
   ```

   Verrà visualizzato un output simile al seguente e sarà rilevata la porta COM USB per la scheda Arduino:

   ![Individuazione del dispositivo][device-discovery]

3. Aprire il file `config.json` nella cartella della lezione e aggiungere il valore del numero della porta COM trovato:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Per la porta COM, nella piattaforma Windows il formato è `COM1, COM2, ...`. In macOS o Ubuntu inizia con `/dev/`.

4. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. Sostituire i valori seguenti nel file `config-arduino.json`:

   Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione][create-an-azure-function-app-and-storage-account] è stata completata nello stesso computer, tutte le configurazioni vengono ereditate. È quindi possibile passare direttamente all'attività di distribuzione ed esecuzione dell'applicazione di esempio. Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione][create-an-azure-function-app-and-storage-account] è stata completata in un computer diverso, è necessario sostituire i segnaposto nel file `config-arduino.json`. Il file `config-arduino.json` si trova nella sottocartella della cartella principale.

   ![Contenuto del file config-arduino.json][config-arduino-json]

   * Sostituire **[Wi-Fi SSID]** con il codice SSID Wi-Fi che ha eseguito la connessione a Internet.
   * Sostituire **[Wi-Fi password]** con la password Wi-Fi. Rimuovere la stringa se il Wi-Fi non richiede la password.
   * Sostituire **[IoT device connection string]** con la stringa di connessione del dispositivo che si ottiene eseguendo il comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Sostituire **[IoT hub connection string]** con la stringa di connessione dell'hub IoT che si ottiene eseguendo il comando `az iot hub show-connection-string --name {my hub name}`.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio nella scheda Arduino usando i comandi seguenti:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

Il comando gulp distribuisce l'applicazione di esempio nella scheda Arduino. Quindi, esegue l'applicazione sulla scheda Arduino e un'attività separata sul computer host per l'invio di 20 messaggi di lampeggiamento dall'hub IoT alla scheda Arduino.

Non appena viene eseguita, l'applicazione di esempio rimane in ascolto di messaggi provenienti dall'hub IoT. Nel frattempo, l'attività gulp invia vari messaggi di lampeggiamento dall'hub IoT alla scheda Arduino. Per ogni messaggio di lampeggiamento ricevuto dalla scheda, l'applicazione di esempio chiama la funzione `blinkLED` per far lampeggiare il LED.

Durante l'invio dei 20 messaggi dall'hub IoT alla scheda Arduino da parte dell'attività gulp, il LED dovrebbe lampeggiare ogni due secondi. L'ultimo è un messaggio "stop" che arresta l'esecuzione dell'applicazione.

![Applicazione di esempio con comando gulp e messaggi di lampeggiamento][sample-application]

## <a name="summary"></a>Riepilogo
Sono stati inviati messaggi dall'hub IoT alla scheda Arduino per far lampeggiare il LED. L'attività successiva è facoltativa: modificare il comportamento di accensione e spegnimento del LED.

## <a name="next-steps"></a>Passaggi successivi
[Modificare il comportamento di accensione e spegnimento del LED][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md


<!--HONumber=Dec16_HO2-->


