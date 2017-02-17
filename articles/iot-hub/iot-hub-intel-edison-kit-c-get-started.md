---
title: 'Connettere Intel Edison (C) ad Azure IoT: introduzione | Documentazione Microsoft'
description: Introduzione a Intel Edison, creazione dell&quot;hub IoT di Azure e connessione di Edison all&quot;hub IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: sviluppo di intel edison, hub iot di azure, introduzione a internet delle cose, esercitazione su internet delle cose, internet delle cose per adafruit, intel edison arduino, introduzione ad arduino
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 90b7486d8a5997db16419a85e79f1c181de98b95


---
# <a name="get-started-with-intel-edison-c"></a>Introduzione a Intel Edison (C)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

Questa esercitazione illustra le nozioni base per l'uso di Intel Edison. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md).

Se non si ha ancora un kit, iniziare da [qui](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="lesson-1-configure-your-device"></a>Lezione 1: Configurare il dispositivo
![Diagramma di flusso della lezione 1](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

Questa lezione illustra come configurare Intel Edison con un sistema operativo, impostare l'ambiente di sviluppo e distribuire un'applicazione in Edison.

### <a name="configure-your-device"></a>Configurare il dispositivo
Configurare Intel Edison per il primo uso assemblando la scheda, fornendo l'alimentazione e installando lo strumento di configurazione nel proprio sistema operativo desktop per eseguire il flashing del firmware di Edison, impostarne la password e connetterlo al Wi-Fi.  

*Tempo previsto per il completamento: 30 minuti*

Passare a [Configurare il dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Get the tools
Scaricare gli strumenti e il software per compilare e distribuire la prima applicazione per Intel Edison.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
Clonare l'applicazione di esempio per il lampeggiamento da GitHub e usare gulp per distribuirla nella scheda Intel Edison. Questa applicazione di esempio fa lampeggiare il LED connesso alla scheda ogni due secondi.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Creare e distribuire l'applicazione per il lampeggiamento][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lezione 2: Creare l'hub IoT
![Diagramma di flusso della lezione 2](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

Questa lezione illustra come creare un account Azure gratuito, effettuare il provisioning dell'hub IoT di Azure e creare il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

### <a name="get-the-azure-tools"></a>Ottenere gli strumenti di Azure
Installare l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Ottenere gli strumenti di Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-intel-edison"></a>Creare l'hub IoT e registrare Intel Edison
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare l'hub IoT e registrare Intel Edison](iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lezione 3: Inviare messaggi da dispositivo a cloud
![Diagramma di flusso della lezione 3](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

Questa lezione illustra come inviare messaggi da Edison all'hub IoT. Si passa quindi alla creazione di un'app per le funzioni di Azure che preleva i messaggi in ingresso dall'hub IoT e li scrive nell'archiviazione tabelle di Azure.

Prima di iniziare questa lezione, completare la lezione 1 e la lezione 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud
Distribuire ed eseguire un'applicazione di esempio nel dispositivo Intel Edison che invia messaggi all'hub IoT.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Leggere i messaggi con salvataggio permanente in Archiviazione di Azure
Monitorare i messaggi da dispositivo a cloud mentre vengono scritti in Archiviazione di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi con salvataggio permanente in Archiviazione di Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lezione 4: Inviare messaggi da cloud a dispositivo
![Diagramma di flusso della lezione 4](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

Questa lezione illustra come inviare messaggi dall'hub IoT di Azure a Intel Edison. I messaggi controllano il comportamento di accensione e spegnimento del LED collegato a Edison. È stata preparata un'applicazione di esempio che permette di completare questa attività.

Prima di iniziare questa lezione, completare la lezione 1, la lezione 2 e la lezione 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo
L'applicazione di esempio della lezione 4 viene eseguita in Edison e monitora i messaggi in ingresso dall'hub IoT. Una nuova attività gulp invia messaggi a Edison dall'hub IoT per far lampeggiare il LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per eventuali problemi durante le lezioni, vedere l'articolo [Risoluzione dei problemi][troubleshooting] per cercare le soluzioni.
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]:iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md


<!--HONumber=Jan17_HO4-->


