---
title: Preparare il computer host e l&quot;hub IoT di Azure | Documentazione Microsoft
description: Installare gli strumenti e il software nel computer host che esegue Ubuntu, creare un hub IoT e registrare il dispositivo nell&quot;hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: sviluppo iot, software iot, servizio cloud iot, software per internet delle cose, interfaccia della riga di comando di azure, installare git in ubuntu, esecuzione di gulp, installare node js in ubuntu
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 3b8aaa4a8e23c69eb1d48574b78598a2e5393166


---
# <a name="get-the-tools-ubuntu-1604"></a>Ottenere gli strumenti (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Installare Git, Node.js, Gulp, Python.
- Installare l'interfaccia della riga di comando di Azure. 

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).
## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come installare Git e Node.js.
  - Git è un sistema distribuito open source di controllo delle versioni. L'applicazione di esempio per questa lezione è archiviata in Git.
  - Node.js è un runtime JavaScript con un ampio ecosistema di pacchetti.
- Come usare NPM per installare gli strumenti di sviluppo Node.js.
  - La versione minima richiesta di Node.js è 4.5 LTS.
  - NPM è uno degli strumenti di gestione pacchetti per Node.js.
- Come installare Visual Studio Code.
  - Visual Studio Code è un editor di codice sorgente multipiattaforma leggero e potente per Windows, Linux e macOS. Dispone di un elevato supporto per funzionalità quali debug, controllo Git incorporato, evidenziazione della sintassi, completamento di codice intelligente, frammenti di codice e refactoring del codice.
- Come installare l'interfaccia della riga di comando di Azure
  - L'interfaccia della riga di comando di Azure offre un'esperienza di riga di comando multipiattaforma per Azure. Si opera direttamente dalla riga di comando per il provisioning e la gestione delle risorse.
- Come usare l'interfaccia della riga di comando di Azure per creare un hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Connessione Internet per scaricare gli strumenti e il software.
- Un computer che esegue Ubuntu 16.04 o versione successiva.

## <a name="install-git-and-nodejs"></a>Installare Git e Node.js

Per installare Git e Node.js, seguire questi passaggi:

1. Premere `Ctrl + Alt + T` per aprire un terminale.
2. Eseguire i comandi seguenti:

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Installare gli strumenti di sviluppo Node.js

Usare [gulp.js](http://gulpjs.com/) per automatizzare la distribuzione e l'esecuzione degli script.

Per installare gulp, eseguire questo comando nel terminale:

```bash
sudo npm install -g gulp
```

Se si verificano problemi con l'installazione, vedere la [Guida alla risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md) per soluzioni ai problemi più comuni.

> [!Note]
> Node, NPM e Gulp sono necessari per eseguire gli script di automazione sviluppati in Node.js.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Per installare l'interfaccia della riga di comando di Azure, seguire questi passaggi:

1. Eseguire questi comandi nel terminale:

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   L'installazione potrebbe richiedere 5 minuti.

2. Verificare l'installazione usando il comando seguente:

   ```bash
   az iot -h
   ```
Se l'installazione ha esito positivo, verrà visualizzato l'output seguente.
![Verificare l'installazione dell'interfaccia della riga di comando di Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Più avanti nell'esercitazione si userà Visual Studio Code per modificare i file di configurazione.

[Scaricare](https://code.visualstudio.com/docs/setup/linux) e installare Visual Studio Code.

## <a name="summary"></a>Riepilogo

È stata eseguita l'installazione di tutti gli strumenti e il software necessari sul computer host. L'attività successiva consiste nell'utilizzare l'interfaccia della riga di comando di Azure per creare un hub IoT e registrarvi il dispositivo.

## <a name="next-steps"></a>Passaggi successivi
[Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


