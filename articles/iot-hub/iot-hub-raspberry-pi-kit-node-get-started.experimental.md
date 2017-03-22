---
title: Introduzione a Raspberry Pi 3 | Documentazione Microsoft
description: Introduzione a Raspberry Pi 3 e informazioni su come creare l&quot;hub IoT di Azure e connettervi il dispositivo Pi.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: hub IoT di Azure, introduzione a Internet delle cose, toolkit iot
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1a67616d42d233866d5d3eb86aafb1df911d5cdf
ms.lasthandoff: 12/08/2016


---
# <a name="get-started-with-raspberry-pi-3-nodejs"></a>Introduzione a Raspberry Pi 3 (Node.JS)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

Questa esercitazione illustra le nozioni di base sull'uso di Raspberry Pi 3 con il sistema operativo Raspbian. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md). Per esempi di Windows 10 IoT Core, vedere [Windows Dev Center](http://www.windowsondevices.com/).

Se non si ha ancora un kit, provare l'[emulatore Raspberry Pi 3](https://blogs.msdn.microsoft.com/iliast/2016/11/10/how-to-emulate-raspberry-pi/). In alternativa, acquistare un nuovo kit [qui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="lesson-1-configure-your-device"></a>Lezione 1: Configurare il dispositivo
![Diagramma di flusso della lezione 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Questa lezione illustra come configurare il dispositivo Raspberry Pi 3 con un sistema operativo, impostare l'ambiente di sviluppo e distribuire un'applicazione nel dispositivo.

### <a name="configure-your-device"></a>Configurare il dispositivo
Configurare Raspberry Pi 3 per il primo uso e installare Raspbian. Raspbian è un sistema operativo gratuito ottimizzato per l'hardware Raspberry Pi.

*Tempo previsto per il completamento: 30 minuti*

Passare a [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md).

### <a name="get-the-tools"></a>Get the tools
Scaricare gli strumenti e il software per compilare e distribuire la prima applicazione per Raspberry Pi 3.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md).

### <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
Clonare l'applicazione per il lampeggiamento Node.js di esempio da GitHub e usare gulp per distribuire l'applicazione sulla scheda di Raspberry Pi 3. Questa applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Creare e distribuire l'applicazione per il lampeggiamento](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md).

## <a name="lesson-2-create-your-iot-hub"></a>Lezione 2: Creare l'hub IoT
![Diagramma di flusso della lezione 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Questa lezione illustra come creare un account Azure gratuito, effettuare il provisioning dell'hub IoT di Azure e creare il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

### <a name="get-the-azure-tools"></a>Ottenere gli strumenti di Azure
Installare l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Ottenere gli strumenti di Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md).

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Creare l'hub IoT e registrare Raspberry Pi 3
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare l'hub IoT e registrare Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lezione 3: Inviare messaggi da dispositivo a cloud
![Diagramma di flusso della lezione 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Questa lezione illustra come inviare messaggi dal dispositivo Pi all'hub IoT. Si passa quindi alla creazione di un'app per le funzioni di Azure che preleva i messaggi in ingresso dall'hub IoT e li scrive nell'archiviazione tabelle di Azure.

Prima di iniziare questa lezione, completare la lezione 1 e la lezione 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud
Distribuire ed eseguire un'applicazione di esempio nel dispositivo Raspberry Pi 3 che invia messaggi all'hub IoT.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

### <a name="read-messages-persisted-in-azure-storage"></a>Leggere i messaggi con salvataggio permanente in Archiviazione di Azure
Monitorare i messaggi da dispositivo a cloud mentre vengono scritti in Archiviazione di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi con salvataggio permanente in Archiviazione di Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md).

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lezione 4: Inviare messaggi da cloud a dispositivo
![Diagramma di flusso della lezione 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Questa lezione illustra come inviare messaggi dall'hub IoT di Azure al dispositivo Raspberry Pi 3. I messaggi controllano il comportamento di accensione e spegnimento del LED collegato a Pi. È stata preparata un'applicazione di esempio che permette di completare questa attività.

Prima di iniziare questa lezione, completare la lezione 1, la lezione 2 e la lezione 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo
L'applicazione di esempio della lezione 4 viene eseguita sul dispositivo Pi e monitora i messaggi in ingresso dall'hub IoT. Una nuova attività gulp invia messaggi al dispositivo Pi dall'hub IoT per far lampeggiare il LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi
In caso di problemi durante le lezioni, è possibile cercare soluzioni nell'articolo [Risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md).


