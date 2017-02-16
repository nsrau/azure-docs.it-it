---
title: Eseguire un&quot;applicazione di esempio per inviare messaggi da dispositivo a cloud all&quot;hub IoT di Azure | Documentazione Microsoft
description: Distribuire ed eseguire un&quot;applicazione di esempio in Adafruit Feather M0 WiFi che invia messaggi all&quot;hub IoT e fa lampeggiare il LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: servizio cloud iot, inviare dati al cloud con arduino
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 569a28ae496f196c0f59be499cdef36550cc2ffa


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Questo articolo illustra come distribuire ed eseguire in una scheda Arduino Adafruit Feather M0 WiFi un'applicazione di esempio che invia messaggi all'hub IoT.

In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Si apprenderà come usare lo strumento gulp per distribuire ed eseguire l'applicazione Arduino di esempio nella scheda Arduino.

## <a name="what-you-need"></a>Elementi necessari
* Prima di iniziare questa attività, è necessario aver completato [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure per elaborare e archiviare i messaggi dell'hub IoT][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Ottenere le stringhe di connessione dell'hub IoT e del dispositivo
La stringa di connessione del dispositivo viene usata per connettere la scheda Arduino all'hub IoT. La stringa di connessione dell'hub IoT viene usata per connettere l'hub IoT all'identità del dispositivo che rappresenta la scheda Arduino nell'hub IoT.

* Elencare tutti gli hub IoT nel gruppo di risorse eseguendo il comando seguente dell'interfaccia della riga di comando di Azure:

```bash
az iot hub list -g iot-sample --query [].name
```

Usare `iot-sample` come valore di `{resource group name}` se non si è modificato il valore.

* Ottenere la stringa di connessione dell'hub IoT eseguendo il comando seguente dell'interfaccia della riga di comando di Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` è il nome specificato quando è stato creato l'hub IoT ed è stata registrata la scheda Arduino.

* Ottenere la stringa di connessione del dispositivo usando il comando seguente:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

Usare `mym0wifi` come valore di `{device id}` se non si è modificato il valore.
## <a name="configure-the-device-connection"></a>Configurare la connessione del dispositivo
Per configurare la connessione al dispositivo, seguire questa procedura.

1. Ottenere la porta seriale del dispositivo usando l'interfaccia della riga di comando per l'individuazione del dispositivo:

   ```bash
   devdisco list --usb
   ```

   Verrà visualizzato un output simile al seguente e sarà rilevata la porta COM USB per la scheda Arduino:

   ![Individuazione del dispositivo][device-discovery]

2. Aprire il file `config.json` nella cartella della lezione e aggiungere il valore del numero della porta COM trovato:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Per la porta COM, nella piattaforma Windows il formato è `COM1, COM2, ...`. In macOS o Ubuntu inizia con `/dev/`.

3. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. Aprire il file di configurazione del dispositivo `config-arduino.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. Sostituire i valori seguenti nel file `config-arduino.json`:

   * Sostituire **[Wi-Fi SSID]** con il codice SSID Wi-Fi che ha eseguito la connessione a Internet.
   * Sostituire **[Wi-Fi password]** con la password Wi-Fi. Rimuovere la stringa se il Wi-Fi non richiede la password.
   * Sostituire **[IoT device connection string]** con il valore di `device connection string` ottenuto.
   * Sostituire **[IoT hub connection string]** con il valore di `iot hub connection string` ottenuto.

   > [!NOTE]
   > Non è necessario specificare `azure_storage_connection_string` in questo articolo. Mantenerlo invariato.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio nella scheda Arduino usando il comando seguente:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> L'attività gulp predefinita esegue le attività `install-tools` e `run` in sequenza. Quando [è stata distribuita l'app per il lampeggiamento][deployed-the-blink-app], queste attività sono state eseguite separatamente.

## <a name="verify-that-the-sample-application-works"></a>Verificare il funzionamento dell'applicazione di esempio
Il LED sulla scheda n. 0 dell'interfaccia GPIO dovrebbe lampeggiare ogni due secondi. Ogni volta che il LED lampeggia, l'applicazione di esempio invia un messaggio all'hub IoT e verifica se il messaggio è stato inviato all'hub IoT. Ogni messaggio ricevuto dall'hub IoT viene stampato nella finestra della console. L'applicazione di esempio termina automaticamente dopo l'invio di 20 messaggi.

![Applicazione di esempio con messaggi inviati e ricevuti][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Riepilogo
È stata distribuita ed eseguita la nuova applicazione di esempio per il lampeggiamento nella scheda Arduino per l'invio di messaggi da dispositivo a cloud all'hub IoT. È ora possibile monitorare i messaggi mentre vengono scritti nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
[Leggere i messaggi con salvataggio permanente in Archiviazione di Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md


<!--HONumber=Dec16_HO2-->


