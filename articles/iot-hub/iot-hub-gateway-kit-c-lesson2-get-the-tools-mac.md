---
title: Preparare il computer Mac e l&quot;hub IoT di Azure | Documentazione Microsoft
description: Installare gli strumenti nel computer Mac, creare un hub IoT e registrare il dispositivo nell&quot;hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: sviluppo iot, software iot, servizio cloud iot, software per internet delle cose, interfaccia della riga di comando di azure, installare python in mac, installare git in mac, esecuzione di gulp, installare node js in mac
ms.assetid: 94e538ef-9857-4023-9c3c-e92a0be7c395
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: 90b211e1d0b85e2c99ab31a633ddcee9218d4874


---
# <a name="get-the-tools-macos"></a>Ottenere gli strumenti (MacOS)
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
  - Python è un diffuso linguaggio di programmazione dinamico e interpretato di alto livello e di uso generico.
- Come installare l'interfaccia della riga di comando di Azure.
  - L'interfaccia della riga di comando di Azure offre un'esperienza di riga di comando multipiattaforma per Azure. Il provisioning e la gestione delle risorse vengono eseguiti direttamente dalla riga di comando.
- Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Connessione Internet per scaricare gli strumenti e il software.
- Computer Mac che esegue OS X Yosemite (10.10) o versioni successive.

## <a name="install-git-and-nodejs"></a>Installare Git e Node.js

Per installare Git e Node.js, usare l'utilità di gestione pacchetti Homebrew seguendo questa procedura:

1. [Scaricare](http://brew.sh/) e installare Homebrew. Se è già stato installato Homebrew, andare al passaggio 2.
   1. Premere `Cmd + Space` e immettere `Terminal` per aprire un terminale.
   2. Eseguire il comando seguente:

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. Installare Git e Node.js eseguendo questi comandi:

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Installare gli strumenti di sviluppo Node.js

Usare [gulp.js](http://gulpjs.com/) per automatizzare la distribuzione e l'esecuzione di script.

Per installare gulp, eseguire questo comando nel terminale:

```bash
npm install -g gulp
```

Se si verificano problemi nell'installazione, vedere la [guida alla risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md) per le soluzioni alle problematiche comuni.

> [!Note]
> Node, NPM e Gulp sono necessari per eseguire gli script di automazione sviluppati in Node.js.

## <a name="install-python"></a>Installare Python

Anche se Python 2.7 è incluso in Mac OS X, è consigliabile installare Python tramite Homebrew. Vedere [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/) (Installazione di Python in Mac OS X).

Installare Python ed eseguire i parametri di inizializzazione del programma tramite il comando seguente:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Per installare l'interfaccia della riga di comando di Azure, seguire questi passaggi:

1. Eseguire questi comandi nel terminale:
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   L'installazione potrebbe richiedere 5 minuti.

2. Verificare l'installazione usando il comando seguente:
   ```bash
   az iot -h
   ```
   Se l'installazione ha esito positivo, verrà visualizzato l'output seguente.

   ![Verificare l'installazione dell'interfaccia della riga di comando di Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Visual Studio Code verrà usato più avanti nell'esercitazione per modificare i file di configurazione.

[Scaricare](https://code.visualstudio.com/docs/setup/osx) e installare Visual Studio Code.

## <a name="summary"></a>Riepilogo

Sono stati installati tutto il software e tutti gli strumenti necessari nel computer Mac. L'attività successiva consiste nell'usare l'interfaccia della riga di comando di Azure per creare un hub IoT e registrare il dispositivo nell'hub.

## <a name="next-steps"></a>Passaggi successivi
[Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


