---
title: Eseguire un&quot;applicazione di esempio per ricevere messaggi da cloud a dispositivo dall&quot;hub IoT di Azure | Documentazione Microsoft
description: "Un&quot;applicazione di esempio viene eseguita in Edison e monitora i messaggi in ingresso dall&quot;hub IoT. Una nuova attività gulp invia messaggi a Edison dall&quot;hub IoT per far lampeggiare il LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: led di controllo arduino dal web, led di controllo arduino tramite web
ms.assetid: bc738bf6-e38d-4024-82d7-39b6c2d4bacb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: d492fa295237f6ff333aefb2181975e8af948723
ms.openlocfilehash: 57f794c85f7feab95a0a22006c49b29bc6cb1384


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Eseguire un'applicazione di esempio per ricevere messaggi da cloud a dispositivo
Questo articolo illustra come distribuire un'applicazione di esempio in Intel Edison. L'applicazione di esempio monitora i messaggi in ingresso provenienti dall'hub IoT. È anche possibile eseguire un'attività gulp nel computer per inviare messaggi a Edison dall'hub IoT. Alla ricezione dei messaggi, l'applicazione di esempio fa lampeggiare il LED. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
* Connettere l'applicazione di esempio all'hub IoT.
* Distribuire ed eseguire l'applicazione di esempio.
* Inviare messaggi dall'hub IoT a Edison per far lampeggiare il LED.

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Contenuto dell'articolo:
* Come monitorare i messaggi in ingresso dall'hub IoT.
* Come inviare messaggi da cloud a dispositivo dall'hub IoT a Edison.

## <a name="what-you-need"></a>Elementi necessari
* Intel Edison, impostato per l'uso. Per informazioni sull'impostazione di Edison, vedere l'articolo su come [configurare il dispositivo][configure-your-device].
* Un hub IoT creato nella propria sottoscrizione di Azure. Per informazioni sulla creazione dell'hub IoT, vedere l'articolo su come [creare l'hub IoT di Azure][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Connettere l'applicazione di esempio all'hub IoT
1. Assicurarsi che sia aperta la cartella `iot-hub-node-edison-getting-started` del repository. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```

   Il file nella sottocartella `app` è il file di origine chiave che contiene il codice per il monitoraggio dei messaggi in ingresso dall'hub IoT. La funzione `blinkLED` fa lampeggiare il LED.

   ![Struttura del repository nell'applicazione di esempio][repo-structure]
2. Inizializzare il file di configurazione usando i comandi seguenti:

   ```bash
   npm install
   gulp init
   ```

   Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione][create-an-azure-function-app-and-storage-account] è stata completata nello stesso computer, tutte le configurazioni vengono ereditate. È quindi possibile passare direttamente all'attività di distribuzione ed esecuzione dell'applicazione di esempio. Se la procedura descritta in [Creare un'app per le funzioni di Azure e un account di archiviazione][create-an-azure-function-app-and-storage-account] è stata completata in un computer diverso, è necessario sostituire i segnaposto nel file `config-edison.json`. Il file `config-edison.json` si trova nella sottocartella della cartella principale.

   ![Contenuto del file config-edison.json](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * Sostituire **[device hostname or IP address]** con l'indirizzo IP del dispositivo annotato quando è stato configurato il dispositivo.
   * Sostituire **[IoT device connection string]** con la stringa di connessione del dispositivo che si ottiene eseguendo il comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Sostituire **[IoT hub connection string]** con la stringa di connessione dell'hub IoT che si ottiene eseguendo il comando `az iot hub show-connection-string --name {my hub name}`.

## <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio in Edison eseguendo questi comandi:

```bash
gulp deploy && gulp run
```

Il comando gulp distribuisce l'applicazione di esempio in Edison, quindi esegue l'applicazione in Edison e un'attività separata nel computer host per inviare 20 messaggi di lampeggiamento dall'hub IoT a Edison.

Non appena viene eseguita, l'applicazione di esempio rimane in ascolto di messaggi provenienti dall'hub IoT. Nel frattempo, l'attività gulp invia vari messaggi di lampeggiamento dall'hub IoT a Edison. Per ogni messaggio di lampeggiamento ricevuto da Edison, l'applicazione di esempio chiama la funzione `blinkLED` per far lampeggiare il LED.

Durante l'invio dei 20 messaggi dall'hub IoT a Edison da parte dell'attività gulp, il LED dovrebbe lampeggiare ogni due secondi. L'ultimo è un messaggio "stop" che arresta l'esecuzione dell'applicazione.

![Applicazione di esempio con comando gulp e messaggi di lampeggiamento][gulp-command-and-blink-messages]

## <a name="summary"></a>Riepilogo
Sono stati inviati messaggi dall'hub IoT a Edison per far lampeggiare il LED. L'attività successiva è facoltativa: modificare il comportamento di accensione e spegnimento del LED.

## <a name="next-steps"></a>Passaggi successivi
[Modificare il comportamento di accensione e spegnimento del LED][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md


<!--HONumber=Dec16_HO2-->


