---
title: 'Connettere Raspberry Pi (Node) ad Azure IoT: lezione 2: Ottenere gli strumenti (Ubuntu) | Documentazione Microsoft'
description: Installare Python e l&quot;interfaccia della riga di comando di Azure in Ubuntu.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: servizio cloud iot, interfaccia della riga di comando di azure
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 2f98923a-5274-4220-87d4-77ac8beb4d0f
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b9557037bcaec02b4cc8e61418bd194b7a869033
ms.lasthandoff: 03/10/2017


---
# <a name="get-azure-tools-ubuntu-1604"></a>Ottenere gli strumenti di Azure (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 e versioni successive](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Installare l'interfaccia della riga di comando di Azure. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

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

![Output che indica l'esito positivo](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>Riepilogo
È stata installata l'interfaccia della riga di comando di Azure. L'attività successiva consiste nella creazione dell'hub IoT di Azure e dell'identità del dispositivo tramite l'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Creare l'hub IoT e registrare Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


