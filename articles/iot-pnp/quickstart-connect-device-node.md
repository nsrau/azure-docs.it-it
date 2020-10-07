---
title: Connettere il codice del dispositivo Node.js di esempio Plug and Play IoT all'hub IoT di Azure | Microsoft Docs
description: Usare Node.js per compilare ed eseguire il codice del dispositivo di esempio Plug and Play IoT che si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: e9ab4f2639569537b7c5967235a926c567aca0d5
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576133"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-nodejs"></a>Avvio rapido: Connettere un'applicazione dispositivo Plug and Play IoT di esempio all'hub IoT (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Questa guida di avvio rapido illustra come creare un'applicazione di dispositivo Plug and Play IoT di esempio, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare i dati di telemetria inviati. L'applicazione di esempio è scritta in Node.js ed è inclusa in Azure IoT SDK per dispositivi per Node.js. Un generatore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa guida di avvio rapido, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Scaricare il codice

In questa guida di avvio rapido viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT Hub Device SDK per Node.js.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node) in questo percorso:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installare le librerie necessarie

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione compilata simula un dispositivo che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. In una finestra del terminale locale passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-node/device/samples/pnp*. Eseguire quindi il comando seguente per installare le librerie necessarie:

    ```cmd/sh
    npm install
    ```

1. Configurare la variabile di ambiente con la stringa di connessione del dispositivo annotata in precedenza:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Eseguire il dispositivo di esempio

Questo esempio implementa un semplice dispositivo termostato Plug and Play IoT. Il modello implementato da questo esempio non usa [componenti](concepts-components.md) di Plug and Play IoT. Il [file di modello DTDL per il dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definisce i dati di telemetria, le proprietà e i comandi implementati dal dispositivo.

Aprire il file _simple_thermostat.js_. In questo file è possibile osservare come:

1. Importare le interfacce necessarie.
1. Scrivere un gestore di aggiornamento delle proprietà e un gestore di comandi.
1. Gestire le patch delle proprietà desiderate e inviare i dati di telemetria.
1. Eseguire facoltativamente il provisioning del dispositivo con il servizio Device Provisioning (DPS) di Azure.

Nella funzione principale è possibile osservare come vengono combinate tutte queste operazioni:

1. Creare il dispositivo dalla stringa di connessione o eseguirne il provisioning usando il servizio Device Provisioning.
1. Usare l'opzione **modelID** per specificare il modello di dispositivo Plug and Play IoT.
1. Abilitare il gestore dei comandi.
1. Inviare i dati di telemetria dal dispositivo all'hub.
1. Ottenere i dispositivi gemelli e aggiornare le proprietà segnalate.
1. Abilitare il gestore di aggiornamento delle proprietà desiderato.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Eseguire l'applicazione di esempio per simulare un dispositivo Plug and Play IoT che invia dati di telemetria all'hub IoT. Per eseguire l'applicazione di esempio, usare il comando seguente:

```cmd\sh
node simple_thermostat.js
```

Viene visualizzato l'output seguente, che indica che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà.

![Messaggi di conferma del dispositivo](media/quickstart-connect-device-node/device-confirmation-node.png)

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come connettere un dispositivo Plug and Play IoT a un hub IoT. Per altre informazioni su come creare una soluzione che interagisce con i dispositivi Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Interagire con un dispositivo Plug and Play IoT connesso alla soluzione](quickstart-service-node.md)
