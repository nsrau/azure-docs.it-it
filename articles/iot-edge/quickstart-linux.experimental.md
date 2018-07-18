---
title: Simulare Azure IoT Edge in Linux | Microsoft Docs
description: Questa guida introduttiva descrive come distribuire codice precompilato in remoto in un dispositivo IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8ee43a1e3b448faae79a7e3086e2e1d639c341f2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611928"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Guida introduttiva: Distribuire il primo modulo IoT Edge in un dispositivo Linux x64

Azure IoT Edge consente di eseguire operazioni di analisi ed elaborazione dei dati direttamente nei dispositivi, invece di dover eseguire il push di tutti i dati nel cloud. Le esercitazioni su IoT Edge mostrano come distribuire tipi diversi di moduli, ma prima di tutto è necessario un dispositivo da testare. 

In questa guida introduttiva si apprende come:

1. Creare un hub IoT.
2. Registrare un dispositivo IoT Edge nell'hub IoT.
3. Avviare il runtime IoT Edge.
4. Distribuire in remoto un modulo in un dispositivo IoT Edge.

![Architettura dell'esercitazione][2]

Questa guida introduttiva trasforma il computer o la macchina virtuale Linux in un dispositivo IoT Edge. È quindi possibile distribuire un modulo dal portale di Azure nel dispositivo. Il modulo distribuito in questa guida introduttiva è un sensore simulato che genera dati di temperatura, umidità e pressione. Le altre esercitazioni su Azure IoT Edge si basano sulle operazioni eseguite qui tramite la distribuzione di moduli che analizzano dati simulati per ottenere informazioni aziendali accurate. 

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito][lnk-account] prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Questa guida introduttiva usa un computer Linux come dispositivo IoT Edge. Se non se ne ha uno disponibile per il test, seguire le istruzioni in [Creare una macchina virtuale Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md). 
* Non è necessario seguire i passaggi per installare ed eseguire il server Web. Dopo essersi connessi alla macchina virtuale, non è necessario proseguire.  
* Creare la macchina virtuale in un nuovo gruppo di risorse, che è possibile usare quando si creano le altre risorse di Azure per questa guida introduttiva. Assegnargli un nome riconoscibile, ad esempio *IoTEdgeResources*. 
* Non è necessaria una macchina virtuale di grandi dimensioni per testare IoT Edge. Sono sufficienti dimensioni come **B1ms**. 

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Iniziare la guida introduttiva creando l'hub IoT nel portale di Azure.
![Creare l'hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.
![Registrare un dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare e avviare il runtime di Azure IoT Edge nel dispositivo. 
![Registrare un dispositivo][5]

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza IoT Edge** si avvia a ogni avvio di un dispositivo Edge ed esegue l' avvio del dispositivo avviando l'agente IoT Edge. L'**agente IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo IoT Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 

Completare i passaggi seguenti nel computer o nella VM Linux preparata per questa guida introduttiva. 

### <a name="register-your-device-to-use-the-software-repository"></a>Registrare il dispositivo per usare il repository software

I pacchetti necessari per eseguire il runtime IoT Edge vengono gestiti in un repository software. Configurare il dispositivo IoT Edge per accedere a questo repository. 

I passaggi in questa sezione sono per i dispositivi che eseguono **Ubuntu 16.04**. Per accedere al repository software in altre versioni di Linux, vedere [Install the Azure IoT Edge runtime on Linux (x64)](how-to-install-iot-edge-linux.md) (Installare il runtime Azure IoT Edge su Linux - x64) o [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) (Installare il runtime Azure IoT Edge su Linux - ARM32v7/armhf).

1. Nel computer usato come dispositivo IoT Edge, installare la configurazione del repository.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Installare una chiave pubblica per accedere al repository.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Installare un runtime del contenitore

Il runtime IoT Edge è un set di contenitori e la logica distribuita nel dispositivo IoT Edge viene inclusa in pacchetti come contenitori. Preparare il dispositivo per questi componenti installando un runtime del contenitore.

Aggiornare **apt get**.

   ```bash
   sudo apt-get update
   ```

Installare **Moby**, un runtime del contenitore.

   ```bash
   sudo apt-get install moby-engine
   ```

Installare i comandi dell'interfaccia della riga di comando per Moby. 

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Installare e configurare il daemon di sicurezza IoT Edge

Il daemon di sicurezza viene installato come servizio di sistema in modo che il runtime IoT Edge venga avviato a ogni avvio del dispositivo. L'installazione include anche una versione di **hsmlib**, che permette al daemon di sicurezza di interagire con la sicurezza hardware del dispositivo. 

1. Scaricare e installare il daemon di sicurezza IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Aprire il file di configurazione di IoT Edge. Poiché è un file protetto, potrebbe essere necessario usare privilegi elevati per accedervi.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Aggiungere la stringa di connessione del dispositivo IoT Edge. Trovare la variabile **device_connection_string** e aggiornarne il valore con la stringa copiata dopo la registrazione del dispositivo.

4. Salvare e chiudere il file. 

   `CTRL + X`, `Y`, `Enter`

4. Riavviare il daemon di sicurezza di IoT Edge.

   ```bash
   sudo systemctl restart iotedge
   ```

5. Verificare che il daemon di sicurezza IoT Edge sia in esecuzione come servizio di sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Osservare il daemon di sicurezza IoT Edge come servizio di sistema](./media/quickstart-linux/iotedged-running.png)

   È anche possibile visualizzare i log dal daemon di sicurezza IoT Edge eseguendo il comando seguente:

   ```bash
   journalctl -u iotedge
   ```

6. Visualizzare i moduli in esecuzione nel dispositivo. 

   >[!TIP]
   >È necessario usare *sudo* per eseguire inizialmente i comandi `iotedge`. Disconnettere il computer ed eseguire di nuovo l'accesso per aggiornare le autorizzazioni e poter quindi eseguire i comandi `iotedge` senza privilegi elevati. 

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare un modulo nel dispositivo](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà dati di telemetria all'hub IoT.
![Registrare un dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni. 

Aprire il prompt dei comandi nel computer eseguendo ancora il dispositivo simulato. Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare tre moduli nel dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Visualizzare i messaggi inviati dal modulo tempSensor:

  ```bash
   sudo iotedge logs tempSensor -f 
   ```

Dopo una disconnessione e un accesso, non è necessario usare *sudo* per il comando sopra citato.

![Visualizzare i dati dal modulo](./media/quickstart-linux/iotedge-logs.png)

Il modulo del sensore temperatura può attendere di connettersi all'hub di IoT Edge se l'ultima riga visualizzata nel log è `Using transport Mqtt_Tcp_Only`. Provare a terminare il modulo e permettere all'agente IoT Edge di riavviarlo. È possibile terminare il modulo con il comando `sudo docker stop tempSensor`.

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo usando lo [strumento IoT Hub Explorer][lnk-iothub-explorer] o l'[estensione IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create e rimuovere il runtime IoT Edge dal dispositivo. 

### <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Se nel gruppo sono presenti risorse che si vuole conservare, eliminare solo le singole risorse che si vuole pulire. 

Per rimuovere un gruppo di risorse, seguire questa procedura: 

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.
2. Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse che contiene l'hub IoT. 
3. A destra del gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.
4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere di nuovo il nome del gruppo di risorse per confermare e fare clic su **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

### <a name="remove-the-iot-edge-runtime"></a>Rimuovere il runtime IoT Edge

Se si vuole rimuovere le installazioni dal dispositivo, usare i comandi seguenti.  

Rimuovere il runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Visualizzare tutti i contenitori.

   ```bash
   sudo docker ps -a
   ```

Eliminare i contenitori creati nel dispositivo dal runtime IoT Edge. Modificare il nome del contenitore tempSensor se gli è stato assegnato un nome diverso. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Rimuovere il runtime del contenitore.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto è disponibile un dispositivo simulato che genera dati non elaborati sul proprio ambiente. 

Questa esercitazione costituisce un prerequisito per tutte le esercitazioni su IoT Edge. È possibile continuare con una delle altre esercitazioni per scoprire in che modo trasformare questi dati in informazioni aziendali accurate tramite Azure IoT Edge nel dispositivo perimetrale.

> [!div class="nextstepaction"]
> [Filtrare dati dei sensori tramite una funzione di Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-account]: https://azure.microsoft.com/free
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
