---
title: 'Connettere Raspberry Pi (Node) ad Azure IoT: lezione 1: Ottenere gli strumenti (Windows) | Documentazione Microsoft'
description: Scaricare e installare gli strumenti necessari e il software per la prima applicazione di esempio per Pi in Windows 7 e versioni successive.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: sviluppo iot, software iot, software per internet delle cose, installare git in windows, esecuzione di gulp, installare node js in windows, installare npm in windows, installare python in windows
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: b3d88e17-97cc-4f23-85fd-a688fc228eb8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 73df1a85cbcf7ee54fe625b6a150f89366ea2c14
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-windows-7-or-later"></a>Ottenere gli strumenti (Windows 7 o versione successiva)

> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Scaricare gli strumenti di sviluppo e il software per la prima applicazione di esempio per Raspberry Pi 3. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:

* Come installare Git e Node.js.
  * [Git](https://git-scm.com) è un sistema distribuito di controllo delle versioni open source. L'applicazione di esempio per questo articolo è archiviata in Git.
  * [Node.js](https://nodejs.org/en/) è un runtime JavaScript con un ampio ecosistema di pacchetti.
* Come usare NPM per installare altri strumenti di sviluppo Node.js.
  * Il requisito di versione minima per Node.js è 4.5 LTS.
  * [NPM](https://www.npmjs.com) è uno degli strumenti di gestione pacchetti per Node.js.

## <a name="what-you-need"></a>Elementi necessari
Per completare questa operazione saranno necessari:

* Una connessione Internet per scaricare gli strumenti di sviluppo e il software.
* Un computer che esegue Windows.

## <a name="install-git-and-nodejs"></a>Installare Git e Node.js
Fare clic sui collegamenti seguenti per scaricare e installare Git e Node.js LTS per Windows.

* [Ottenere Git per Windows](https://git-scm.com/download/win/)
* [Ottenere Node.js LTS per Windows](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>Installare altri strumenti di sviluppo Node.js
Usare [gulp.js](http://gulpjs.com) per automatizzare la distribuzione dell'applicazione di esempio in Pi. È anche possibile usare [device-discovery-cli](https://github.com/Azure/device-discovery-cli) per recuperare informazioni di rete sui dispositivi IoT.

Avviare il prompt dei comandi come amministratore. Installare `gulp` e `device-discovery-cli` eseguendo questo comando:

```bash
npm install -g device-discovery-cli gulp
```

Se si verificano problemi durante l'installazione di Node.js e di questi strumenti di sviluppo di Node.js aggiuntivi nel computer, vedere la [guida alla risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md) per le soluzioni alle problematiche comuni.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code
[Scaricare](https://code.visualstudio.com/docs/setup/windows) e installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero, ma potente per Windows, Linux e macOS. Più avanti nell'esercitazione si userà questo editor per modificare il codice di esempio.

## <a name="summary"></a>Riepilogo
Sono stati installati gli strumenti di sviluppo e il software necessari per la prima applicazione di esempio. L'attività successiva consiste nella creazione, distribuzione ed esecuzione dell'applicazione di esempio nel dispositivo Pi.

## <a name="next-steps"></a>Passaggi successivi
[Creare e distribuire l'applicazione di esempio per il lampeggiamento](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)


