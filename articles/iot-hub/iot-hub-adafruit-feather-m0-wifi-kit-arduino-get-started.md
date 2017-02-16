---
title: Introduzione allo starter kit di Azure IoT per Adafruit Feather M0 WiFi | Documentazione Microsoft
description: Introduzione ad Adafruit Feather M0 WiFi e informazioni per la creazione dell&quot;hub IoT di Azure e la connessione all&quot;hub IoT di Adafruit Feather M0 WiFi
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: hub iot di azure, introduzione a internet delle cose, esercitazione per internet delle cose, internet delle cose per adafruit, introduzione ad arduino
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 8689ee35e880b1aa774b09bb194b43f280b2af79


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Introduzione alla scheda Arduino: Adafruit Feather M0 WiFi

Questa esercitazione illustra le nozioni di base per l'uso della scheda Arduino. Viene poi illustrato come connettere i dispositivi al cloud usando l'[hub IoT di Azure](iot-hub-what-is-iot-hub.md).

## <a name="lesson-1-configure-your-device"></a>Lezione 1: Configurare il dispositivo
![Diagramma di flusso della lezione 1][Lesson-1-end-to-end-diagram]

Questa lezione spiega come configurare la scheda Arduino con un sistema operativo, impostare l'ambiente di sviluppo e distribuire un'applicazione nella scheda Arduino.

### <a name="configure-your-device"></a>Configurare il dispositivo
Configurare la scheda Arduino per il primo utilizzo assemblando la scheda e fornendo l'alimentazione.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Configurare il dispositivo][configure-your-device].

### <a name="get-the-tools"></a>Get the tools
Scaricare gli strumenti e il software per compilare e distribuire la prima applicazione per la scheda Arduino.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Creare e distribuire l'applicazione per il lampeggiamento
Clonare l'applicazione Arduino di esempio per il lampeggiamento da GitHub e usare gulp per distribuirla nella scheda Arduino. Questa applicazione di esempio consente di far lampeggiare il LED sulla scheda n. 13 dell'interfaccia GPIO ogni due secondi.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Creare e distribuire l'applicazione per il lampeggiamento][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Lezione 2: Creare l'hub IoT
![Diagramma di flusso della lezione 2][lesson-2-end-to-end-diagram]

Questa lezione illustra come creare un account Azure gratuito, effettuare il provisioning dell'hub IoT di Azure e creare il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

### <a name="get-the-azure-tools"></a>Ottenere gli strumenti di Azure
Installare l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Ottenere gli strumenti di Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>Creare l'hub IoT e registrare la scheda Arduino
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare l'hub IoT e registrare la scheda Arduino][create-your-iot-hub-and-register-your-arduino-board].

## <a name="lesson-3-send-device-to-cloud-messages"></a>Lezione 3: Inviare messaggi da dispositivo a cloud
![Diagramma di flusso della lezione 3][lesson-3-end-to-end-diagram]

Questa lezione illustra come inviare messaggi dalla scheda Arduino all'hub IoT. Si passa quindi alla creazione di un'app per le funzioni di Azure che preleva i messaggi in ingresso dall'hub IoT e li scrive nell'archiviazione tabelle di Azure.

Prima di iniziare questa lezione, completare la lezione 1 e la lezione 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud
Distribuire ed eseguire un'applicazione di esempio nella scheda Arduino che invia messaggi all'hub IoT.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire un'applicazione di esempio per inviare messaggi da dispositivo a cloud][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Leggere i messaggi con salvataggio permanente in Archiviazione di Azure
Monitorare i messaggi da dispositivo a cloud mentre vengono scritti in Archiviazione di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi con salvataggio permanente in Archiviazione di Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Lezione 4: Inviare messaggi da cloud a dispositivo
![Diagramma di flusso della lezione 4][lesson-4-end-to-end-diagram]

Questa lezione illustra come inviare messaggi dall'hub IoT di Azure alla scheda Arduino. I messaggi controllano il comportamento di accensione e spegnimento del LED sulla scheda n. 13 dell'interfaccia GPIO. È stata preparata un'applicazione di esempio che permette di completare questa attività.

Prima di iniziare questa lezione, completare la lezione 1, la lezione 2 e la lezione 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo
L'applicazione di esempio della lezione 4 viene eseguita nella scheda Arduino e monitora i messaggi in ingresso dall'hub IoT. Una nuova attività gulp invia messaggi alla scheda Arduino dall'hub IoT per far lampeggiare il LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Sezione facoltativa: Modificare il comportamento di accensione e spegnimento del LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per eventuali problemi durante le lezioni, vedere l'articolo [Risoluzione dei problemi][troubleshooting] per cercare le soluzioni.

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md


<!--HONumber=Dec16_HO2-->


