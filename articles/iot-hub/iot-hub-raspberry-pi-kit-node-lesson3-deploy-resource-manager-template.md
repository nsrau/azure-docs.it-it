---
title: Creare un&quot;app per le funzioni di Azure e un account di archiviazione di Azure | Documentazione Microsoft
description: L&quot;app per le funzioni di Azure rimane in ascolto degli eventi dell&quot;hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell&quot;archiviazione tabelle di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: archiviazione di dati nel cloud, dati archiviati nel cloud, servizio cloud iot
ms.assetid: 6c58de85-c5c4-4989-bb5e-08c45c549966
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: df446b1a6b244761f39bfdcebb7e404435c2c35d


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
[Funzioni di Azure](../azure-functions/functions-overview.md) è una soluzione che consente di eseguire facilmente piccole parti di codice, ovvero *funzioni*, nel cloud. Un'app per le funzioni di Azure ospita l'esecuzione delle funzioni in Azure.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure. L'app per le funzioni di Azure rimane in ascolto degli eventi dell'hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell'archiviazione tabelle di Azure. In caso di problemi cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:

* Come usare [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per distribuire le risorse di Azure.
* Come usare un'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT e scriverli in una tabella nell'archiviazione tabelle di Azure.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato:
* [Introduzione a Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
* [Creare l'hub IoT di Azure](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="open-the-sample-app"></a>Aprire l'app di esempio
Aprire il progetto di esempio in Visual Studio Code usando i comandi seguenti:

```bash
cd Lesson3
code .
```

![Struttura del repository](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

* Il file `app.js` nella sottocartella `app` è il file di origine chiave. Questo file contiene il codice per inviare 20 volte un messaggio all'hub IoT e far lampeggiare il LED per ogni messaggio inviato.
* Il file `arm-template.json` è il modello di Azure Resource Manager che contiene un'app per le funzioni di Azure e un account di archiviazione di Azure.
* `arm-template-param.json` è il file di configurazione usato dal modello di Azure Resource Manager.
* La sottocartella `ReceiveDeviceMessages` contiene il codice Node.js per la funzione di Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurare i modelli di Azure Resource Manager e creare risorse in Azure
Aggiornare il file `arm-template-param.json` in Visual Studio Code.

![Parametri del modello di Azure Resource Manager](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

* Sostituire **[your IoT Hub name]** con il valore di **{my hub name}** specificato quando [è stato creato l'hub IoT ed è stato registrato il dispositivo Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
* Sostituire **[prefix string for new resources]** con il prefisso desiderato. Usando un prefisso si ha la sicurezza che il nome della risorsa sia globalmente univoco per evitare conflitti. Non usare un trattino o un numero all'inizio del prefisso.

Dopo aver aggiornato il file `arm-template-param.json`, distribuire le risorse in Azure usando il comando seguente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Per creare queste risorse sono necessari circa cinque minuti. Mentre è in corso la creazione di risorse, è possibile passare all'articolo successivo.

## <a name="summary"></a>Riepilogo
È stata creata l'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT ed è stato configurato un account di archiviazione di Azure per archiviare tali messaggi. È ora possibile distribuire ed eseguire l'esempio per l'invio di messaggi da dispositivo a cloud sul dispositivo Pi.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud sul dispositivo Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)




<!--HONumber=Nov16_HO5-->


