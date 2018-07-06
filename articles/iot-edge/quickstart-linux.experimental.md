---
title: Simulare Azure IoT Edge in Linux | Microsoft Docs
description: Questa guida introduttiva descrive come distribuire codice precompilato in remoto in un dispositivo IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0e0d22b3363b00c81be5091fd12773f9e486c09e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099186"
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

In assenza di una sottoscrizione di Azure attiva, creare un [account gratuito][lnk-account] prima di iniziare.

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

Installare Moby, un runtime del contenitore, e i relativi comandi dell'interfaccia della riga di comando. 

   ```bash
   sudo apt-get install moby-engine
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

3. Aggiungere la stringa di connessione del dispositivo IoT Edge copiata durante la registrazione del dispositivo. Sostituire il valore della variabile **device_connection_string** copiato in precedenza in questa guida introduttiva.

4. Riavviare il daemon di sicurezza IoT Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Verificare che il daemon di sicurezza IoT Edge sia in esecuzione come servizio di sistema:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Osservare il daemon di sicurezza IoT Edge come servizio di sistema](./media/quickstart-linux/iotedged-running.png)

   È anche possibile visualizzare i log dal daemon di sicurezza IoT Edge eseguendo il comando seguente:

   ```bash
   journalctl -u iotedge
   ```

6. Visualizzare i moduli in esecuzione nel dispositivo: 

   ```bash
   sudo iotedge list
   ```
Dopo una disconnessione e un accesso, non è necessario usare *sudo* per il comando sopra citato.

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
Dopo una disconnessione e un accesso, non è necessario usare *sudo* per il comando sopra citato.

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

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. Se si vuole rimuovere le installazioni dal dispositivo, usare i comandi seguenti.  

Rimuovere il runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Eliminare i contenitori creati nel dispositivo. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Rimuovere il runtime del contenitore.

   ```bash
   sudo apt-get remove --purge moby
   ```

Quando l'hub Azure IoT o il dispositivo IoT Edge creato in questa guida introduttiva non è più necessario, è possibile eliminarlo nel portale di Azure. Passare alla pagina di panoramica dell'hub IoT e selezionare **Elimina**. 

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
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
