---
title: Simulazione dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa esercitazione mostra come usare il simulatore di dispositivi con la soluzione preconfigurata di monitoraggio remoto.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 732ec45003481b0e2f2eca03b6ae13772d325ef1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Testare la soluzione con dispositivi simulati

Questa esercitazione mostra come personalizzare il microservizio del simulatore di dispositivi con la soluzione preconfigurata di monitoraggio remoto. Per illustrare le funzionalità del simulatore di dispositivi, questa esercitazione usa due scenari nell'applicazione IoT Contoso.

Nel primo scenario Contoso vuole testare un nuovo dispositivo lampadina intelligente. Per eseguire i test, si crea un nuovo dispositivo simulato con le caratteristiche seguenti:

*Proprietà*

| Nome                     | Valori                      |
| ------------------------ | --------------------------- |
| Colore                    | White (Bianco), Red (Rosso), Blue (Blu)            |
| Brightness (Luminosità)               | Da 0 a 100                    |
| Estimated remaining life (Durata rimanente stimata) | Conto alla rovescia da 10.000 ore |

*Telemetria*

La tabella seguente mostra i dati trasmessi dalla lampadina al cloud come flusso di dati:

| Nome   | Valori      |
| ------ | ----------- |
| Stato | "on", "off" |

*Metodi*

La tabella seguente mostra le azioni supportate dal nuovo dispositivo:

| Nome        |
| ----------- |
| Switch on (Accendi)   |
| Switch off (Spegni)  |

*Stato iniziale*

La tabella seguente mostra lo stato iniziale del dispositivo:

| Nome                     | Valori |
| ------------------------ | -------|
| Initial color (Colore iniziale)            | Bianco  |
| Initial brightness (Luminosità iniziale)       | 75     |
| Initial remaining life (Durata rimanente iniziale)   | 10.000 |
| Initial telemetry status (Stato telemetria iniziale) | "on"   |

Nel secondo scenario si aggiunge un nuovo tipo di telemetria al dispositivo **Chiller** (Refrigeratore) esistente di Contoso.

Questa esercitazione mostra come usare il simulatore di dispositivi con la soluzione preconfigurata di monitoraggio remoto:

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Creare un nuovo tipo di dispositivo
> * Simulare il comportamento del dispositivo personalizzato
> * Aggiungere un nuovo tipo di dispositivo al dashboard
> * Inviare dati di telemetria personalizzati da un tipo di dispositivo esistente

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Se la soluzione di monitoraggio remoto non è stata ancora distribuita, è necessario completare l'esercitazione [Distribuire la soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>Servizio di simulazione di dispositivi

Il servizio di simulazione di dispositivi nella soluzione preconfigurata consente di apportare modifiche ai tipi di dispositivi simulati predefiniti e di creare nuovi tipi di dispositivi simulati. È possibile usare i tipi di dispositivi personalizzati per testare il comportamento della soluzione di monitoraggio remoto prima di connettere i dispositivi fisici alla soluzione.

## <a name="create-a-simulated-device-type"></a>Creare un tipo di dispositivo simulato

Il modo più semplice per creare un nuovo tipo di dispositivo nel microservizio di simulazione consiste nel copiare e modificare un tipo esistente. I passaggi seguenti mostrano come copiare il dispositivo **Chiller** (Refrigeratore) predefinito per creare un nuovo dispositivo **Lightbulb** (Lampadina):

1. Usare il comando seguente per clonare il repository GitHub **device-simulation** nel computer locale:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Per ogni tipo di dispositivo sono presenti un file modello JSON e gli script associati nella cartella `Services/data/devicemodels`. Copiare i file relativi a **Chiller** (Refrigeratore) per creare i file relativi a **Lightbulb** (Lampadina) come illustrato nella tabella seguente:

    | Origine                      | Destinazione                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definire le caratteristiche del nuovo tipo di dispositivo

Il file `lightbulb-01.json` definisce le caratteristiche del tipo, ad esempio i dati di telemetria generati e i metodi supportati. I passaggi seguenti aggiornano il file `lightbulb-01.json` per definire il dispositivo **Lightbulb** (Lampadina):

1. Nel file `lightbulb-01.json` aggiornare i metadati del dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Nel file `lightbulb-01.json` aggiornare la definizione di simulazione come illustrato nel frammento di codice seguente:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. Nel file `lightbulb-01.json` aggiornare le proprietà del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Nel file `lightbulb-01.json` aggiornare le definizioni di telemetria del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. Nel file `lightbulb-01.json` aggiornare i metodi del tipo di dispositivo come illustrato nel frammento di codice seguente:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. Salvare il file.`lightbulb-01.json`

### <a name="simulate-custom-device-behavior"></a>Simulare il comportamento del dispositivo personalizzato

Il file `scripts/lightbulb-01-state.js` definisce il comportamento di simulazione del tipo **Lightbulb** (Lampadina). I passaggi seguenti aggiornano il file `scripts/lightbulb-01-state.js` per definire il comportamento del dispositivo **Lightbulb** (Lampadina):

1. Modificare la definizione di stato nel file `scripts/lightbulb-01-state.js` come illustrato nel frammento di codice seguente:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Sostituire la funzione **vary** con la funzione **flip** seguente:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Modificare la funzione **main** per implementare il comportamento come illustrato nel frammento di codice seguente:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Salvare il file.`scripts/lightbulb-01-state.js`

Il file `scripts/SwitchOn-method.js` implementa il metodo **Switch On** (Accendi) in un dispositivo **Lightbulb** (Lampadina). I passaggi seguenti aggiornano il file `scripts/SwitchOn-method.js`:

1. Modificare la definizione di stato nel file `scripts/SwitchOn-method.js` come illustrato nel frammento di codice seguente:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Per accendere la lampadina, modificare la funzione **main** come indicato di seguito:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Salvare il file.`scripts/SwitchOn-method.js`

1. Creare una copia del file `scripts/SwitchOn-method.js` denominata `scripts/SwitchOff-method.js`.

1. Per spegnere la lampadina, modificare la funzione **main** nel file `scripts/SwitchOff-method.js` come indicato di seguito:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Salvare il file.`scripts/SwitchOff-method.js`

### <a name="test-the-lightbulb-device-type"></a>Testare il tipo di dispositivo lampadina

Per testare il tipo di dispositivo **Lightbulb** (Lampadina), è prima di tutto possibile verificare se il tipo di dispositivo si comporta come previsto eseguendo una copia locale del servizio **device-simulation**. Dopo avere eseguito il test e il debug del nuovo tipo di dispositivo in locale, è possibile ricreare il contenitore e ridistribuire il servizio **device-simulation** in Azure.

Per eseguire il test e il debug delle modifiche in locale, vedere [Device simulation overview](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Panoramica della simulazione di dispositivi).

Configurare il progetto per copiare i file del nuovo dispositivo **Lightbulb** (Lampadina) nella directory di output.

Per testare il nuovo dispositivo in una soluzione distribuita, vedere:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Distribuzione di contenitori dall'account docker-hub personalizzato)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Aggiornare un contenitore distribuito tramite copia manuale)

Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del nuovo tipo:

![Visualizzare l'elenco di simulazioni disponibili](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

È possibile visualizzare i dati di telemetria del dispositivo simulato:

![Visualizzare i dati di telemetria della lampadina](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

È possibile chiamare i metodi **SwitchOn** e **SwitchOff** nel dispositivo:

![Chiamare i metodi per la lampadina](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Per creare un'immagine Docker con il nuovo tipo di dispositivo per la distribuzione in Azure, vedere [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Creazione di un'immagine Docker personalizzata).

## <a name="add-a-new-telemetry-type"></a>Aggiungere un nuovo tipo di telemetria

Questa sezione descrive come modificare un tipo di dispositivo simulato esistente per supportare un nuovo tipo di telemetria.

### <a name="locate-the-chiller-device-type-files"></a>Individuare i file per il tipo di dispositivo Chiller (Refrigeratore)

I passaggi seguenti mostrano come trovare i file che definiscono il dispositivo **Chiller** (Refrigeratore) predefinito:

1. Se non è già stato fatto, usare il comando seguente per clonare il repository GitHub **device-simulation** nel computer locale:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Per ogni tipo di dispositivo sono presenti un file modello JSON e gli script associati nella cartella `data/devicemodels`. I file che definiscono il tipo di dispositivo **Chiller** (Refrigeratore) simulato sono:
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Specificare il nuovo tipo di telemetria

I passaggi seguenti mostrano come aggiungere un nuovo tipo **Internal Temperature** (Temperatura interna) al tipo di dispositivo **Chiller** (Refrigeratore):

1. Aprire il file `chiller-01.json` .

1. Aggiornare il valore di **SchemaVersion** come indicato di seguito:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Nella sezione **InitialState** aggiungere le due definizioni seguenti:

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

1. Salvare il file.`chiller-01.json`

1. Aprire il file `scripts/chiller-01-state.js` .

1. Aggiungere i campi seguenti alla variabile **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aggiungere la riga seguente alla funzione **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Salvare il file.`scripts/chiller-01-state.js`

### <a name="test-the-chiller-device-type"></a>Testare il tipo di dispositivo Chiller (Refrigeratore)

Per testare il tipo di dispositivo **Chiller** (Refrigeratore) aggiornato, è prima di tutto possibile verificare se il tipo di dispositivo si comporta come previsto eseguendo una copia locale del servizio **device-simulation**. Dopo avere eseguito il test e il debug del tipo di dispositivo aggiornato in locale, è possibile ricreare il contenitore e ridistribuire il servizio **device-simulation** in Azure.

Quando si esegue il servizio **device-simulation** in locale, i dati di telemetria vengono inviati alla soluzione di monitoraggio remoto. Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del tipo aggiornato.

Per eseguire il test e il debug delle modifiche in locale, vedere [Running the service with Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) (Eseguire il servizio con Visual Studio) o [Build and Run from the command line](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line) (Compilare ed eseguire dalla riga di comando).

Per testare il nuovo dispositivo in una soluzione distribuita, vedere:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Distribuzione di contenitori dall'account docker-hub personalizzato)
* [Update a deployed container via manual copy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy) (Aggiornare un contenitore distribuito tramite copia manuale)

Nella pagina **Devices** (Dispositivi) è possibile effettuare il provisioning delle istanze del tipo aggiornato:

![Aggiungere il refrigeratore aggiornato](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

È possibile visualizzare il nuovo tipo di telemetria **Internal temperature** (Temperatura interna) del dispositivo simulato.

Per creare un'immagine Docker con il nuovo tipo di dispositivo per la distribuzione in Azure, vedere [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Creazione di un'immagine Docker personalizzata).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha mostrato come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Creare un nuovo tipo di dispositivo
> * Simulare il comportamento del dispositivo personalizzato
> * Aggiungere un nuovo tipo di dispositivo al dashboard
> * Inviare dati di telemetria personalizzati da un tipo di dispositivo esistente

Ora che si è appreso come usare il servizio di simulazione di dispositivi, il passaggio successivo consigliato consente di apprendere come [connettere un dispositivo fisico alla soluzione di monitoraggio remoto](iot-suite-connecting-devices-node.md).

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->