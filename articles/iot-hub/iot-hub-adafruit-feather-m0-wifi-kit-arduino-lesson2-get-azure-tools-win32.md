---
title: 'Connettere Arduino ad Azure IoT: lezione 2: Strumenti di Azure (Windows) | Documentazione Microsoft'
description: Installare Python e l&quot;interfaccia della riga di comando di Azure in Windows 7 e versioni successive.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: interfaccia della riga di comando di azure, servizio cloud iot, cloud arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 70dfff14-4be1-468d-9919-e40f4bead308
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 74f4448cbd5348988c185c86cadad5337da3a0da
ms.lasthandoff: 03/10/2017


---
# <a name="get-azure-tools-windows-7-and-later"></a>Ottenere gli strumenti di Azure (Windows 7 e versioni successive)

> [!div class="op_single_selector"]
> * [Windows 7 o versione successiva][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

Installare Python e l'interfaccia della riga di comando di Azure. In caso di problemi con la scheda Arduino per Adafruit Feather M0 WiFi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come installare Python.
* Come installare l'interfaccia della riga di comando di Azure.

## <a name="what-you-need"></a>Elementi necessari
* Un computer Windows con connessione Internet.
* Una sottoscrizione di Azure attiva. Se non si ha un account Azure, creare un [account Azure gratuito](http://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

## <a name="install-python"></a>Installare Python
[Installare Python](https://www.python.org/downloads/) nel computer Windows. È possibile installare Python 2.7, 3.4 o 3.5. Questa esercitazione è basata su Python 2.7. Se Python è già installato, passare alla sezione successiva e installare l'interfaccia della riga di comando di Azure.

È inoltre necessario aggiungere il percorso delle cartelle in cui vengono installati python.exe e pip.exe alla variabile di ambiente `PATH` del sistema. Per impostazione predefinita, python.exe viene installato in `C:\Python27` e pip.exe viene installato in `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
L'interfaccia della riga di comando di Azure offre un'esperienza di riga di comando multipiattaforma per Azure. Si opera direttamente dalla riga di comando per il provisioning e la gestione delle risorse.

Per installare l'interfaccia della riga di comando di Azure, seguire questi passaggi:

1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Eseguire i comandi seguenti:

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Verificare l'installazione usando il comando seguente:

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
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_win.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
