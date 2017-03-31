---
title: 'Connettere Arduino ad Azure IoT: lezione 1: Distribuire l&quot;app | Documentazione Microsoft'
description: Clonare l&quot;applicazione Arduino di esempio da GitHub ed eseguire gulp per distribuire l&quot;applicazione per Adafruit Feather M0 WiFi. Questa applicazione di esempio consente di far lampeggiare l&quot;interfaccia GPIO
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: progetti led arduino, lampeggiamento led arduino, codice di lampeggiamento led arduino, programma di lampeggiamento arduino, esempio di lampeggiamento arduino
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Clonare l'applicazione di esempio Arduino da GitHub e usare lo strumento gulp per distribuire l'applicazione di esempio nella scheda Adafruit Feather M0 WiFi Arduino. La stessa applicazione di esempio consente di far lampeggiare il LED sulla scheda n. 13 dell'interfaccia GPIO ogni due secondi.

In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting-page].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
* Come distribuire ed eseguire l'applicazione di esempio sulla scheda Arduino.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato le operazioni seguenti:

* [Configurare il dispositivo][configure-your-device]
* [Ottenere gli strumenti][get-the-tools]

## <a name="open-the-sample-application"></a>Aprire l'applicazione di esempio
Per aprire l'applicazione di esempio, seguire questa procedura:

1. Clonare il repository di esempio da GitHub eseguendo questo comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![Struttura del repository][repo-structure]

Il file `app.ino` nella sottocartella `app` è il file di origine chiave contenente il codice per controllare il LED.

### <a name="install-application-dependencies"></a>Installare le dipendenze dell'applicazione
Installare le librerie e gli altri moduli necessari per l'applicazione di esempio eseguendo questo comando:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configurare la connessione del dispositivo
Per configurare la connessione al dispositivo, seguire questa procedura.

1. Ottenere la porta seriale del dispositivo usando l'interfaccia della riga di comando per l'individuazione del dispositivo:

   ```bash
   devdisco list --usb
   ```

   Viene visualizzato un output analogo al seguente e viene rilevata la porta COM USB per la scheda Arduino: ![Individuazione del dispositivo][device-discovery]

2. Aprire il file `config.json` nella cartella della lezione e aggiungere il valore del numero della porta COM trovato:

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Per la porta COM, nella piattaforma Windows il formato è `COM1, COM2, ...`. In macOS o Ubuntu inizia con `/dev/`.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
### <a name="install-the-required-tools-for-your-arduino-board"></a>Installare gli strumenti necessari per la scheda Arduino

Installare Azure IoT SDK per hub per la scheda Arduino eseguendo il comando seguente:

```bash
gulp install-tools
```

A seconda della connessione di rete, l'operazione potrebbe richiedere molto tempo.

> [!NOTE]
> Quando si eseguono le attività gulp `install-tools` e `run`, chiudere l'istanza Arduino IDE corrente.

### <a name="deploy-and-run-the-sample-app"></a>Distribuire ed eseguire l'app di esempio
Distribuire ed eseguire l'applicazione di esempio eseguendo questo comando:

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>Verificare il funzionamento dell'app
Se il LED non lampeggia, vedere la [guida alla risoluzione dei problemi][troubleshooting-page] per trovare le soluzioni ai problemi comuni.

![LED lampeggiante][led-blinking]

## <a name="summary"></a>Riepilogo
Sono stati installati gli strumenti necessari per usare la scheda Arduino ed è stata distribuita un'applicazione di esempio nella scheda Arduino per far lampeggiare il LED. È ora possibile creare, distribuire ed eseguire un'altra applicazione di esempio che connette la scheda Arduino all'hub IoT di Azure per inviare e ricevere messaggi.

## <a name="next-steps"></a>Passaggi successivi
[Ottenere gli strumenti di Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
