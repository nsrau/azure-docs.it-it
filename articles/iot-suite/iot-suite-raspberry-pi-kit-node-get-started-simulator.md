---
title: Connettere Raspberry Pi ad Azure IoT Suite usando Node.js con telemetria simulata | Microsoft Docs
description: Usare lo Starter Kit di Microsoft Azure IoT per Raspberry Pi 3 e Azure IoT Suite. Usare Node.js per la connessione di Raspberry Pi alla soluzione di monitoraggio remoto, per inviare dati di telemetria simulata al cloud e per rispondere ai metodi richiamati dal dashboard della soluzione.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 3d41a3665ffb2217ecbe1ad53853f0e9d30fac34
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017

---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>Connettere Raspberry Pi 3 alla soluzione di monitoraggio remoto e inviare la telemetria simulata usando Node.js

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

Questa esercitazione illustra come usare Raspberry Pi 3 per simulare i dati relativi alla temperatura e all'umidità dati da inviare al cloud. L'esercitazione usa:

- Il sistema operativo Raspbian, il linguaggio di programmazione Node.js e Microsoft Azure IoT SDK per Node.js per implementare un dispositivo di esempio.
- La soluzione preconfigurata di monitoraggio remoto IoT Suite come back-end basato su cloud.

[!INCLUDE [iot-suite-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La soluzione di monitoraggio remoto esegue il provisioning di un set di servizi di Azure nella sottoscrizione di Azure. La distribuzione riflette un'architettura enterprise reale. Per evitare costi di consumo di Azure non necessari, eliminare l'istanza della soluzione preconfigurata in azureiotsuite.com al completamento. Se la soluzione preconfigurata occorre nuovamente, è possibile ricrearla facilmente. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi).

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-raspberry-pi-kit-prepare-pi-simulator.md)]

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

Se non è già stato fatto, clonare i repository necessari eseguendo i comandi seguenti in un terminale in Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Aggiornare la stringa di connessione del dispositivo

Aprire il file origine di esempio nell'editor **nano** usando il comando seguente:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Trovare la riga:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Sostituire i valori segnaposto con il dispositivo e le informazioni dell'hub IoT creati e salvati all'inizio di questa esercitazione. Salvare le modifiche (**Ctrl+O**, **INVIO**) e chiudere l'editor (**Ctrl+X**).

## <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire i comandi seguenti per installare i pacchetti prerequisiti per l'esempio:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

È ora possibile eseguire il programma di esempio su Raspberry Pi. Immettere il comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

L'output seguente riporta un esempio dell'output visualizzato al prompt dei comandi su Raspberry Pi:

![Output dell'app Raspberry Pi][img-raspberry-output]

Premere **CTRL-C** per uscire dal programma in qualsiasi momento.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Passaggi successivi

Visitare il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per altri esempi e documentazione su Azure IoT.

[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

