---
title: 'Connettere Raspberry Pi (C) ad Azure IoT: lezione 4: da cloud a dispositivo | Microsoft Docs'
description: "Un&quot;applicazione di esempio viene eseguita nel dispositivo Pi e monitora i messaggi in ingresso dall&quot;hub IoT. Una nuova attività gulp invia messaggi al dispositivo Pi dall&quot;hub IoT per far lampeggiare il LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: da cloud a dispositivo, messaggio dal cloud
ms.assetid: fcbc0dd0-cae3-47b0-8e58-240e4f406f75
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 3a6957ebbb239eacb4fce696b7fcfd02e690310b


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire un'applicazione di esempio per ricevere messaggi da cloud a dispositivo
Questo articolo illustra come distribuire un'applicazione di esempio nel dispositivo Raspberry Pi 3. L'applicazione di esempio monitora i messaggi in ingresso provenienti dall'hub IoT. È anche possibile eseguire un'attività gulp nel computer per inviare messaggi dall'hub IoT al dispositivo Pi. Alla ricezione dei messaggi, l'applicazione di esempio fa lampeggiare il LED. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Connettere l'applicazione di esempio all'hub IoT.
* Distribuire ed eseguire l'applicazione di esempio.
* Inviare messaggi dall'hub IoT al dispositivo Pi per far lampeggiare il LED.

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione
Contenuto dell'articolo:
* Come monitorare i messaggi in ingresso dall'hub IoT.
* Come inviare messaggi da cloud a dispositivo dall'hub IoT al dispositivo Pi.

## <a name="what-you-need"></a>Elementi necessari
* Raspberry Pi 3, impostato per l'uso. Per informazioni su come impostare il dispositivo Pi, vedere [Configurare il dispositivo](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md).
* Un hub IoT creato nella propria sottoscrizione di Azure. Per informazioni su come creare l'hub IoT, vedere [Creare l'hub IoT e registrare Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Connettere l'applicazione di esempio all'hub IoT
1. Assicurarsi che sia aperta la cartella `iot-hub-c-raspberrypi-getting-started` del repository. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```

   Si noti il file `app.c` nella sottocartella `app`. `app.c` è il file di origine chiave che contiene il codice per il monitoraggio dei messaggi in ingresso dall'hub IoT. La funzione `blinkLED` fa lampeggiare il LED.

   ![Struttura del repository nell'applicazione di esempio](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure_c.png)
2. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   npm install
   gulp init
   ```

   Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) è stata completata con lo stesso computer, tutte le configurazioni vengono ereditate. È quindi possibile passare direttamente all'attività di distribuzione ed esecuzione dell'applicazione di esempio. Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) è stata completata con un computer diverso, è necessario sostituire i segnaposto nel file `config-raspberrypi.json`. Il file `config-raspberrypi.json` si trova nella sottocartella della cartella principale.

   ![Contenuto del file config-raspberrypi.json](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* Sostituire **[device hostname or IP address]** con l'indirizzo IP o il nome host del dispositivo Pi che si ottiene eseguendo il comando `devdisco list --eth`.
* Sostituire **[IoT device connection string]** con la stringa di connessione del dispositivo che si ottiene eseguendo il comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}`.
* Sostituire **[IoT hub connection string]** con la stringa di connessione dell'hub IoT che si ottiene eseguendo il comando `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}`.

> [!NOTE]
> Se non è stato fatto nella lezione 1, eseguire anche il comando **gulp install-tools**.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio nel dispositivo Pi con questi comandi:

```
gulp deploy && gulp run
```

Il comando gulp esegue innanzitutto l'attività install-tools, quindi distribuisce l'applicazione di esempio nel dispositivo Pi. Esegue infine l'applicazione nel dispositivo Pi e un'attività separata nel computer host per inviare 20 messaggi di lampeggiamento dall'hub IoT al dispositivo Pi.

Non appena viene eseguita, l'applicazione di esempio rimane in ascolto di messaggi provenienti dall'hub IoT. Nel frattempo, l'attività gulp invia vari messaggi di lampeggiamento dall'hub IoT al dispositivo Pi. Per ogni messaggio di lampeggiamento ricevuto dal dispositivo Pi, l'applicazione di esempio chiama la funzione `blinkLED` per far lampeggiare il LED.

Durante l'invio dei 20 messaggi dall'hub IoT al dispositivo Pi da parte dell'attività gulp, il LED dovrebbe lampeggiare ogni due secondi. L'ultimo è un messaggio "stop" che arresta l'esecuzione dell'applicazione.

![Applicazione di esempio con comando gulp e messaggi di lampeggiamento](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink_c.png)

## <a name="summary"></a>Riepilogo
Sono stati inviati messaggi dall'hub IoT al dispositivo Pi per far lampeggiare il LED. L'attività successiva è facoltativa: modificare il comportamento di accensione e spegnimento del LED.

## <a name="next-steps"></a>Passaggi successivi
[Modificare il comportamento di accensione e spegnimento del LED](iot-hub-raspberry-pi-kit-c-lesson4-change-led-behavior.md)



<!--HONumber=Jan17_HO4-->


