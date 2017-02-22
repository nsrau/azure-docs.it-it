---
title: 'Connettere Raspberry Pi (C) ad Azure IoT: lezione 2: Strumenti di Azure (Ubuntu) | Documentazione Microsoft'
description: Installare Python e l&quot;interfaccia della riga di comando di Azure in Ubuntu.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: interfaccia della riga di comando di azure, servizio cloud iot, cloud arduino
ms.assetid: bb5cb602-7292-4772-ac90-c0b52ebc8340
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e29f43af1ab46ad9f80137924632b6a975375c1a


---
# <a name="get-azure-tools-ubuntu-1604"></a>Ottenere gli strumenti di Azure (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

Installare l'interfaccia della riga di comando di Azure. In caso di problemi con la scheda Arduino per Adafruit Feather M0 WiFi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come installare l'interfaccia della riga di comando di Azure.
* Come aggiungere un sottogruppo IoT dell'interfaccia della riga di comando di Azure.

## <a name="what-you-need"></a>Elementi necessari
* Un computer Ubuntu con connessione Internet.
* Una sottoscrizione di Azure attiva. Se non si ha un account, è possibile creare un [account di valutazione gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure offre un'esperienza multipiattaforma per Azure, permettendo di lavorare direttamente dalla riga di comando per effettuare il provisioning delle risorse e gestirle.

Per installare l'interfaccia della riga di comando di Azure più recente, seguire questi passaggi:

1. Eseguire i comandi seguenti in una finestra del terminale. L'installazione dell'interfaccia della riga di comando di Azure può richiedere cinque minuti.

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. Verificare l'installazione usando il comando seguente:

   ```bash
   az iot -h
   ```

Se l'installazione ha esito positivo, verrà visualizzato l'output seguente.

![Output che indica l'esito positivo][output]

## <a name="summary"></a>Riepilogo
È stata installata l'interfaccia della riga di comando di Azure. L'attività successiva consiste nella creazione dell'hub IoT di Azure e dell'identità del dispositivo tramite l'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Creare l'hub IoT e registrare la scheda Arduino][create-your-iot-hub-and-register-your-arduino-board]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_ubuntu.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Jan17_HO4-->


