---
title: Ottenere gli strumenti per lo starter kit di Azure IoT per Windows 7 o versioni successive | Documentazione Microsoft
description: Scaricare e installare il software e gli strumenti necessari per la prima applicazione di esempio per Edison in Windows 7 e versioni successive.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: strumenti di sviluppo arduino, sviluppo iot, software iot, software per internet delle cose, installare git in windows, installare node js in windows
ms.assetid: 7d29a358-544d-4657-a504-5ed9b79c2925
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: f45b3bf00d619376ac07418f0c02eca5f3241939
ms.openlocfilehash: 111018da51823f928e7f2b815db8621ecda65025


---
# <a name="get-the-tools-windows-7-or-later"></a>Ottenere gli strumenti (Windows 7 o versione successiva)
> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Scaricare gli strumenti di sviluppo e il software per la prima applicazione di esempio per Intel Edison. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

> [!NOTE]
> Anche se il linguaggio di programmazione della logica principale è C, le lezioni fanno uso di strumenti Node.js per compilare e distribuire applicazioni di esempio.

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

Usare [gulp.js](http://gulpjs.com) per automatizzare la distribuzione dell'applicazione di esempio in Edison.

Avviare il prompt dei comandi come amministratore. Installare `gulp` eseguendo questo comando:

```cmd
npm install -g gulp
```

Se si verificano problemi durante l'installazione di Node.js e di questi strumenti di sviluppo di Node.js aggiuntivi nel computer, vedere la [guida alla risoluzione dei problemi][troubleshooting] per le soluzioni alle problematiche comuni.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

[Scaricare](https://code.visualstudio.com/docs/setup/windows) e installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero, ma potente per Windows, Linux e macOS. Più avanti nell'esercitazione si userà questo editor per modificare il codice di esempio.

## <a name="summary"></a>Riepilogo

Sono stati installati gli strumenti di sviluppo e il software necessari per la prima applicazione di esempio. L'attività successiva consiste nella creazione, distribuzione ed esecuzione dell'applicazione di esempio in Edison.

## <a name="next-steps"></a>Passaggi successivi

[Creare e distribuire l'applicazione per il lampeggiamento][create-and-deploy-the-blink-application]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-mac.md



<!--HONumber=Dec16_HO2-->


