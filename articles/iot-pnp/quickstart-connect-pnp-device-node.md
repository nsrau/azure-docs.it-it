---
title: Connettere il codice del dispositivo di esempio Plug and Play IoT (anteprima) all'hub IoT | Microsoft Docs
description: Con Node.js è possibile compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT (anteprima) che si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964788"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Guida introduttiva: Connettere un'applicazione dispositivo Plug and Play IoT (anteprima) di esempio all'hub IoT (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Questa guida di avvio rapido illustra come creare un'applicazione dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta per Node.js ed è inclusa in Azure IoT Hub Device SDK per Node.js. Uno sviluppatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza dover visualizzare il codice del dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida di avvio rapido, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Installare Azure IoT Explorer

Scaricare e installare la versione più recente di **Azure IoT Explorer** dalla pagina [repository](https://github.com/Azure/azure-iot-explorer/releases) dello strumento, selezionando il file con estensione MSI in "Assets" per l'aggiornamento più recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire questo comando per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prenderne nota per usarla in un secondo momento:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

In questa guida di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT Hub Device SDK per Node.js.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node) in questo percorso:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Il completamento dell'operazione può richiedere alcuni minuti.

## <a name="install-required-libraries"></a>Installare le librerie necessarie

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. In una finestra del terminale locale passare alla cartella del repository clonato e quindi alla cartella **/azure-iot-sdk-node/digitaltwins/samples/device/javascript**. Eseguire quindi il comando seguente per installare le librerie necessarie:

    ```cmd/sh
    npm install
    ```
1. Configurare la _stringa di connessione del dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

Eseguire un'applicazione di esempio nell'SDK per simulare un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT. Per eseguire l'applicazione di esempio, usare il comando seguente:

```cmd\sh
    node sample_device.js
```

Viene visualizzato l'output seguente, che indica che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà.

   ![Messaggi di conferma del dispositivo](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Per assicurarsi che lo strumento possa leggere le definizioni del modello di interfaccia dal dispositivo, selezionare **Settings** (Impostazioni). È possibile che **On the connected device** (Nel dispositivo connesso) sia già presente nelle configurazioni Plug and Play nel menu Settings (Impostazioni); in caso contrario, selezionare **+ Add module definition source** (+ Aggiungi origine definizione modulo) e quindi **On the connected device** (Nel dispositivo connesso) per aggiungerlo.

1. Tornare alla pagina di panoramica **Devices** (Dispositivi) per trovare l'identità del dispositivo creata in precedenza. Con l'applicazione del dispositivo ancora in esecuzione nel prompt dei comandi, verificare che lo **stato di connessione** del dispositivo in Azure IoT Explorer risulti _connesso_ (in caso contrario, fare clic su **Aggiorna** fino a quando non lo è). Selezionare il dispositivo per visualizzare altri dettagli.

1. Espandere l'interfaccia con ID **urn:contoso:com:EnvironmentalSensor:1** per visualizzare le primitive dell'interfaccia e Plug and Play IoT, ovvero le proprietà, i comandi e i dati di telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Procedura: Connettersi e interagire con un dispositivo Plug and Play IoT (anteprima)](howto-develop-solution.md)
