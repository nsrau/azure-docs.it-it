---
title: 'Connettere Intel Edison (C) ad Azure IoT: lezione 1: Ottenere gli strumenti (macOS) | Documentazione Microsoft'
description: Scaricare e installare il software e gli strumenti necessari per la prima applicazione di esempio per Edison in macOS.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: strumenti di sviluppo arduino, sviluppo iot, software iot, software per internet delle cose, installare git in mac, installare node js in mac
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 3f764f2e-25fa-4dde-9e8d-d278453fccfd
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 6bfefbdb7728d375ea05a17978ece84aaf8e2821
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-macos-1010"></a>Ottenere gli strumenti (macOS 10.10)
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
  * La versione minima richiesta di Node.js è 4.5 LTS.
  * [NPM](https://www.npmjs.com) è uno degli strumenti di gestione pacchetti per Node.js.

## <a name="what-you-need"></a>Elementi necessari
Per completare questa operazione saranno necessari:
* Una connessione Internet per scaricare gli strumenti di sviluppo e il software.
* Un computer Mac che esegue macOS Yosemite (10.10) o versione successiva.

## <a name="install-git-and-nodejs"></a>Installare Git e Node.js
Per installare Git e Node.js, usare l'utilità di gestione pacchetti [Homebrew](http://brew.sh) seguendo questa procedura.

1. Installare Homebrew. Se è già stato installato Homebrew, andare al passaggio 2.

   1. Premere `Cmd + Space` e immettere `Terminal` per aprire un terminale.
   2. Eseguire il comando seguente:

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Installare Git e Node.js eseguendo questi comandi:

   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>Installare altri strumenti di sviluppo Node.js
Usare [gulp.js](http://gulpjs.com) per automatizzare la distribuzione dell'applicazione di esempio in Edison.

Installare `gulp` eseguendo questo comando nel terminale:

```bash
sudo npm install -g gulp
```

Se si verificano problemi durante l'installazione in macOS di Node.js e di questi strumenti di sviluppo aggiuntivi, vedere la [guida alla risoluzione dei problemi][troubleshooting] per le soluzioni alle problematiche comuni.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code
[Scaricare](https://code.visualstudio.com/docs/setup/osx) e installare Visual Studio Code. Visual Studio Code è un editor di codice sorgente leggero, ma potente per Windows, Linux e macOS. Più avanti nell'esercitazione si userà questo editor per modificare il codice di esempio.

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

