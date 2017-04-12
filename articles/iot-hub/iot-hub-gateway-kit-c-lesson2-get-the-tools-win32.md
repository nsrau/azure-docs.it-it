---
title: 'Dispositivo SensorTag e gateway Azure IoT: lezione 2: Ottenere gli strumenti (Windows) | Documentazione Microsoft'
description: Installare gli strumenti e il software nel computer host che esegue Windows, creare un hub IoT e registrare il dispositivo nell&quot;hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: sviluppo iot, software iot, servizio cloud iot, software per internet delle cose, interfaccia della riga di comando di azure, installare git in windows, esecuzione di gulp, installare node js in windows, installare npm in windows, installare python in windows
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 18ae6ee4-574a-4d5f-9838-ca2a78165628
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 4a854222be9aaf6733f1c256dd75195927299099
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-windows-7-and-later"></a>Ottenere gli strumenti (Windows 7 e versioni successive)
> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Installare Git, Node.js, Gulp e Python.
- Installare l'interfaccia della riga di comando di Azure. 

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come installare [Git](https://git-scm.com/) e [Node.js](https://nodejs.org/en/).
  - Git è un sistema distribuito open source di controllo delle versioni. L'applicazione di esempio per questa lezione è archiviata in Git.
  - Node.js è un runtime JavaScript con un ampio ecosistema di pacchetti.
- Come usare [NPM](https://www.npmjs.com/) per installare gli strumenti di sviluppo Node.js.
  - La versione minima richiesta di Node.js è 4.5 LTS.
  - NPM è uno degli strumenti di gestione pacchetti per Node.js.
- Come installare Visual Studio Code.
  - Visual Studio Code è un editor di codice sorgente multipiattaforma leggero ma potente per Windows, Linux e macOS. Offre un eccellente supporto per il debug, il controllo di Git incorporato, l'evidenziazione della sintassi, il completamento intelligente del codice, i frammenti e il refactoring del codice.
- Come installare Python.
  - Python è un linguaggio di programmazione dinamico, interpretato, generico e di alto livello molto diffuso.
- Come installare l'interfaccia della riga di comando di Azure.
  - L'interfaccia della riga di comando di Azure offre un'esperienza di riga di comando multipiattaforma per Azure. Il provisioning e la gestione delle risorse vengono eseguiti direttamente dalla riga di comando.
- Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Connessione Internet per scaricare gli strumenti e il software.
- Computer Windows.

## <a name="install-git-and-nodejs"></a>Installare Git e Node.js

Fare clic sui collegamenti seguenti per scaricare e installare Git e Node.js LTS per Windows.

- [Ottenere Git per Windows](https://git-scm.com/download/win/)
- [Ottenere Node.js LTS per Windows](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>Installare gli strumenti di sviluppo Node.js

Usare [gulp.js](http://gulpjs.com/) per automatizzare la distribuzione e l'esecuzione di script.

Premere `Windows + R`, digitare `cmd`, premere `Enter` per aprire una finestra del prompt dei comandi e quindi eseguire questo comando:

```cmd
npm install -g gulp
```

Se si verificano problemi nell'installazione, vedere la [guida alla risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md) per le soluzioni alle problematiche comuni.

> [!Note]
> Node, NPM e Gulp sono necessari per eseguire gli script di automazione sviluppati in Node.js.

## <a name="install-python"></a>Installare Python

È possibile scegliere Python 2.7, 3.4 o 3.5. In questa esercitazione si userà Python 2.7. Se è già stato installato Python, passare alla sezione successiva.

[Ottenere Python per Windows](https://www.python.org/downloads/)

È necessario anche aggiungere il percorso delle cartelle in cui vengono installati Python.exe e pip.exe alla variabile di ambiente `PATH` del sistema. Per impostazione predefinita, python.exe viene installato in `C:\Python27` e pip.exe viene installato in `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Per installare l'interfaccia della riga di comando di Azure, seguire questi passaggi:

1. Aprire una finestra del Prompt dei comandi come amministratore.

2. Installare l'interfaccia della riga di comando di Azure eseguendo questi comandi:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   L'installazione potrebbe richiedere 5 minuti.

3. Verificare l'installazione usando il comando seguente:

   ```cmd
   az iot -h
   ```

   Se l'installazione ha esito positivo, verrà visualizzato l'output seguente.

   ![Verificare l'installazione dell'interfaccia della riga di comando di Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Visual Studio Code verrà usato più avanti nell'esercitazione per modificare i file di configurazione.

[Scaricare](https://code.visualstudio.com/docs/setup/windows) e installare Visual Studio Code.

## <a name="summary"></a>Riepilogo

Sono stati installati tutto il software e tutti gli strumenti necessari nel computer host. L'attività successiva consiste nell'usare l'interfaccia della riga di comando di Azure per creare un hub IoT e registrare il dispositivo nell'hub.

## <a name="next-steps"></a>Passaggi successivi
[Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)

