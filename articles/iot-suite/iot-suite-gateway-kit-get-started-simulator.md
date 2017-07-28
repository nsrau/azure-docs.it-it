---
title: Connettere un gateway ad Azure IoT Suite con un Intel NUC | Microsoft Docs
description: Usare il kit gateway commerciale di Microsoft IoT e la soluzione di monitoraggio remoto preconfigurato. Usare il gateway Azure IoT Edge per connettersi alla soluzione di monitoraggio remoto, inviare dati di telemetria simulati al cloud e rispondere ai metodi richiamati dal dashboard della soluzione.
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
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 42e82793b73a941df08e438d9e04669a386a8368
ms.contentlocale: it-it
ms.lasthandoff: 06/08/2017

---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Collegare il gateway Azure IoT Edge alla soluzione preconfigurata di monitoraggio remoto e inviare dati di telemetria simulati

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

In questa esercitazione viene illustrato come usare Azure IoT Edge per simulare dati relativi alla temperatura e all'umidità da inviare alla soluzione preconfigurata di monitoraggio remoto. L'esercitazione usa:

- Azure IoT Edge per implementare un gateway di esempio.
- La soluzione preconfigurata di monitoraggio remoto IoT Suite come back-end basato su cloud.

## <a name="overview"></a>Panoramica

In questa esercitazione si completa la procedura seguente:

- Distribuire un'istanza della soluzione preconfigurata di monitoraggio remoto nella sottoscrizione di Azure. Questo passaggio distribuisce e configura automaticamente più servizi di Azure.
- Configurare il dispositivo di gateway Intel NUC per la comunicazione con il computer e la soluzione di monitoraggio remoto.
- Configurare il gateway IoT Edge per inviare dati di telemetria simulati che è possibile visualizzare nel dashboard della soluzione.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La soluzione di monitoraggio remoto esegue il provisioning di un set di servizi di Azure nella sottoscrizione di Azure. La distribuzione riflette un'architettura enterprise reale. Per evitare costi di consumo di Azure non necessari, eliminare l'istanza della soluzione preconfigurata in azureiotsuite.com al completamento. Se la soluzione preconfigurata occorre nuovamente, è possibile ricrearla facilmente. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Ripetere i passaggi precedenti per aggiungere un secondo dispositivo tramite un ID di dispositivo, ad esempio **device02**. Nell'esempio vengono inviati dati da due dispositivi simulati nel gateway alla soluzione di monitoraggio remoto.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Lo script di compilazione inserisce il modulo di IoT Edge libsimulator.so personalizzato nella cartella di compilazione.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurare ed eseguire il gateway IoT Edge

È ora possibile configurare il gateway IoT Edge per inviare dati di telemetria simulati al dashboard di monitoraggio remoto. Per altre informazioni sulla configurazione di un gateway e sui moduli IoT Edge, vedere [Concetti di Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> In questa esercitazione, si usa l'editor di testo standard `vi` in Intel NUC. Se non è stato usato `vi` in precedenza, è necessario completare un'esercitazione introduttiva, ad esempio [Unix - The vi Editor Tutorial][lnk-vi-tutorial] per familiarizzare con questo editor. In alternativa, è possibile installare l'editor [nano](https://www.nano-editor.org/) più semplice tramite il comando `smart install nano -y`.

Aprire il file di configurazione di esempio nell'editor **vi** usando il comando seguente:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Sostituire i segnaposti **deviceID** e **deviceKey** con gli ID e le chiavi per i due dispositivi creati in precedenza nella soluzione di monitoraggio remoto.

Salvare le modifiche.

Ora è possibile eseguire il gateway IoT Edge usando i comandi seguenti:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

Il gateway avvia Intel NUC e invia i dati di telemetria simulati alla soluzione di monitoraggio remoto:

![Il gateway IoT Edge genera dati di telemetria simulati][img-simulated telemetry]

Premere **CTRL-C** per uscire dal programma in qualsiasi momento.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Il gateway IoT Edge ora invia dati di telemetria simulati alla soluzione di monitoraggio remota. È possibile visualizzare i dati di telemetria nel dashboard della soluzione.

- Passare al dashboard della soluzione.
- Selezionare uno dei due dispositivi configurati nel gateway nell'elenco a discesa **Dispositivo da visualizzare**.
- I dati di telemetria dai dispositivi di gateway vengono visualizzati nel dashboard.

![Visualizzare i dati di telemetria dai dispositivi di gateway simulati][img-telemetry-display]

> [!WARNING]
> Se si lascia la soluzione di monitoraggio remoto in esecuzione nell'account Azure, verrà addebitato il tempo dell'esecuzione. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi). Al termine, eliminare la soluzione preconfigurata dall'account Azure.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per altri esempi e documentazione su Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started
