---
title: 'Dispositivo simulato e gateway Azure IoT: lezione 4: Salvare i messaggi | Documentazione Microsoft'
description: Salvare i messaggi da Intel NUC all&quot;hub IoT, scriverli nell&quot;archivio tabelle di Azure e quindi leggerli dal cloud.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: archiviazione di dati nel cloud, dati archiviati nel cloud, servizio cloud iot
ms.assetid: ffed0c2e-b092-40e1-9113-8196ec057d67
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 2116e2c2ef5808145b56e63c1e2b91f20e639a3d


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione

Funzioni di Azure è una soluzione che consente di eseguire facilmente delle _funzioni_, ovvero piccole parti di codice, nel cloud. Un'app per le funzioni di Azure ospita l'esecuzione delle funzioni in Azure. 

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure. L'app per le funzioni di Azure rimane in ascolto degli eventi dell'hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell'archiviazione tabelle di Azure.

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).


## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

In questa lezione si apprenderà:

- Come usare Azure Resource Manager per distribuire le risorse di Azure.
- Come usare un'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT e scriverli in una tabella nell'archivio tabelle di Azure.

## <a name="what-you-need"></a>Elementi necessari

È necessario aver completato le lezioni precedenti:

- [Lezione 1: Configurare Intel NUC come gateway IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)
- [Lezione 2: Preparare il computer host e l'hub IoT di Azure](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
- [Lezione 3: Ricevere messaggi dal dispositivo simulato e leggerli dall'hub IoT](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

## <a name="open-a-sample-app"></a>Aprire un'app di esempio

Passare alla cartella del repository `iot-hub-c-intel-nuc-gateway-getting-started`, inizializzare i file di configurazione, quindi aprire il progetto di esempio in Visual Studio Code eseguendo il comando seguente:

```bash
cd Lesson4
npm install
gulp init
code .
```

![Struttura del repository](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- Il file `arm-template.json` è il modello di Azure Resource Manager che contiene un'app per le funzioni di Azure e un account di archiviazione di Azure.
- `arm-template-param.json` è il file di configurazione usato dal modello di Azure Resource Manager.
- La sottocartella `ReceiveDeviceMessages` contiene il codice Node.js per la funzione di Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurare i modelli di Azure Resource Manager e creare risorse in Azure

Aggiornare il file `arm-template-param.json` in Visual Studio Code.

![File JSON del modello di Azure Resource Manager](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- Sostituire `[your IoT Hub name]` con il nome `{my hub name}` specificato nella lezione 2.

Dopo aver aggiornato il file `arm-template-param.json`, distribuire le risorse in Azure usando il comando seguente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

Usare `iot-gateway` come valore di `{resource group name}` se nella lezione 2 non si è modificato il valore.

## <a name="summary"></a>Riepilogo

È stata creata l'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT ed è stato configurato un account di archiviazione di Azure per archiviare tali messaggi. È ora possibile leggere i messaggi inviati dal gateway all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
[Leggere i messaggi con salvataggio permanente in Archiviazione di Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).



<!--HONumber=Jan17_HO4-->


