---
title: Comportamento di un dispositivo simulato nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive come usare JavaScript per definire il comportamento di un dispositivo simulato nella soluzione di monitoraggio remoto.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e5846893166c3e65b75e84d02849c2b8ab78e079
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="implement-the-device-model-behavior"></a>Implementare il comportamento del modello di dispositivo

L'articolo [Understand the device model schema](iot-suite-remote-monitoring-device-schema.md) (Conoscere lo schema del modello di dispositivo) descrive lo schema che definisce un modello di dispositivo simulato. L'articolo fa riferimento a due tipi di file JavaScript che implementano il comportamento di un dispositivo simulato:

- File JavaScript di tipo **State** che vengono eseguiti a intervalli prefissati per aggiornare lo stato interno del dispositivo.
- File JavaScript di tipo **Method** che vengono eseguiti quando la soluzione richiama un metodo nel dispositivo.

In questo articolo viene spiegato come:

>[!div class="checklist"]
> * Controllare lo stato di un dispositivo simulato.
> * Definire come deve rispondere un dispositivo simulato a una chiamata di metodo dalla soluzione di monitoraggio remoto.
> * Eseguire il debug degli script.

## <a name="state-behavior"></a>Comportamento in caso di file di tipo State

La sezione [Simulation](iot-suite-remote-monitoring-device-schema.md#simulation) dello schema del modello di dispositivo definisce lo stato interno di un dispositivo simulato:

- `InitialState` definisce i valori iniziali di tutte le proprietà dell'oggetto di stato del dispositivo.
- `Script` identifica un file JavaScript che viene eseguito in base a una pianificazione per aggiornare lo stato del dispositivo.

L'esempio seguente mostra la definizione dell'oggetto di stato del dispositivo per un dispositivo simulato refrigeratore:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Script": {
    "Type": "javascript",
    "Path": "chiller-01-state.js",
    "Interval": "00:00:05"
  }
}
```

Lo stato del dispositivo simulato, definito nella sezione `InitialState`, viene mantenuto in memoria dal servizio di simulazione. Le informazioni sullo stato vengono passate come input alla funzione `main` definita in **chiller-01-state.js**. In questo esempio il servizio di simulazione esegue il file **chiller-01-state.js** ogni cinque secondi. Lo script può modificare lo stato del dispositivo simulato.

Di seguito è riportata la struttura di una tipica funzione `main`:

```javascript
function main(context, previousState) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Il parametro `context` ha le proprietà seguenti:

- `currentTime` come stringa con formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, ad esempio `Simulated.Chiller.123`
- `deviceModel`, ad esempio `Chiller`

Il parametro `state` contiene lo stato del dispositivo mantenuto dal servizio di simulazione del dispositivo. Questo valore corrisponde all'oggetto `state` restituito dalla chiamata precedente a `main`.

L'esempio seguente mostra un'implementazione tipica del metodo `main` per gestire lo stato del dispositivo mantenuto dal servizio di simulazione:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState) {

  restoreState(previousState);

  // Update state

  return state;
}
```

L'esempio seguente mostra la capacità del metodo `main` di simulare i valori di telemetria che variano nel tempo:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

È possibile visualizzare il file [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) completo in Github.

## <a name="method-behavior"></a>Comportamento in caso di file di tipo Method

La sezione [CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) dello schema del modello di dispositivo definisce i metodi a cui risponde un dispositivo simulato.

L'esempio seguente mostra l'elenco dei metodi supportati da un dispositivo simulato refrigeratore:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Ogni metodo presenta un file JavaScript associato che implementa il comportamento del metodo.

Lo stato del dispositivo simulato, definito nella sezione `InitialState` dello schema, viene mantenuto in memoria dal servizio di simulazione. Le informazioni sullo stato vengono passate come input alla funzione `main` definita nel file JavaScript quando viene chiamato il metodo. Lo script può modificare lo stato del dispositivo simulato.

Di seguito è riportata la struttura di una tipica funzione `main`:

```javascript
function main(context, previousState) {

}
```

Il parametro `context` ha le proprietà seguenti:

- `currentTime` come stringa con formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, ad esempio `Simulated.Chiller.123`
- `deviceModel`, ad esempio `Chiller`

Il parametro `state` contiene lo stato del dispositivo mantenuto dal servizio di simulazione del dispositivo.

Sono presenti due funzioni globali che è possibile usare per implementare il comportamento del metodo:

- `updateState` per aggiornare lo stato mantenuto dal servizio di simulazione.
- `sleep` per sospendere l'esecuzione della simulazione di un'attività a esecuzione prolungata.

L'esempio seguente mostra una versione abbreviata dello script **IncreasePressure-method.js** usato dai dispositivi simulati refrigeratore:

```javascript
function main(context, previousState) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Debug dei file di script

Non è possibile allegare un debugger all'interprete Javascript usato dal servizio di simulazione del dispositivo per eseguire gli script di stato e metodo. Tuttavia, è possibile registrare informazioni nel log del servizio. La funzione predefinita `log()` consente di salvare le informazioni per tenere traccia ed eseguire il debug dell'esecuzione della funzione.

In caso di errore di sintassi, l'interprete ha esito negativo e scrive una voce `Jint.Runtime.JavaScriptException` nel log del servizio.

L'articolo [Creare un nuovo dispositivo simulato](iot-suite-remote-monitoring-test.md) illustra come eseguire il servizio di simulazione del dispositivo a livello locale. L'esecuzione del servizio in locale facilita il debug dei dispositivi simulati prima della distribuzione nel cloud.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha spiegato come definire il comportamento di un modello di dispositivo simulato personalizzato. Sono state illustrate le procedure per eseguire queste operazioni:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controllare lo stato di un dispositivo simulato.
> * Definire come deve rispondere un dispositivo simulato a una chiamata di metodo dalla soluzione di monitoraggio remoto.
> * Eseguire il debug degli script.

Ora che si è appreso come specificare il comportamento di un dispositivo simulato, il passaggio successivo consigliato consente di apprendere come [creare un dispositivo simulato](iot-suite-remote-monitoring-test.md).

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->