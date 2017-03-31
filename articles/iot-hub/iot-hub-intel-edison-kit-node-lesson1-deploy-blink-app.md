---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 1: Distribuire l&quot;app | Documentazione Microsoft'
description: Clonare l&quot;applicazione C di esempio da GitHub ed eseguire gulp per distribuirla nella scheda Intel Edison. Questa applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: progetti led arduino, lampeggiamento led arduino, codice di lampeggiamento led arduino, programma di lampeggiamento arduino, esempio di lampeggiamento arduino
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c
ms.lasthandoff: 01/25/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Clonare l'applicazione C di esempio da GitHub e usare lo strumento gulp per distribuire l'applicazione di esempio in Intel Edison. L'applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
* Come distribuire ed eseguire l'applicazione di esempio in Edison.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato le operazioni seguenti:

* [Configurare il dispositivo][configure-your-device]
* [Ottenere gli strumenti][get-the-tools]

## <a name="open-the-sample-application"></a>Aprire l'applicazione di esempio
Per aprire l'applicazione di esempio, seguire questa procedura:

1. Clonare il repository di esempio da GitHub eseguendo questo comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Struttura del repository][repo-structure]

Il file nella sottocartella `app` è il file di origine chiave contenente il codice per controllare il LED.

### <a name="install-application-dependencies"></a>Installare le dipendenze dell'applicazione
Installare le librerie e gli altri moduli necessari per l'applicazione di esempio eseguendo questo comando:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configurare la connessione del dispositivo
Per configurare la connessione al dispositivo, seguire questa procedura.

1. Generare il file di configurazione del dispositivo eseguendo questo comando:

   ```bash
   gulp init
   ```

   Il file di configurazione `config-edison.json` contiene le credenziali utente usate per accedere a Edison. Per evitare la perdita delle credenziali utente, il file di configurazione viene generato nella sottocartella `.iot-hub-getting-started` della home directory del computer.

2. Aprire il file di configurazione del dispositivo in Visual Studio Code eseguendo questo comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. Sostituire i segnaposto `[device hostname or IP address]` e `[device password]` con l'indirizzo IP e la password annotati nella lezione precedente.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

Congratulazioni. La creazione della prima applicazione di esempio per Edison è completata.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio

### <a name="deploy-and-run-the-sample-app"></a>Distribuire ed eseguire l'app di esempio
Distribuire ed eseguire l'applicazione di esempio eseguendo questo comando:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Verificare il funzionamento dell'app
L'applicazione di esempio termina automaticamente dopo 20 lampeggiamenti del LED. Se il LED non lampeggia, vedere la [guida alla risoluzione dei problemi][troubleshooting] per trovare le soluzioni ai problemi comuni.

![LED lampeggiante][led-blinking]

## <a name="summary"></a>Riepilogo
Sono stati installati gli strumenti necessari per usare Edison ed è stata distribuita un'applicazione di esempio che consente a Edison di far lampeggiare il LED. È ora possibile creare, distribuire ed eseguire un'altra applicazione di esempio che connette Edison all'hub IoT di Azure per inviare e ricevere messaggi.

## <a name="next-steps"></a>Passaggi successivi
[Ottenere gli strumenti di Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md

