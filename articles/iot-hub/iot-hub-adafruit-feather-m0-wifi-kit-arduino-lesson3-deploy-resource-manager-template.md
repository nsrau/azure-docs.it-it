---
title: 'Connettere Arduino (C) ad Azure IoT: lezione 3: Distribuzione del modello | Documentazione Microsoft'
description: L&quot;app per le funzioni di Azure rimane in ascolto degli eventi dell&quot;hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell&quot;archiviazione tabelle di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: archiviazione di dati nel cloud, dati archiviati nel cloud, servizio cloud iot
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cf666d17406a49036082a6a4dd15bad89c04aa8f
ms.lasthandoff: 01/24/2017


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
[Funzioni di Azure](../../articles/azure-functions/functions-overview.md) è una soluzione che consente di eseguire facilmente piccole parti di codice, ovvero *funzioni*, nel cloud. Un'app per le funzioni di Azure ospita l'esecuzione delle funzioni in Azure.

## <a name="what-will-you-do"></a>Contenuto dell'esercitazione
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure. L'app per le funzioni di Azure rimane in ascolto degli eventi dell'hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell'archiviazione tabelle di Azure.

In caso di problemi con la scheda Arduino per Adafruit Feather M0 WiFi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-will-you-learn"></a>Concetti legati all'esercitazione
Contenuto dell'articolo:
* Come usare [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) per distribuire le risorse di Azure.
* Come usare un'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT e scriverli in una tabella nell'archiviazione tabelle di Azure.

## <a name="what-do-you-need"></a>Elementi necessari
È necessario aver completato:
- [Introduzione alla scheda Arduino][get-started]
- [Creare l'hub IoT di Azure][create-iot-hub]

## <a name="open-the-sample-app"></a>Aprire l'app di esempio
Aprire il progetto di esempio in Visual Studio Code usando i comandi seguenti:

```bash
cd Lesson3
code .
```

![Struttura del repository][repo-structure]

* Il file `app.ino` nella sottocartella `app` è il file di origine chiave. Questo file contiene il codice per inviare 20 volte un messaggio all'hub IoT e far lampeggiare il LED per ogni messaggio inviato.
* `config.json` contiene le impostazioni di configurazione necessarie.
* Il file `arm-template.json` è il modello di Azure Resource Manager che contiene un'app per le funzioni di Azure e un account di archiviazione di Azure.
* `arm-template-param.json` è il file di configurazione usato dal modello di Azure Resource Manager.
* La sottocartella `ReceiveDeviceMessages` contiene il codice Node.js per la funzione di Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurare i modelli di Azure Resource Manager e creare risorse in Azure
Aggiornare il file `arm-template-param.json` in Visual Studio Code.

![Parametri del modello di Azure Resource Manager][arm-template-params]

* Sostituire **[your IoT Hub name]** con il valore di **{my hub name}** specificato quando [è stato creato l'hub IoT ed è stata registrata la scheda Arduino][created-iot-hub-and-registered-arduino-board].
* Sostituire **[prefix string for new resources]** con il prefisso desiderato. Usando un prefisso si ha la sicurezza che il nome della risorsa sia globalmente univoco per evitare conflitti. Non usare un trattino o un numero all'inizio del prefisso.

Dopo aver aggiornato il file `arm-template-param.json`, distribuire le risorse in Azure usando il comando seguente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Per creare queste risorse sono necessari circa cinque minuti. Mentre è in corso la creazione di risorse, è possibile passare all'articolo successivo.

## <a name="summary"></a>Riepilogo
È stata creata l'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT ed è stato configurato un account di archiviazione di Azure per archiviare tali messaggi. È ora possibile distribuire ed eseguire l'esempio per l'invio di messaggi da dispositivo a cloud sulla scheda Arduino.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud nella scheda Arduino][send-device-to-cloud-messages]

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
