---
title: Usare MQTT per creare un client del dispositivo Plug and Play IoT (anteprima) | Microsoft Docs
description: Usare direttamente il protocollo MQTT per creare un client del dispositivo Plug and Play IoT senza usare Azure IoT SDK per dispositivi
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535207"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Usare MQTT per sviluppare un client del dispositivo Plug and Play IoT (anteprima)

Se possibile, per creare i client del dispositivo Plug and Play IoT, è consigliabile usare una delle istanze di Azure IoT SDK per dispositivi. Tuttavia, in scenari come l'uso di un dispositivo con vincoli di memoria, potrebbe essere necessario usare una libreria MQTT per comunicare con l'hub IoT.

L'esempio di questa esercitazione usa la libreria MQTT [Eclipse Mosquitto](http://mosquitto.org/) e Visual Studio. I passaggi di questa esercitazione presuppongono che si usi Windows nel computer di sviluppo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione in Windows, installare il software seguente nell'ambiente Windows locale:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro **Sviluppo di applicazioni desktop con C++** quando si [installa](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Azure IoT Explorer](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la firma di accesso condiviso per connettere il dispositivo all'hub. Prendere nota di questa stringa, perché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Usare la stringa di connessione all'hub IoT per configurare lo strumento **Azure IoT Explorer**:

1. Avviare lo strumento **Azure IoT Explorer**.
1. Nella pagina **Impostazioni** incollare la stringa di connessione all'hub IoT nelle impostazioni delle **configurazioni dell'app**.
1. Selezionare **Save and Connect** (Salva e connetti).
1. Il dispositivo aggiunto in precedenza è incluso nell'elenco visualizzato nella pagina principale.

## <a name="clone-sample-repo"></a>Clonare il repository di esempio

Usare il comando seguente per clonare il repository di esempio in una posizione adatta nel computer locale:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Installare la libreria MQTT

Usare lo strumento `vcpkg` per scaricare e compilare la libreria Eclipse Mosquitto.

Usare il comando seguente per clonare il repository **Vcpkg** in una posizione adatta nel computer locale:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Usare i comandi seguenti per preparare l'ambiente `vcpkg`. Quando si esegue `vcpkg integrate install`, è necessario un prompt dei comandi con privilegi elevati:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Usare il comando seguente per scaricare e compilare la libreria Eclipse Mosquitto:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Eseguire la migrazione dell'esempio a Plug and Play IoT

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Esaminare il codice di esempio di Plug and Play IoT

Aggiornare il codice con i dettagli dell'hub IoT e del dispositivo prima di compilarlo ed eseguirlo.

Per visualizzare il codice di esempio in Visual Studio, aprire il file della soluzione *MQTTWin32.sln* nella cartella *IoTMQTTSample\src\Windows*.

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **TelemetryMQTTWin32** e scegliere **Imposta come progetto di avvio**.

Nel progetto **TelemetryMQTTWin32** aprire il file di origine **MQTT_Mosquitto.cpp**. Aggiornare le definizioni delle informazioni di connessione con i dettagli del dispositivo annotati in precedenza. Sostituire il segnaposto della stringa del token per:

* Identificatore `IOTHUBNAME` con il nome dell'hub IoT creato.
* Identificatore `DEVICEID` con il nome del dispositivo creato.
* Identificatore `PWD` con il valore della firma di accesso condiviso generato per il dispositivo.

Verificare che il codice funzioni correttamente, avviando Azure IoT Explorer, quindi restare in ascolto dei dati di telemetria.

Eseguire l'applicazione (CTRL+F5). Dopo un paio di secondi verrà visualizzato un output simile al seguente:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Output dell'applicazione di esempio MQTT":::

In Azure IoT Explorer è possibile verificare che il dispositivo non è di tipo Plug and Play IoT:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Dispositivo non di tipo Plug and Play IoT in Azure IoT Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Impostare il dispositivo come Plug and Play IoT

Il dispositivo Plug and Play IoT deve seguire una serie di semplici convenzioni. Se un dispositivo invia un ID modello quando si connette, diventa un dispositivo Plug and Play IoT.

In questo esempio si aggiunge un ID modello** al pacchetto di connessione MQTT. Passare l'ID modello come parametro di stringa di query in `USERNAME` e cambiare `api-version` in `2020-05-31-preview`:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Ricompilare ed eseguire l'esempio.

Il dispositivo gemello ora include l'ID modello:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Visualizzare l'ID modello in Azure IoT Explorer":::

È ora possibile esplorare il componente Plug and Play IoT:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Visualizzare i componenti in Azure IoT Explorer":::

È ora possibile modificare il codice del dispositivo per implementare i dati di telemetria, le proprietà e i comandi definiti nel modello. Per vedere un'implementazione di esempio del termostato usando la libreria Mosquitto, vedere [Uso di MQTT PnP con l'hub IoT di Azure senza IoT SDK in Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) su GitHub.

> [!NOTE]
> Per impostazione predefinita, una firma di accesso condiviso è valida solo per 60 minuti.

> [!NOTE]
>Il client usa il file del certificato radice `IoTHubRootCA_Baltimore.pem` per verificare l'identità dell'hub IoT a cui si connette.

### <a name="mqtt-topics"></a>Argomenti relativi a MQTT

Le definizioni seguenti riguardano gli argomenti MQTT usati dal dispositivo per inviare informazioni all'hub IoT. Per altre informazioni, vedere [Comunicare con l'hub IoT tramite il protocollo MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE` definisce l'argomento usato dal dispositivo per segnalare le interfacce implementate.
* `DEVICETWIN_PATCH_MESSAGE` definisce l'argomento usato dal dispositivo per segnalare gli aggiornamenti delle proprietà all'hub IoT.
* `DEVICE_TELEMETRY_MESSAGE` definisce l'argomento usato dal dispositivo per inviare dati di telemetria all'hub IoT.

Per altre informazioni su MQTT, vedere gli [esempi di MQTT per Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) nel repository GitHub.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come modificare un client di dispositivo MQTT per seguire le convenzioni di Plug and Play IoT. Per altre informazioni su Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Architettura](concepts-architecture.md)

Per altre informazioni sul supporto dell'hub IoT per il protocollo MQTT, vedere:

> [!div class="nextstepaction"]
> [Comunicare con l'hub IoT tramite il protocollo MQTT](../iot-hub/iot-hub-mqtt-support.md)
