---
title: Schema dei dispositivi nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive lo schema JSON che definisce un dispositivo simulato nella soluzione di monitoraggio remota.
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
ms.openlocfilehash: 364698a529623958695f93a245bab28a89f6bd4c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="understand-the-device-model-schema"></a>Informazioni sullo schema del modello dei dispositivi

È possibile usare dispositivi simulati nella soluzione di monitoraggio remoto per testarne il comportamento. Quando si distribuisce la soluzione di monitoraggio remoto, viene eseguito automaticamente il provisioning di una raccolta di dispositivi simulati. È possibile personalizzare i dispositivi simulati esistenti o crearne di nuovi.

Questo articolo descrive lo schema del modello di dispositivo che specifica le funzionalità e il comportamento di un dispositivo simulato. Il modello del dispositivo viene archiviato in un file JSON.

Gli articoli seguenti sono correlati all'articolo corrente:

* [Implement the device model behavior](iot-suite-remote-monitoring-device-behavior.md) (Implementare il comportamento del modello di dispositivo) descrive i file JavaScript usati per implementare il comportamento di un dispositivo simulato.
* [Creare un nuovo dispositivo simulato](iot-suite-remote-monitoring-test.md) riunisce tutti gli aspetti e illustra come distribuire un nuovo tipo di dispositivo simulato nella soluzione.

In questo articolo viene spiegato come:

>[!div class="checklist"]
> * Usare un file JSON per definire un modello di dispositivo simulato
> * Specificare le proprietà del dispositivo simulato
> * Specificare i dati di telemetria inviati dal dispositivo simulato
> * Specificare i metodi da cloud a dispositivo a cui risponde il dispositivo

## <a name="the-parts-of-the-device-model-schema"></a>Parte dello schema del modello di dispositivo

Ogni modello di dispositivo, sia esso un refrigeratore o un veicolo, definisce un tipo di dispositivo simulato da connettere alla soluzione di monitoraggio remota. Ogni modello di dispositivo viene archiviato in un file JSON con lo schema di primo livello seguente:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

È possibile visualizzare i file di schema per i dispositivi simulati predefiniti nella [cartella devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) su GitHub.

La tabella seguente descrive le voci dello schema di primo livello:

| Voce dello schema | DESCRIZIONE |
| -- | --- |
| `SchemaVersion` | La versione dello schema è sempre `1.0.0` ed è specifica per il formato del file. |
| `Id` | ID univoco per questo modello di dispositivo. |
| `Version` | Identifica la versione del modello di dispositivo. |
| `Name` | Nome descrittivo per il modello di dispositivo. |
| `Description` | Descrizione del modello di dispositivo. |
| `Protocol` | Protocollo di connessione usato dal dispositivo. I valori possibili sono `AMQP`, `MQTT` e `HTTP`. |

Le sezioni seguenti descrivono le altre sezioni nello schema JSON:

## <a name="simulation"></a>Simulation

Nella sezione `Simulation` viene definito lo stato interno del dispositivo simulato. Gli eventuali valori di telemetria inviati dal dispositivo devono far parte di questo stato del dispositivo.

La definizione dello stato del dispositivo è costituita da due elementi:

* `InitialState` definisce i valori iniziali per tutte le proprietà dell'oggetto di stato del dispositivo.
* `Script` identifica un file JavaScript che viene eseguito in base a una pianificazione per aggiornare lo stato del dispositivo. È possibile usare questo file di script per impostare in modo casuale i valori di telemetria inviati dal dispositivo.

Per altre informazioni sul file JavaScript che aggiorna l'oggetto di stato del dispositivo, vedere [Informazioni sul comportamento del modello dei dispositivi](iot-suite-remote-monitoring-device-behavior.md).

L'esempio seguente mostra la definizione dell'oggetto di stato del dispositivo per un dispositivo refrigeratore (chiller) simulato:

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

Il servizio di simulazione esegue il file **chiller-01-state.js** ogni cinque secondi per aggiornare lo stato del dispositivo. È possibile visualizzare i file JavaScript per i dispositivi simulati predefiniti nella [cartella scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) su GitHub. Per convenzione, questi file JavaScript hanno il suffisso **-state** per distinguerli dai file che implementano i comportamenti dei metodi.

## <a name="properties"></a>Properties

La sezione `Properties` dello schema definisce i valori di proprietà segnalati dal dispositivo alla soluzione. Ad esempio: 

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

All'avvio, la soluzione richiede a tutti i dispositivi simulati di compilare un elenco di valori `Type` da usare nell'interfaccia utente. La soluzione usa le proprietà `Latitiude` e `Longitude` per aggiungere la posizione del dispositivo alla mappa nel dashboard.

## <a name="telemetry"></a>Telemetry

La matrice `Telemetry` elenca tutti i tipi di dati di telemetria inviati alla soluzione dal dispositivo simulato.

L'esempio seguente invia un messaggio di dati di telemetria JSON ogni 10 secondi con dati `floor`, `vibration` e `temperature` dai sensori del montacarichi:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` definisce la struttura del messaggio JSON inviato dal dispositivo simulato. I segnaposto in `MessageTemplate` usano la sintassi `${NAME}` dove `NAME` è una chiave dall'[oggetto di stato del dispositivo](#simulation). Le stringhe devono essere racchiuse tra virgolette, i numeri no.

`MessageSchema` definisce lo schema del messaggio inviato dal dispositivo simulato. Lo schema del messaggio viene pubblicato anche nell'hub IoT per consentire alle applicazioni back-end di riutilizzare le informazioni per interpretare i dati di telemetria in ingresso.

Attualmente, è possibile usare solo gli schemi di messaggio JSON. I campi elencati nello schema possono essere dei tipi seguenti:

* Oggetto: serializzato con JSON
* Binario: serializzato con base64
* Text
* boolean
* Integer
* Double
* Datetime

Per inviare messaggi di dati di telemetria a intervalli diversi, aggiungere più tipi di dati di telemetria alla matrice `Telemetry`. L'esempio seguente invia i dati di temperatura e umidità ogni 10 secondi e lo stato dell'illuminazione ogni minuto:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Un dispositivo simulato può rispondere ai metodi da cloud a dispositivo chiamati dalla soluzione di monitoraggio remoto. La sezione `CloudToDeviceMethods` nel file di schema del modello di dispositivo:

* Definisce i metodi a cui può rispondere il dispositivo simulato.
* Identifica il file JavaScript che contiene la logica da eseguire.

Il dispositivo simulato invia l'elenco di metodi supportati alla soluzione di monitoraggio remoto.

Per altre informazioni sul file JavaScript che implementa il comportamento del dispositivo, vedere [Informazioni sul comportamento del modello dei dispositivi](iot-suite-remote-monitoring-device-behavior.md).

L'esempio seguente specifica i tre metodi supportati e i file JavaScript che implementano questi metodi:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
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

È possibile visualizzare i file JavaScript per i dispositivi simulati predefiniti nella [cartella scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) su GitHub. Per convenzione, questi file JavaScript hanno il suffisso **-method** per distinguerli dai file che implementano il comportamento di stato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come creare un modello di dispositivo simulato personalizzato. In questo articolo sono state presentate le procedure per:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Usare un file JSON per definire un modello di dispositivo simulato
> * Specificare le proprietà del dispositivo simulato
> * Specificare i dati di telemetria inviati dal dispositivo simulato
> * Specificare i metodi da cloud a dispositivo a cui risponde il dispositivo

Dopo aver appreso alcune informazioni di base sullo schema JSON, il passaggio successivo consigliato consiste nello scoprire come [implementare il comportamento del dispositivo simulato](iot-suite-remote-monitoring-device-behavior.md).

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->