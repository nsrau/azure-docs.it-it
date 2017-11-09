---
title: Connettere Raspberry Pi ad Azure IoT Suite usando Node.js per supportare gli aggiornamenti del firmware | Microsoft Docs
description: Usare lo Starter Kit di Microsoft Azure IoT per Raspberry Pi 3 e Azure IoT Suite. Usare Node.js per connettere Raspberry Pi alla soluzione di monitoraggio remoto, inviare dati di telemetria dai sensori al cloud ed eseguire un aggiornamento del firmware remoto.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Connettere Raspberry Pi 3 alla soluzione di monitoraggio remoto e attivare gli aggiornamenti del firmware remoto usando Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Questa esercitazione illustra come usare lo Starter Kit di Microsoft Azure IoT per Raspberry Pi 3 per:

* Sviluppare un lettore di temperatura e umidità in grado di comunicare con il cloud.
* Abilitare ed eseguire un aggiornamento del firmware remoto per aggiornare l'applicazione client su Raspberry Pi.

L'esercitazione usa:

- Il sistema operativo Raspbian, il linguaggio di programmazione Node.js e Microsoft Azure IoT SDK per Node.js per implementare un dispositivo di esempio.
- La soluzione preconfigurata di monitoraggio remoto IoT Suite come back-end basato su cloud.

## <a name="overview"></a>Panoramica

In questa esercitazione si completa la procedura seguente:

- Distribuire un'istanza della soluzione preconfigurata di monitoraggio remoto nella sottoscrizione di Azure. Questo passaggio distribuisce e configura automaticamente più servizi di Azure.
- Configurare il dispositivo e i sensori per la comunicazione con il computer e la soluzione di monitoraggio remoto.
- Aggiornare il codice del dispositivo di esempio per eseguire la connessione alla soluzione di monitoraggio remoto e inviare i dati di telemetria che è possibile visualizzare nel dashboard della soluzione.
- Usare il codice del dispositivo di esempio per aggiornare l'applicazione client.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> La soluzione di monitoraggio remota esegue il provisioning di un set di servizi di Azure nella sottoscrizione di Azure. La distribuzione riflette un'architettura enterprise reale. Per evitare costi di consumo di Azure non necessari, eliminare l'istanza della soluzione preconfigurata in azureiotsuite.com al completamento. Se la soluzione preconfigurata occorre nuovamente, è possibile ricrearla facilmente. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

È ora possibile scaricare e configurare l'applicazione client di monitoraggio remoto su Raspberry Pi.

### <a name="install-nodejs"></a>Installare Node.js

Se non è già stato fatto, installare Node.js in Raspberry Pi. IoT SDK per Node.js richiede la versione 0.11.5 o successiva di Node.js. I passaggi seguenti mostrano come installare Node.js v6.10.2 su Raspberry Pi:

1. Usare il comando seguente per aggiornare Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Usare il comando seguente per scaricare i file binari di Node.js su Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Usare il comando seguente per installare i file binari:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Usare il comando seguente per verificare di aver installato correttamente Node.js v6.10.2:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Clonare i repository

Se non è già stato fatto, clonare i repository necessari eseguendo i comandi seguenti nel Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aggiornare la stringa di connessione del dispositivo

Aprire il file di configurazione di esempio nell'editor **nano** usando il comando seguente:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Sostituire i valori segnaposto con l'id del dispositivo e informazioni dell'hub IoT creati e salvati all'inizio di questa esercitazione.

Al termine, il contenuto del file deviceinfo dovrebbe essere simile all'esempio seguente:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Salvare le modifiche (**Ctrl+O**, **INVIO**) e chiudere l'editor (**Ctrl+X**).

## <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire i comandi seguenti per installare i pacchetti prerequisiti per l'esempio:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

È ora possibile eseguire il programma di esempio su Raspberry Pi. Immettere il comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

L'output seguente riporta un esempio dell'output visualizzato al prompt dei comandi su Raspberry Pi:

![Output dell'app Raspberry Pi][img-raspberry-output]

Premere **CTRL+C** per uscire dal programma in qualsiasi momento.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Nel dashboard della soluzione fare clic su **Dispositivi** per passare alla pagina **Dispositivi**. Selezionare Raspberry Pi in **Elenco dispositivi**. Quindi scegliere **Metodi**:

    ![Elencare i dispositivi nel dashboard][img-list-devices]

1. Nella pagina **Richiama metodo**, scegliere **InitiateFirmwareUpdate** nell'elenco a discesa **Metodo**.

1. Nel campo **FWPackageURI** immettere **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Questo file contiene l'implementazione della versione 2.0 del firmware.

1. Scegliere **InvokeMethod**. L'app su Raspberry Pi invia un riconoscimento al dashboard della soluzione. Avvia quindi il processo di aggiornamento del firmware scaricando la nuova versione del firmware:

    ![Visualizzare la cronologia del metodo][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Osservare il processo di aggiornamento del firmware

È possibile osservare il processo di aggiornamento del firmware durante l'esecuzione nel dispositivo e visualizzando le proprietà dichiarate nel dashboard della soluzione:

1. È possibile visualizzare lo stato di avanzamento del processo di aggiornamento su Raspberry Pi:

    ![Mostra l'avanzamento dell'aggiornamento][img-update-progress]

    > [!NOTE]
    > L'applicazione di monitoraggio remoto viene riavviata automaticamente al termine dell'aggiornamento. Usare il comando `ps -ef` per verificare che sia in esecuzione. Se si vuole terminare il processo, usare il comando `kill` con l'id del processo.

1. È possibile visualizzare lo stato dell'aggiornamento del firmware, come segnalato dal dispositivo, nel portale della soluzione. La schermata seguente mostra lo stato e la durata di ogni fase del processo di aggiornamento e la nuova versione del firmware:

    ![Mostra lo stato del processo][img-job-status]

    Se si passa nuovamente al dashboard, è possibile verificare che il dispositivo sta ancora inviando i dati di telemetria dopo l'aggiornamento del firmware.

> [!WARNING]
> Se si lascia la soluzione di monitoraggio remoto in esecuzione nell'account Azure, verrà addebitato il tempo dell'esecuzione. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi). Al termine, eliminare la soluzione preconfigurata dall'account Azure.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per altri esempi e documentazione su Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
