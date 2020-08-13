---
title: Connettere il codice Node.js di un dispositivo Plug and Play IoT (anteprima) di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice Node.js di un dispositivo Plug and Play IoT (anteprima) di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-javascript
ms.openlocfilehash: d26179ab82f29ce8f937f5b444463c1308d92047
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904055"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT (anteprima) di esempio con più componenti a un hub IoT (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti e interfaccia radice, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta per Node.js ed è inclusa in Azure IoT Hub Device SDK per Node.js. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Per interagire con il dispositivo di esempio nella seconda parte di questa esercitazione, usare lo strumento**Azure IoT Explorer**. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Eseguire il comando seguente per ottenere la _stringa di connessione dell'hub IoT_ per l'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> È anche possibile usare lo strumento Azure IoT Explorer per trovare la stringa di connessione dell'hub IoT.

Eseguire il comando seguente per ottenere la _stringa di connessione del dispositivo_ aggiunto all'hub. Prendere nota della stringa di connessione, poiché verrà usata più avanti in questa esercitazione:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Scaricare il codice

In questa esercitazione viene preparato un ambiente di sviluppo che è possibile usare per clonare e compilare Azure IoT Hub Device SDK per Node.js.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node) in questo percorso:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Il completamento dell'operazione può richiedere alcuni minuti.

## <a name="install-required-libraries"></a>Installare le librerie necessarie

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione creata simula un dispositivo Plug and Play con più componenti e un'interfaccia radice che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. In una finestra del terminale locale passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-node/device/samples/pnp*. Eseguire quindi il comando seguente per installare le librerie necessarie:

```cmd/sh
npm install
```

Vengono installati i file npn pertinenti necessari per eseguire gli esempi nella cartella.

1. Configurare la variabile di ambiente con la stringa di connessione del dispositivo annotata in precedenza:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Esaminare il codice

Passare alla cartella *azure-iot-sdk-node\device\samples\pnp*.

La cartella *azure-iot-sdk-node\device\samples\pnp* contiene il codice di esempio per il dispositivo termoregolatore Plug and Play IoT.

Il codice del file *pnpTemperatureController.js* implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

Aprire il file *pnpTemperatureController.js* in un editor di codice a scelta. Il codice di esempio illustra come:

1. Definire `modelId`, ovvero il DTMI per il dispositivo da implementare. Questo DTMI è definito dall'utente e deve corrispondere a quello del [modello DTDL del dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Implementare i componenti definiti nel modello DTDL del dispositivo termoregolatore. I componenti di un dispositivo termoregolatore reale dovranno implementare queste due interfacce. Queste due interfacce sono già pubblicate in un repository centrale. In questo esempio, le due interfacce sono:
  - Thermostat
  - Informazioni sul dispositivo sviluppate da Azure

3. Definire i nomi dei componenti. Questo esempio include due termostati e un componente di informazioni sul dispositivo.

4. Definire il nome del comando. Si tratta dei comandi a cui il dispositivo risponde.

5. Definire la costante `serialNumber`. `serialNumber` è un valore fisso in qualsiasi dispositivo specifico.

6. Definire i gestori dei comandi.

7. Definire le funzioni per inviare le risposte ai comandi.

8. Definire le funzioni helper per registrare le richieste dei comandi.

9. Definire una funzione helper per creare le proprietà.

10. Definire un listener per gli aggiornamenti delle proprietà.

11. Definire una funzione per inviare dati di telemetria da questo dispositivo. Sia i termostati che il componente radice inviano dati di telemetria. Questa funzione riceve il nome del componente come parametro.

12. Definire una funzione `main` che:

    1. Usa l'SDK del dispositivo per creare un client del dispositivo e connettersi all'hub IoT. Il dispositivo fornisce il valore di `modelId` in modo che possa essere identificato dall'hub IoT come dispositivo Plug and Play IoT.

    1. Avvia l'ascolto di richieste dei comandi tramite la funzione `onDeviceMethod`. La funzione configura un listener per le richieste dei comandi provenienti dal servizio:
        - Il DTDL del dispositivo definisce i comandi `reboot` e `getMaxMinReport`.
        - La funzione `commandHandler` definisce il modo in cui il dispositivo risponde a un comando.

    1. Avvia l'invio dei dati di telemetria usando `setInterval` e `sendTelemetry`.

    1. Usa la funzione `helperCreateReportedPropertiesPatch` per creare le proprietà e `updateComponentReportedProperties` per aggiornarle.

    1. Usa `desiredPropertyPatchListener` per restare in ascolto di aggiornamenti delle proprietà.

    1. Disabilita tutti i listener e le attività ed esce dal ciclo quando si preme **Q** o **q**.

Dopo avere esaminato il codice, usare il comando seguente per eseguire l'esempio:

```cmd\sh
node pnpTemperatureController.js
```

Viene visualizzato l'output seguente, che indica che il dispositivo ha iniziato a inviare dati di telemetria all'hub ed è ora pronto a ricevere comandi e aggiornamenti delle proprietà.

![Messaggi di conferma del dispositivo](media/tutorial-multiple-components-node/multiple-component.png)

Lasciare l'esempio in esecuzione mentre si completano i passaggi successivi.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usare Azure IoT Explorer per convalidare il codice

Dopo l'avvio dell'esempio client del dispositivo, usare lo strumento Azure IoT Explorer per verificarne il funzionamento.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT con componenti a un hub IoT. Per altre informazioni sui modelli di dispositivo Plug and Play IoT, vedere:

> [!div class="nextstepaction"]
> [Guida per gli sviluppatori alla modellazione di Plug and Play IoT (anteprima)](concepts-developer-guide.md)
