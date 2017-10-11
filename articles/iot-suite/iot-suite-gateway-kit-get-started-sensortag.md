---
title: Connettere un gateway ad Azure IoT Suite con un Intel NUC | Microsoft Docs
description: Usare il kit gateway commerciale di Microsoft IoT e la soluzione di monitoraggio remoto preconfigurato. Usare il gateway Azure IoT Edge per abilitare un dispositivo SensorTag da connettere alla soluzione di monitoraggio remoto, inviare dati di telemetria al cloud e rispondere ai metodi richiamati dal dashboard della soluzione.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: bda16be1094276fcecef1e708f9d7db307d94a89
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Collegare il gateway Azure IoT Edge alla soluzione preconfigurata di monitoraggio remoto e inviare dati di telemetria da un SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

In questa esercitazione viene illustrato come usare Azure IoT Edge per inviare dati relativi alla temperatura e all'umidità dal dispositivo SensorTag alla soluzione preconfigurata di monitoraggio remoto. Il SensorTag si connette al gateway Intel NUC tramite Bluetooth. L'esercitazione usa:

- Azure IoT Edge per implementare un gateway di esempio.
- La soluzione preconfigurata di monitoraggio remoto IoT Suite come back-end basato su cloud.

## <a name="overview"></a>Panoramica

In questa esercitazione si completa la procedura seguente:

- Distribuire un'istanza della soluzione preconfigurata di monitoraggio remoto nella sottoscrizione di Azure. Questo passaggio distribuisce e configura automaticamente più servizi di Azure.
- Configurare il dispositivo di gateway Intel NUC per la comunicazione con il computer e la soluzione di monitoraggio remoto.
- Configurare il gateway Intel NUC per ricevere i dati di telemetria da un dispositivo SensorTag e inviarli al dashboard di monitoraggio remoto.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[SensorTag BLE di Texas Instruments][lnk-sensortag]. Questa esercitazione consente di recuperare i dati di telemetria sul Bluetooth dal dispositivo SensorTag.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La soluzione di monitoraggio remoto esegue il provisioning di un set di servizi di Azure nella sottoscrizione di Azure. La distribuzione riflette un'architettura enterprise reale. Per evitare costi di consumo di Azure non necessari, eliminare l'istanza della soluzione preconfigurata in azureiotsuite.com al completamento. Se la soluzione preconfigurata occorre nuovamente, è possibile ricrearla facilmente. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Configurare la connettività Bluetooth

Configurare Bluetooth in Intel NUC per abilitare il dispositivo SensorTag a connettersi e a inviare dati di telemetria.

### <a name="find-the-mac-address-of-the-sensortag"></a>Trovare l'indirizzo MAC del SensorTag

1. Nella shell su Intel NUC, eseguire il comando seguente per sbloccare il servizio Bluetooth:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Eseguire i comandi seguenti per avviare il servizio Bluetooth su Intel NUC e immettere la shell di Bluetooth:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Eseguire il comando seguente per accendere il controller Bluetooth:

    ```bash
    power on
    ```

    Quando il controller è attivo, viene visualizzato un messaggio **Changing power on succeeded** (La modifica dell'accensione ha avuto esito positivo).

1. Eseguire il comando seguente per avviare l'analisi per i dispositivi Bluetooth circostanti:

    ```bash
    scan on
    ```

1. Premere il pulsante di accensione di SensorTag per renderlo individuabile. Il LED verde lampeggia.

1. Quando viene visualizzato un messaggio nella shell in base al quale il controller ha individuato il SensorTag, prendere nota dell'indirizzo MAC del dispositivo. L'indirizzo MAC è simile a **A0:E6:F8:B5:F6:00**. Quando si configura il gateway, più avanti nell'esercitazione, è necessario l'indirizzo MAC.

1. Eseguire il comando seguente per disattivare l'analisi del Bluetooth:

    ```bash
    scan off
    ```

1. Eseguire il comando seguente per verificare che è possibile connettersi al dispositivo SensorTag:

    ```bash
    connect <SensorTag MAC address>
    ```

    Se ci si connette correttamente, la shell visualizzerà il messaggio **Connessione riuscita** e stamperà informazioni sul dispositivo SensorTag. Se non è possibile connettersi, controllare che il SensorTag sia ancora acceso.

1. Ora è possibile disconnettersi da SensorTag e chiudere la shell del Bluetooth eseguendo i comandi seguenti:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Compilare il modulo IoT Edge personalizzato

È ora possibile compilare il modulo IoT Edge personalizzato che consente al gateway di inviare messaggi alla soluzione di monitoraggio remoto. Per altre informazioni sulla configurazione di un gateway e sui moduli IoT Edge, vedere [Concetti di Azure IoT Edge][lnk-gateway-concepts].

Scaricare il codice sorgente per i moduli personalizzati di IoT Edge da GitHub tramite i comandi seguenti:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Compilare il modulo personalizzato IoT Edge tramite i comandi seguenti:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Lo script di compilazione inserisce il modulo di IoT Edge libsensor2remotemonitoring.so personalizzato nella cartella di compilazione.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurare ed eseguire il gateway IoT Edge

È ora possibile configurare il gateway IoT Edge per inviare dati di telemetria dal dispositivo SensorTag al dashboard di monitoraggio remoto. Per altre informazioni sulla configurazione di un gateway e sui moduli IoT Edge, vedere [Concetti di Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> In questa esercitazione, si usa l'editor di testo standard `vi` in Intel NUC. Se non è stato usato `vi` in precedenza, è necessario completare un'esercitazione introduttiva, ad esempio [Unix - The vi Editor Tutorial][lnk-vi-tutorial] per familiarizzare con questo editor. In alternativa, è possibile installare l'editor [nano](https://www.nano-editor.org/) più semplice tramite il comando `smart install nano -y`.

Aprire il file di configurazione di esempio nell'editor **vi** usando il comando seguente:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Individuare le righe seguenti nella configurazione per il modulo IoTHub:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Sostituire i valori segnaposto con le informazioni dell'hub IoT create e salvate all'inizio di questa esercitazione. Il valore per IoTHubName è simile a **yourrmsolution37e08** e il valore per IoTSuffix è in genere **azure-devices.net**.

Individuare le righe seguenti nella configurazione per il modulo di mapping:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Sostituire il segnaposto **macAddress** con l'indirizzo MAC del SensorTag di cui si è preso nota in precedenza. Sostituire i segnaposti **deviceID** e **deviceKey** con gli ID e le chiavi per i due dispositivi creati in precedenza nella soluzione di monitoraggio remoto.

Individuare le righe seguenti nella configurazione per il modulo di SensorTag:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Sostituire il segnaposto **device\_mac\_address** con l'indirizzo MAC del SensorTag di cui si è preso nota in precedenza.

Salvare le modifiche.

Ora è possibile eseguire il gateway usando i comandi seguenti:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

Il gateway IoT Edge avvia Intel NUC e invia i dati di telemetria dal SensorTag alla soluzione di monitoraggio remoto:

![Il gateway IoT Edge invia i dati di telemetria dal SensorTag][img-telemetry]

Premere **CTRL-C** per uscire dal programma in qualsiasi momento.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Ora il gateway invia i dati di telemetria dal dispositivo SensorTag alla soluzione di monitoraggio remoto. È possibile visualizzare i dati di telemetria nel dashboard della soluzione. È anche possibile inviare comandi al dispositivo SensorTag tramite il gateway dal dashboard della soluzione.

- Passare al dashboard della soluzione.
- Selezionare il dispositivo configurato nel gateway che rappresenta il SensorTag nell'elenco a discesa **Dispositivo da visualizzare**.
- I dati di telemetria dal dispositivo SensorTag vengono visualizzati nel dashboard.

![Visualizzare i dati di telemetria dai dispositivi SensorTag][img-telemetry-display]

> [!WARNING]
> Se si lascia la soluzione di monitoraggio remoto in esecuzione nell'account Azure, verrà addebitato il tempo dell'esecuzione. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi). Al termine, eliminare la soluzione preconfigurata dall'account Azure.


## <a name="next-steps"></a>Passaggi successivi

Visitare il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per altri esempi e documentazione su Azure IoT.

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started