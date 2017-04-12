---
title: 'Dispositivo simulato e gateway Azure IoT: lezione 3: Eseguire l&quot;app di esempio | Documentazione Microsoft'
description: Eseguire un&quot;app di esempio per dispositivo simulato per inviare dati sulla temperatura all&quot;hub IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: dati al cloud
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 873f3cef8cb1d115f77f0d3fa2c4b50391f66d91
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>Configurare ed eseguire un'app di esempio per dispositivo simulato

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Clonare il repository di esempio.
- Usare l'interfaccia della riga di comando di Azure per ottenere informazioni sull'hub IoT e sul dispositivo logico per l'applicazione di esempio per il dispositivo simulato. Configurare ed eseguire l'applicazione di esempio per il dispositivo simulato.

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Contenuto dell'articolo:

- Come configurare ed eseguire l'applicazione di esempio per il dispositivo simulato.

## <a name="what-you-need"></a>Elementi necessari

È necessario aver completato:

- [Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Clonare il repository di esempio nel computer host

Per clonare il repository di esempio, seguire questa procedura nel computer host:

1. Aprire un prompt dei comandi in Windows o aprire un terminale in macOS o in Ubuntu.
2. Eseguire i comandi seguenti:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>Configurare il dispositivo simulato e NUC

1. Aprire il file di configurazione `config.json` in Visual Studio Code usando il comando seguente:

   ```bash
   code config.json
   ```

2. Sostituire `"has_sensortag": true` con `"has_sensortag": false`.

   ![Configurazione quando non si ha un dispositivo TI SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. Aprire `config-gateway.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. Trovare la riga di codice seguente e sostituire `[device hostname or IP address]` con l'indirizzo IP o il nome host di Intel NUC.
   ![Screenshot del gateway di configurazione](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>Ottenere la stringa di connessione per il dispositivo logico dell'hub IoT

Per ottenere la stringa di connessione dell'hub IoT di Azure per il dispositivo logico, usare questo comando nel computer host:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` è il nome dell'hub IoT usato. Usare iot-gateway come valore di `{resource group name}` e mydevice come valore di `{device id}` se nella lezione 2 non si è modificato il valore.

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>Configurare l'applicazione di esempio di caricamento nel cloud per il dispositivo simulato

Per configurare ed eseguire l'applicazione di esempio di caricamento nel cloud per il dispositivo simulato, seguire questa procedura nel computer host:

1. Aprire `config-sensortag.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![Screenshot di SensorTag di configurazione](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. Sostituire i valori seguenti nel codice:
   - Sostituire `[IoT hub name]` con il nome dell'hub IoT.
   - Sostituire `[IoT device connection string]` con la stringa di connessione per il dispositivo logico dell'hub IoT.

3. Eseguire l'applicazione.

   Distribuire ed eseguire l'applicazione eseguendo questo comando:

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>Verificare il funzionamento dell'applicazione di esempio

Verrà ora visualizzato un output simile al seguente:

![Output dell'applicazione di esempio per il dispositivo simulato](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

L'applicazione invia i dati sulla temperatura all'hub IoT per 40 secondi.

## <a name="summary"></a>Riepilogo

È stata configurata ed eseguita l'applicazione di esempio di caricamento nel cloud dal dispositivo simulato, che invia i dati all'hub IoT con il dispositivo simulato.

## <a name="next-steps"></a>Passaggi successivi
[Leggere messaggi dall'hub IoT](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)
