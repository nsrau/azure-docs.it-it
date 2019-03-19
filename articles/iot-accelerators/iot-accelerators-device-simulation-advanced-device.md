---
title: Creare un modello di dispositivo simulato avanzato - Azure | Microsoft Docs
description: In questa guida pratica viene illustrato come creare un modello di dispositivo avanzato da usare con l'acceleratore di soluzione Simulazione dispositivi.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: a97afe64992db6b48558561ba958b513c8416494
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592826"
---
# <a name="create-an-advanced-device-model"></a>Creare un modello di dispositivo avanzato

Questa guida pratica descrive i file JSON e JavaScript che definiscono un modello di dispositivo personalizzato. L'articolo include alcuni file di definizione del modello di dispositivo di esempio e illustra come caricarli nell'istanza di Simulazione dispositivi. È possibile creare modelli di dispositivo avanzati per simulare comportamenti del dispositivo più realistici per il test.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per seguire i passaggi di questa guida pratica, è necessaria un'istanza distribuita di Simulazione dispositivi nella sottoscrizione di Azure.

Se la soluzione Simulazione dispositivi non è stata ancora distribuita, è necessario completare la guida introduttiva [Distribuire ed eseguire una simulazione dei dispositivi IoT in Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Aprire Simulazione dispositivi

Per eseguire Simulazione dispositivi nel browser, passare innanzitutto al sito [Acceleratori di soluzione Microsoft Azure IoT](https://www.azureiotsolutions.com).

Potrebbe essere necessario accedere con le credenziali della sottoscrizione di Azure.

Fare quindi clic su **Avvio** nel riquadro per la soluzione Simulazione dispositivi distribuita nella guida introduttiva [Distribuire ed eseguire una simulazione dei dispositivi IoT in Azure](quickstart-device-simulation-deploy.md).

## <a name="device-models"></a>Modelli di dispositivo

Ogni dispositivo simulato appartiene a un modello di dispositivo specifico che definisce il comportamento di simulazione. Questo comportamento include la frequenza con cui inviare i dati di telemetria, il tipo di messaggi da inviare e i metodi supportati.

Per definire un modello di dispositivo, si usano un file di definizione di dispositivo JSON e un set di file JavaScript. Questi file JavaScript definiscono il comportamento di simulazione, ad esempio i dati di telemetria casuali e la logica dei metodi.

Un modello di dispositivo tipico comprende:

* Un file JSON per ogni modello di dispositivo (ad esempio, elevator.json).
* Un file script di comportamento JavaScript per ogni modello di dispositivo (ad esempio, elevator-state.js)
* Un file script di metodo JavaScript per ogni modello di dispositivo (ad esempio, elevator-go-down.js)

> [!NOTE]
> Poiché non tutti i modelli di dispositivo definiscono metodi, un modello di dispositivo non comprende necessariamente gli script dei metodi. Tutti i modelli di dispositivo devono tuttavia avere uno script di comportamento.

## <a name="device-definition-file"></a>File di definizione di dispositivo

Ogni file di definizione di dispositivo contiene i dettagli di un modello di dispositivo simulato, incluse le informazioni seguenti:

* Nome modello di dispositivo: stringa.
* Protocollo: AMQP | MQTT | HTTP.
* Stato iniziale del dispositivo.
* Frequenza di aggiornamento dello stato del dispositivo.
* File JavaScript da usare per aggiornare lo stato del dispositivo.
* Elenco di messaggi di telemetria da inviare, ognuno con una frequenza specifica.
* Schema dei messaggi di telemetria, usato dall'applicazione back-end per analizzare i dati di telemetria ricevuti.
* Elenco di metodi supportati e file JavaScript da usare per simulare ogni metodo.

### <a name="file-schema"></a>Schema del file

La versione dello schema è sempre "1.0.0" ed è specifica per il formato del file:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Descrizione del modello di dispositivo

Le proprietà seguenti descrivono il modello di dispositivo. Ogni tipo ha un identificatore univoco, una versione semantica, un nome e una descrizione:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protocollo IoT

I dispositivi IoT possono connettersi usando protocolli diversi. La simulazione consente di usare **AMQP**, **MQTT** o **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stato del dispositivo simulato

Ogni dispositivo simulato ha uno stato interno, che deve essere definito. Lo stato definisce anche le proprietà che possono essere segnalate nei dati di telemetria. Un refrigeratore, ad esempio, potrebbe avere uno stato iniziale come il seguente:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Un dispositivo in movimento con diversi sensori potrebbe avere altre proprietà, ad esempio:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Lo stato del dispositivo viene conservato in memoria dal servizio di simulazione e fornito come input alla funzione JavaScript. La funzione JavaScript potrebbe stabilire di:

* Ignorare lo stato e generare alcuni dati casuali.
* Aggiornare lo stato del dispositivo in modo realistico per un determinato scenario.

La funzione che genera lo stato riceve come input anche quanto segue:

* ID del dispositivo.
* Modello del dispositivo.
* Ora corrente. Questo valore consente di generare dati diversi a seconda del dispositivo e dell'ora.

### <a name="generating-telemetry-messages"></a>Generazione di messaggi di telemetria

Il servizio di simulazione può inviare diversi tipi di dati di telemetria per ogni dispositivo. I dati di telemetria includono in genere i dati sullo stato del dispositivo. Un locale simulato potrebbe ad esempio inviare informazioni sulla temperatura e l'umidità ogni 10 secondi. Si noti che i segnaposto nel frammento di codice seguente vengono sostituiti automaticamente con i valori dello stato del dispositivo:

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
    }
],
```

I segnaposto usano una speciale sintassi **${NAME}** dove **NAME** è una chiave dell'oggetto stato del dispositivo restituito dalla funzione **main** JavaScript. Le stringhe devono essere racchiuse tra virgolette, i numeri no.

#### <a name="message-schema"></a>Schema del messaggio

Ogni tipo di messaggio deve avere uno schema ben definito. Lo schema del messaggio viene pubblicato anche nell'hub IoT in modo che le applicazioni back-end possano riutilizzare le informazioni per interpretare i dati di telemetria in ingresso.

Lo schema supporta il formato JSON, che consente di eseguire facilmente analisi e trasformazione in diversi sistemi e servizi.

I campi elencati nello schema possono essere dei tipi seguenti:

* Oggetto: serializzato con JSON
* Binario: serializzato con base64
* Text
* Boolean
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Metodi supportati

I dispositivi simulati possono anche rispondere alle chiamate ai metodi, nel qual caso eseguono una logica e forniscono una risposta. Analogamente alla simulazione, la logica del metodo viene archiviata in un file JavaScript ed può interagire con lo stato del dispositivo. Ad esempio: 

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Creare un file di definizione di dispositivo

In questa guida pratica viene illustrato come creare un modello di dispositivo per un drone. Il drone effettuerà un volo casuale in un set iniziale di coordinate cambiando altitudine e posizione.

Copiare il codice JSON seguente in un editor di testo e salvarlo come **drone.json**.

### <a name="device-definition-json-example"></a>Esempio JSON di definizione di dispositivo

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>File di script di comportamento

Il codice nel file di script del comportamento fa muovere il drone. Lo script altera l'altitudine e la posizione del drone, modificando lo stato in memoria del dispositivo.

I file JavaScript devono avere una funzione **main**, che accetta due parametri:

* Un oggetto **context** che contiene tre proprietà:
    * **currentTime**, una stringa in formato **aaaa-MM-gg'T'HH:mm:sszzz**.
    * **deviceId**, ad esempio **Simulated.Elevator.123**.
    * **deviceModel**, ad esempio **Elevator**.
* Un oggetto **state** corrispondente al valore restituito dalla funzione nella chiamata precedente. Questo stato del dispositivo viene gestito dal servizio di simulazione e usato per generare messaggi di telemetria.

La funzione **main** restituisce il nuovo stato del dispositivo. Ad esempio: 

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Creare un file di script di comportamento

Copiare il codice JavaScript seguente in un editor di testo e salvarlo come **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Esempio di simulazione JavaScript di modello di dispositivo

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
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
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
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
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Creare un file di script di metodo

Gli script dei metodi sono simili agli script dei comportamenti. Definiscono il comportamento del dispositivo quando viene chiamato un metodo da cloud a dispositivo specifico.

Lo script di richiamo del drone imposta le coordinate del drone su un punto fisso per simulare il rientro del drone alla base.

Copiare il codice JavaScript seguente in un editor di testo e salvarlo come **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Esempio di simulazione JavaScript di modello di dispositivo

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
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
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Debug dei file di script

Anche se non è possibile collegare un debugger a un file di comportamento in esecuzione, è possibile scrivere informazioni nel log del servizio usando la funzione **log**. Per gli errori di sintassi, l'interprete ha esito negativo e scrive le informazioni sull'eccezione nel log.

Esempio di registrazione:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return state;
}
```

## <a name="deploy-an-advanced-device-model"></a>Distribuire un modello di dispositivo avanzato

Per distribuire il modello di dispositivo avanzato, caricare i file nell'istanza di Simulazione dispositivi:

Selezionare **Device models** (Modelli di dispositivo) nella barra dei menu. Nella pagina **Device models** (Modelli di dispositivo) vengono elencati i modelli di dispositivo disponibili in questa istanza di Simulazione dispositivi:

![Modelli di dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Fare clic su **+ Add Device Models** (+ Aggiungi modelli di dispositivo) nell'angolo in alto a destra della pagina:

![Aggiungere un modello di dispositivo](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Fare clic su **Advanced** (Avanzate) per aprire la scheda del modello di dispositivo avanzato:

![Scheda Advanced (Avanzate)](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Fare clic su **Browse** (Sfoglia) e selezionare i file JSON e JavaScript creati. Assicurarsi di selezionare tutti e tre i file. Se uno qualsiasi dei file manca, la convalida ha esito negativo:

![Ricerca dei file](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Se i file superano la convalida, fare clic su **Salva** per poter usare il modello del dispositivo in una simulazione. In caso contrario, correggere eventuali errori e ricaricare i file:

![Salva](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida pratica sono stati illustrati i file del modello del dispositivo usati in Simulazione dispositivi ed è stato spiegato come creare un modello di dispositivo avanzato. Successivamente sarà possibile vedere come [usare Time Series Insights per visualizzare i dati di telemetria inviati dall'acceleratore della soluzione Simulazione dispositivi](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
