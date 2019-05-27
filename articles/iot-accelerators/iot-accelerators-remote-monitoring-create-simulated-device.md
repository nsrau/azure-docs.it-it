---
title: Simulazione di dispositivi con Monitoraggio remoto IoT - Azure | Microsoft Docs
description: Questa guida alle procedure mostra come usare il simulatore di dispositivi con l'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5044f8b85e59911633a4ffab509efc000948144a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832590"
---
# <a name="create-and-test-a-new-simulated-device"></a>Creare e testare un nuovo dispositivo simulato

L'acceleratore di soluzione Monitoraggio remoto consente di definire dispositivi simulati personalizzati. Questo articolo illustra come definire un nuovo dispositivo lampadina simulato e quindi testarlo in locale. L'acceleratore di soluzione include dispositivi simulati, ad esempio refrigeratori e veicoli. Tuttavia, è possibile definire dispositivi simulati personalizzati per testare le soluzioni IoT prima di distribuire i dispositivi reali.

> [!NOTE]
> L'articolo descrive le modalità secondo cui usare i dispositivi simulati ospitati nel servizio di simulazione del dispositivo. Se si vuole creare un dispositivo reale, vedere [Connettere il dispositivo all'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-connecting-devices.md).

Questa guida alle procedure illustra come personalizzare il microservizio di simulazione dispositivi. Questo microservizio fa parte dell'acceleratore di soluzione Monitoraggio remoto. Per illustrare le funzionalità di simulazione di dispositivi, questa guida alle procedure usa due scenari nell'applicazione IoT Contoso:

Nel primo scenario si aggiunge un nuovo tipo di telemetria al dispositivo **Chiller** esistente di Contoso.

Nel secondo scenario Contoso intende testare un nuovo dispositivo lampadina intelligente. Per eseguire i test, si crea un nuovo dispositivo simulato con le caratteristiche seguenti:

*Proprietà*

| NOME                     | Valori                      |
| ------------------------ | --------------------------- |
| Colore                    | White (Bianco), Red (Rosso), Blue (Blu)            |
| Brightness (Luminosità)               | Da 0 a 100                    |
| Estimated remaining life (Durata rimanente stimata) | Conto alla rovescia da 10.000 ore |

*Telemetria*

La tabella seguente mostra i dati trasmessi dalla lampadina al cloud come flusso di dati:

| NOME   | Valori      |
| ------ | ----------- |
| Stato | "on", "off" |
| Temperatura | Gradi F |
| online | true, false |

> [!NOTE]
> Il valore di dati di telemetria **online** è obbligatorio per tutti i tipi simulati.

*Metodi*

La tabella seguente mostra le azioni supportate dal nuovo dispositivo:

| NOME        |
| ----------- |
| Switch on (Accendi)   |
| Switch off (Spegni)  |

*Stato iniziale*

La tabella seguente mostra lo stato iniziale del dispositivo:

| NOME                     | Valori |
| ------------------------ | -------|
| Initial color (Colore iniziale)            | Bianco  |
| Initial brightness (Luminosità iniziale)       | 75     |
| Initial remaining life (Durata rimanente iniziale)   | 10,000 |
| Initial telemetry status (Stato telemetria iniziale) | "on"   |
| Initial telemetry temperature (Temperatura telemetria iniziale) | 200   |

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica, sono necessari gli elementi seguenti:

* Visual Studio Code. È possibile [scaricare Visual Studio Code per Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. È possibile scaricare [.NET Core per Mac, Linux e Windows](https://www.microsoft.com/net/download).
* [C# per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. È possibile scaricare [Postman per Mac, Windows o Linux](https://www.getpostman.com/apps).
* Un [hub IoT distribuito nella sottoscrizione di Azure in uso](../../articles/iot-hub/iot-hub-create-through-portal.md). Per completare la procedura descritta in questa guida, è necessaria la stringa di connessione dell'hub IoT. È possibile ottenere la stringa di connessione nel portale di Azure.
* Un database Cosmos DB che usi l'API SQL e che sia configurato per [coerenza assoluta](../../articles/cosmos-db/how-to-manage-database-account.md). Per completare la procedura descritta in questa guida, è necessaria la stringa di connessione del database Cosmos DB. È possibile ottenere la stringa di connessione nel portale di Azure.

## <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo

Completare le attività seguenti per preparare l'ambiente di sviluppo:

* Scaricare il codice sorgente per il microservizio di simulazione dispositivi.
* Scaricare il codice sorgente per il microservizio di adattatore di archiviazione.
* Eseguire il microservizio di adattatore di archiviazione in locale.

Le istruzioni presenti in questo articolo presuppongono che si usi Windows. Se si usa un altro sistema operativo,può essere necessario modificare alcuni percorsi di file e alcuni comandi in base all'ambiente.

### <a name="download-the-microservices"></a>Scaricare i microservizi

Scaricare e decomprimere i [microservizi di monitoraggio remoto](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) da GitHub in un percorso appropriato nel computer locale. L'articolo presuppone che il nome di questa cartella sia **remote-monitoring-services-dotnet-master**.

Scaricare e decomprimere il [microservizio di simulazione dispositivi](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) da GitHub in un percorso appropriato nel computer locale. L'articolo presuppone che il nome di questa cartella sia **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Eseguire il microservizio di adattatore di archiviazione

Aprire la cartella **remote-monitoring-services-dotnet-master\storage-adapter** in Visual Studio Code. Fare clic su un pulsante **Ripristina** per risolvere le dipendenze non risolte.

Aprire il **storage-adapter/WebService/appsettings.ini** del file e assegnare la stringa di connessione di Cosmos DB per il **documentDBConnectionString** variabile.

Per eseguire il microservizio in locale, fare clic su **Debug > Avvia debug**.

Nella finestra **Terminale** in Visual Studio Code viene visualizzato un output dal microservizio in esecuzione che include un URL per il controllo dell'integrità del servizio Web: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Quando si passa a questo indirizzo, lo stato deve essere "OK: Attivo e".

Lasciare il microservizio di adattatore di archiviazione in esecuzione nell'istanza corrente di Visual Studio Code fino al completamento dei passaggi successivi.

## <a name="modify-the-chiller"></a>Modificare il refrigeratore

In questa sezione si aggiunge un nuovo tipo di telemetria **Internal Temperature** al tipo di dispositivo **Chiller** esistente:

1. Creare una nuova cartella **C:\temp\devicemodels** nel computer locale.

1. Copiare i file seguenti nella nuova cartella dalla copia scaricata del microservizio di simulazione dispositivi:

    | `Source` | Destination |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Aprire il file **C:\temp\devicemodels\chiller-01.json**.

1. Nella sezione **InitialState**aggiungere le due definizioni seguenti:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Nella matrice **Telemetry** aggiungere la definizione seguente:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Salvare il file **C:\temp\devicemodels\chiller-01.json**.

1. Aprire il file **C:\temp\devicemodels\scripts\chiller-01-state.js**.

1. Aggiungere i campi seguenti alla variabile **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aggiornare la funzione **main** nel modo seguente:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Salvare il file **C:\temp\devicemodels\scripts\chiller-01-state.js**.

## <a name="create-the-lightbulb"></a>Creare la lampadina

In questa sezione viene definito un nuovo tipo di dispositivo **Lightbulb**:

1. Creare un file **C:\temp\devicemodels\lightbulb-01.json** e aggiungere il contenuto seguente:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Salvare le modifiche in **C:\temp\devicemodels\lightbulb-01.json**.

1. Creare un file **C:\temp\devicemodels\scripts\lightbulb-01-state.js** e aggiungere il contenuto seguente:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Salvare le modifiche in **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Creare un file **C:\temp\devicemodels\scripts\SwitchOn-method.js** e aggiungere il contenuto seguente:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Salvare le modifiche in **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Creare un file **C:\temp\devicemodels\scripts\SwitchOff-method.js** e aggiungere il contenuto seguente:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Salvare le modifiche in **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

È stata creata una versione personalizzata del tipo di dispositivo **Chiller** ed è stato creato un nuovo tipo di dispositivo **Lightbulb**.

## <a name="test-the-devices"></a>Testare i dispositivi

In questa sezione vengono testati i tipi di dispositivi creati in locale nelle sezioni precedenti.

### <a name="run-the-device-simulation-microservice"></a>Eseguire il microservizio di simulazione dispositivi

Aprire la cartella **device-simulation-dotnet-master** scaricata da GitHub in una nuova istanza di Visual Studio Code. Fare clic su un pulsante **Ripristina** per risolvere le dipendenze non risolte.

Aprire il **WebService/appsettings.ini** del file e assegnare la stringa di connessione di Cosmos DB per il **documentdb_connstring** variabile e anche modificare le impostazioni come segue:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Per eseguire il microservizio in locale, fare clic su **Debug > Avvia debug**.

Nella finestra **Terminale** di Visual Studio Code viene visualizzato l'output del microservizio in esecuzione.

Lasciare il microservizio di simulazione dispositivi in esecuzione nell'istanza corrente di Visual Studio Code fino al completamento dei passaggi successivi.

### <a name="set-up-a-monitor-for-device-events"></a>Configurare un monitor per gli eventi del dispositivo

In questa sezione si usa l'interfaccia della riga di comando di Azure per configurare un monitor degli eventi per visualizzare i dati di telemetria inviati dai dispositivi connessi all'hub IoT.

Lo script seguente presuppone che il nome dell'hub IoT sia **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lasciare il monitor degli eventi in esecuzione mentre si testano i dispositivi simulati.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Creare una simulazione con il tipo di dispositivo refrigeratore aggiornato

In questa sezione si usa lo strumento Postman per richiedere al microservizio di simulazione dispositivi di eseguire una simulazione tramite il tipo di dispositivo refrigeratore aggiornato. Postman è uno strumento che consente di inviare richieste REST a un servizio Web. I file di configurazione di Postman necessari sono nella copia locale del repository **device-simulation-dotnet**.

Per configurare Postman

1. Aprire Postman nel computer locale.

1. Fare clic su **File > Importa**. Fare clic su **Scegli file**.

1. Passare alla cartella **device-simulation-dotnet-master/docs/postman**. Selezionare **Azure IoT Device Simulation solution accelerator.postman_collection** e **Azure IoT Device Simulation solution accelerator.postman_environment** e quindi fare clic su **Apri**.

1. Espandere **Azure IoT Device Simulation solution accelerator** (Acceleratore soluzione di simulazione dispositivi IoT Azure) alle richieste che è possibile inviare.

1. Fare clic su **No Environment** (nessun ambiente) e selezionare **Azure IoT Device Simulation solution accelerator** (Acceleratore soluzione di simulazione dispositivi IoT Azure).

Sono ora disponibili una raccolta e un ambiente caricati nell'area di lavoro di Postman che è possibile usare per interagire con il microservizio di simulazione dispositivi.

Per configurare ed eseguire la simulazione

1. Nella raccolta Postman selezionare **Create modified chiller simulation** (Crea simulazione refrigeratore modificata) e quindi fare clic su **Send** (Invia). Questa richiesta crea quattro istanze del tipo di dispositivo refrigeratore simulato.

1. L'output del monitor degli eventi nell'interfaccia della riga di comando di Azure mostra i dati di telemetria dei dispositivi simulati, inclusi i nuovi valori **internal_temperature**.

Per arrestare la simulazione, selezionare la richiesta **Stop simulation** (Arresta simulazione) in Postman e quindi fare clic su **Send** (Invia).

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Creare una simulazione con il tipo di dispositivo lampadina

In questa sezione si usa lo strumento Postman per richiedere al microservizio di simulazione dispositivi di eseguire una simulazione tramite il tipo di dispositivo lampadina. Postman è uno strumento che consente di inviare richieste REST a un servizio Web.

Per configurare ed eseguire la simulazione

1. Nella raccolta Postman selezionare **Create lightbulb simulation** (Crea simulazione lampadina) e quindi fare clic su **Send** (Invia). Questa richiesta crea due istanze del tipo di dispositivo lampadina simulata.

1. L'output del monitor degli eventi nell'interfaccia della riga di comando di Azure mostra i dati di telemetria delle lampadine simulate.

Per arrestare la simulazione, selezionare la richiesta **Stop simulation** (Arresta simulazione) in Postman e quindi fare clic su **Send** (Invia).

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile arrestare i due microservizi in esecuzione in locale nelle istanze di Visual Studio Code (**Debug > Arresta debug**).

Se le istanze dell'hub IoT e di Cosmos DB non sono più necessarie, eliminarle dalla sottoscrizione di Azure per evitare eventuali addebiti.

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha illustrato come creare tipi di dispositivi simulati personalizzati e testarli eseguendo il microservizio di simulazione dispositivi in locale.

Il passaggio successivo consigliato è apprendere come distribuire i tipi di dispositivi simulati personalizzati nell'[acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
