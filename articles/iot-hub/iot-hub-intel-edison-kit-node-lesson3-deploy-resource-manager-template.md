---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 3: Creare l&quot;app per le funzioni | Documentazione Microsoft'
description: L&quot;app per le funzioni di Azure rimane in ascolto degli eventi dell&quot;hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell&quot;archiviazione tabelle di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: archiviazione di dati nel cloud, dati archiviati nel cloud, servizio cloud iot
ms.assetid: 37ee5962-95ce-40e8-8162-17e735eaec21
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 72d1496fcf353662fa2f7b003ee3757c45b8181e
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
[Funzioni di Azure](../../articles/azure-functions/functions-overview.md) è una soluzione che consente di eseguire facilmente piccole parti di codice, ovvero *funzioni*, nel cloud. Un'app per le funzioni di Azure ospita l'esecuzione delle funzioni in Azure.

## <a name="what-will-you-do"></a>Contenuto dell'esercitazione
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure. L'app per le funzioni di Azure rimane in ascolto degli eventi dell'hub IoT di Azure, elabora i messaggi in ingresso e li scrive nell'archiviazione tabelle di Azure. L'account di archiviazione viene usato per leggere le copie con salvataggio permanente dei messaggi dalla tabella Azure. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-will-you-learn"></a>Concetti legati all'esercitazione
Contenuto dell'articolo:
* Come usare [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) per distribuire le risorse di Azure.
* Come usare un'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT e scriverli in una tabella nell'archiviazione tabelle di Azure.

## <a name="what-do-you-need"></a>Elementi necessari
È necessario aver completato:
- [Introduzione a Intel Edison][get-started-with-your-intel-edison]
- [Creare l'hub IoT di Azure][create-your-azure-iot-hub]

## <a name="open-the-sample-app"></a>Aprire l'app di esempio
Aprire il progetto di esempio in Visual Studio Code usando i comandi seguenti:

```bash
cd Lesson3
code .
```

![Struttura del repository][repo-structure]

* Il file nella sottocartella `app` è il file di origine chiave. Questo file contiene il codice per inviare 20 volte un messaggio all'hub IoT e far lampeggiare il LED per ogni messaggio inviato.
* Il file `arm-template.json` è il modello di Azure Resource Manager che contiene un'app per le funzioni di Azure e un account di archiviazione di Azure.
* `arm-template-param.json` è il file di configurazione usato dal modello di Azure Resource Manager.
* La sottocartella `ReceiveDeviceMessages` contiene il codice Node.js per la funzione di Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurare i modelli di Azure Resource Manager e creare risorse in Azure
Aggiornare il file `arm-template-param.json` in Visual Studio Code.

![Parametri del modello di Azure Resource Manager][arm-template-parameters]

* Sostituire **[your IoT Hub name]** con il valore di **{my hub name}** specificato durante la [creazione dell'hub IoT e registrazione di Intel Edison][created-your-iot-hub-and-registered-intel-edison].
* Sostituire **[prefix string for new resources]** con il prefisso desiderato. Usando un prefisso si ha la sicurezza che il nome della risorsa sia globalmente univoco per evitare conflitti. Non usare un trattino o un numero all'inizio del prefisso.

Dopo aver aggiornato il file `arm-template-param.json`, distribuire le risorse in Azure usando il comando seguente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Per creare queste risorse sono necessari circa cinque minuti. Mentre è in corso la creazione di risorse, è possibile passare all'articolo successivo.

## <a name="summary"></a>Riepilogo
È stata creata l'app per le funzioni di Azure per elaborare i messaggi dell'hub IoT ed è stato configurato un account di archiviazione di Azure per archiviare tali messaggi. È ora possibile distribuire ed eseguire l'esempio per l'invio di messaggi da dispositivo a cloud in Edison.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud in Intel Edison][send-device-to-cloud-messages].
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-started-with-your-intel-edison]: iot-hub-intel-edison-kit-node-get-started.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-get-started.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson3/repo_structure.png
[arm-template-parameters]: media/iot-hub-intel-edison-lessons/lesson3/arm_para.png
[created-your-iot-hub-and-registered-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
