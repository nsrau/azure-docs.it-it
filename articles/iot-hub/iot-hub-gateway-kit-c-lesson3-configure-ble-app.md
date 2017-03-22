---
title: 'Dispositivo SensorTag e gateway Azure IoT: lezione 3: Eseguire l&quot;app di esempio | Documentazione Microsoft'
description: Eseguire un&quot;applicazione di esempio BLE per ricevere dati da SensorTag BLE e dall&quot;hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: app BLE, app di monitoraggio dei sensori, raccolta di dati dei sensori, dati dai sensori, dai dati dei sensori al cloud
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>Configurare ed eseguire un'applicazione di esempio BLE

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Clonare il repository di esempio. 
- Configurare la connettività tra SensorTag e Intel NUC. 
- Usare l'interfaccia della riga di comando di Azure per ottenere informazioni dall'hub IoT e da SensorTag per un'applicazione di esempio BLE (Bluetooth Low Energy). Configurare ed eseguire l'applicazione di esempio BLE. 

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Contenuto dell'articolo:

- Come configurare ed eseguire l'applicazione di esempio BLE.

## <a name="what-you-need"></a>Elementi necessari

È necessario aver completato:

- [Create an IoT hub and register SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md) (Creare un hub IoT e registrare SensorTag)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Clonare il repository di esempio nel computer host

Per clonare il repository di esempio, seguire questa procedura nel computer host:

1. Aprire una finestra del prompt dei comandi in Windows o aprire un terminale in macOS o in Ubuntu.
2. Eseguire i comandi seguenti:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>Configurare la connettività tra SensorTag e Intel NUC

Per configurare la connettività, seguire questa procedura nel computer host:

1. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. Aprire `config-gateway.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. Trovare la riga di codice seguente e sostituire `[device hostname or IP address]` con l'indirizzo IP o il nome host di Intel NUC.
   ![Screenshot del gateway di configurazione](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. Installare gli strumenti helper in Intel NUC usando il comando seguente:

   ```bash
   gulp install-tools
   ```

5. Accendere SensorTag premendo il pulsante di alimentazione, come nell'immagine seguente. Il LED verde inizierà a lampeggiare.

   ![Accendere SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. Analizzare i dispositivi SensorTag usando i comandi seguenti:

   ```bash
   gulp discover-sensortag
   ```

7. Testare la connettività tra SensorTag e Intel NUC usando il comando seguente:

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   Sostituire `{mac address}` con l'indirizzo MAC ottenuto nel passaggio precedente.

## <a name="get-the-connection-string-of-sensortag"></a>Ottenere la stringa di connessione di SensorTag

Per ottenere la stringa di connessione dell'hub IoT di Azure per SensorTag, usare questo comando nel computer host:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` è il nome dell'hub IoT usato. Usare iot-gateway come valore di `{resource group name}` e mydevice come valore di `{device id}` se nella lezione 2 non si è modificato il valore.

## <a name="configure-the-ble-sample-application"></a>Configurare l'applicazione di esempio BLE

Per configurare ed eseguire l'applicazione di esempio BLE, seguire questa procedura nel computer host:

1. Aprire `config-sensortag.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![Screenshot di SensorTag di configurazione](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. Sostituire i valori seguenti nel codice:
   - Sostituire `[IoT hub name]` con il nome dell'hub IoT usato.
   - Sostituire `[IoT device connection string]` con la stringa di connessione di SensorTag ottenuta.
   - Sostituire `[device_mac_address]` con l'indirizzo MAC di SensorTag ottenuto.

3. Eseguire l'applicazione di esempio BLE.

   Per eseguire l'applicazione di esempio BLE, seguire questa procedura nel computer host:

   1. Accendere SensorTag.

   2. Distribuire ed eseguire l'applicazione di esempio BLE in Intel NUC usando il comando seguente:
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>Verificare il funzionamento dell'applicazione di esempio BLE

Verrà ora visualizzato un output simile al seguente:

![Output dell'applicazione di esempio BLE](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

L'applicazione di esempio continua a raccogliere dati sulla temperatura e li invia all'hub IoT. L'applicazione di esempio termina automaticamente dopo 40 secondi di invio.

## <a name="summary"></a>Riepilogo

È stata configurata la connettività tra SensorTag e Intel NUC ed è stata eseguita un'applicazione di esempio BLE che raccoglie e invia dati da SensorTag all'hub IoT. Ora è possibile passare all'esercitazione che illustra come verificare che l'hub IoT abbia ricevuto i dati.

## <a name="next-steps"></a>Passaggi successivi
[Leggere messaggi dall'hub IoT](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
