---
title: Connettere il codice Node.js di un dispositivo Plug and Play IoT di esempio con componenti a un hub IoT | Microsoft Docs
description: Compilare ed eseguire il codice Node.js di un dispositivo Plug and Play IoT di esempio che usa più componenti e si connette a un hub IoT. Usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate dal dispositivo all'hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: ea7b1ba159aa5d11a20ff565390ce0b24e38c1d2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577187"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-nodejs"></a>Esercitazione: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio con più componenti a un hub IoT (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Questa esercitazione illustra come creare un'applicazione di un dispositivo Plug and Play IoT di esempio con componenti, connetterla all'hub IoT e usare lo strumento Azure IoT Explorer per visualizzare le informazioni inviate all'hub. L'applicazione di esempio è scritta per Node.js ed è inclusa in Azure IoT Hub Device SDK per Node.js. Un integratore di soluzioni può usare lo strumento Azure IoT Explorer per conoscere le funzionalità di un dispositivo Plug and Play IoT senza doverne visualizzare il codice.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Per completare questa esercitazione, è necessario Node.js nel computer di sviluppo. È possibile scaricare la versione più recente consigliata per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Scaricare il codice

Se è stata completato l'argomento [Avvio rapido: Connettere un'applicazione di un dispositivo Plug and Play IoT di esempio in esecuzione in Windows a un hub IoT (Node)](quickstart-connect-device-node.md), il repository è già stato clonato.

Aprire un prompt dei comandi nella directory di propria scelta. Eseguire questo comando per clonare il repository GitHub di [Microsoft Azure IoT SDK per Node.js](https://github.com/Azure/azure-iot-sdk-node) in questo percorso:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installare le librerie necessarie

Usare l'SDK del dispositivo per compilare il codice di esempio incluso. L'applicazione creata simula un dispositivo Plug and Play con più componenti che si connette a un hub IoT. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. In una finestra del terminale locale passare alla cartella del repository clonato e quindi alla cartella */azure-iot-sdk-node/device/samples/pnp*. Eseguire quindi il comando seguente per installare le librerie necessarie:

```cmd/sh
npm install
```

Vengono installati i file npn pertinenti necessari per eseguire gli esempi nella cartella.

## <a name="review-the-code"></a>Esaminare il codice

Passare alla cartella *azure-iot-sdk-node\device\samples\pnp*.

La cartella *azure-iot-sdk-node\device\samples\pnp* contiene il codice di esempio per il dispositivo termoregolatore Plug and Play IoT.

Il codice del file *pnpTemperatureController.js* implementa un dispositivo termoregolatore Plug and Play IoT. Il modello implementato da questo esempio usa [più componenti](concepts-components.md). Il [file di modello DTDL (Digital Twins Definition Language) per il dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definisce i dati di telemetria, le proprietà e i comandi implementati.

Aprire il file *pnpTemperatureController.js* in un editor di codice a scelta. Il codice di esempio illustra come:

- Definire `modelId`, ovvero il DTMI per il dispositivo da implementare. Questo DTMI è definito dall'utente e deve corrispondere a quello del [modello DTDL del dispositivo termoregolatore](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

- Implementare i componenti definiti nel modello DTDL del dispositivo termoregolatore. I componenti di un dispositivo termoregolatore reale dovranno implementare queste due interfacce. Queste due interfacce sono già pubblicate in un repository centrale. In questo esempio, le due interfacce sono:

  - Thermostat
  - Informazioni sul dispositivo sviluppate da Azure

- Definire i nomi dei componenti. Questo esempio include due termostati e un componente di informazioni sul dispositivo.

- Definire il nome del comando. Si tratta dei comandi a cui il dispositivo risponde.

- Definire la costante `serialNumber`. `serialNumber` è un valore fisso in qualsiasi dispositivo specifico.

- Definire i gestori dei comandi.

- Definire le funzioni per inviare le risposte ai comandi.

- Definire le funzioni helper per registrare le richieste dei comandi.

- Definire una funzione helper per creare le proprietà.

- Definire un listener per gli aggiornamenti delle proprietà.

- Definire una funzione per inviare dati di telemetria da questo dispositivo. Sia i termostati che il componente predefinito inviano dati di telemetria. Questa funzione riceve il nome del componente come parametro.

- Definire una funzione `main` che:

  - Usa l'SDK del dispositivo per creare un client del dispositivo e connettersi all'hub IoT. Il dispositivo fornisce il valore di `modelId` in modo che possa essere identificato dall'hub IoT come dispositivo Plug and Play IoT.

  - Avvia l'ascolto di richieste dei comandi tramite la funzione `onDeviceMethod`. La funzione configura un listener per le richieste dei comandi provenienti dal servizio:

    - Il DTDL del dispositivo definisce i comandi `reboot` e `getMaxMinReport`.
    - La funzione `commandHandler` definisce il modo in cui il dispositivo risponde a un comando.

  - Avvia l'invio dei dati di telemetria usando `setInterval` e `sendTelemetry`.

  - Usa la funzione `helperCreateReportedPropertiesPatch` per creare le proprietà e `updateComponentReportedProperties` per aggiornarle.

  - Usa `desiredPropertyPatchListener` per restare in ascolto di aggiornamenti delle proprietà.

  - Disabilita tutti i listener e le attività ed esce dal ciclo quando si preme **Q** o **q**.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Per altre informazioni sulla configurazione di esempio, vedere il file [Leggimi dell'esempio](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

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
> [Guida per sviluppatori alla modellazione di Plug and Play IoT](concepts-developer-guide-device-csharp.md)
