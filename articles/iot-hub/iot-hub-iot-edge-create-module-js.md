---
title: Creare un modulo Azure IoT Edge con Node.js | Microsoft Docs
description: "Questa esercitazione illustra come scrivere un modulo convertitore di dati BLE usando i pacchetti NPM di Azure IoT Edge più recenti e il generatore Yeoman."
services: iot-hub
author: sushi
manager: xiaozha
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 12e0702c31f36368fd95127abeede1a0d4ff2c87
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Creare un modulo Azure IoT Edge con Node.js

Questa esercitazione illustra come creare un modulo per Azure IoT Edge in JS.

L'esercitazione illustra i passaggi per configurare l'ambiente e spiega come scrivere un modulo convertitore di dati [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) con la versione più recente dei pacchetti NPM di Azure IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

In questa sezione si configura l'ambiente per lo sviluppo del modulo IoT Edge. Si applica sia a *Windows a 64 bit* che a *Linux a 64 bit (Ubuntu 14+)*.

È richiesto il software seguente:
* [Client Git](https://git-scm.com/downloads).
* [Nodo LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Architettura

La piattaforma Azure IoT Edge usa frequentemente l'[architettura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). L'intera architettura di Azure IoT Edge è quindi un sistema che elabora input e produce output e ogni singolo modulo è anche un piccolo sottosistema di input/output. In questa esercitazione vengono presentati i due moduli seguenti:

1. Un modulo che riceve un segnale [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulato e lo converte in un messaggio [JSON](https://en.wikipedia.org/wiki/JSON) formattato.
2. Un modulo che stampa il messaggio [JSON](https://en.wikipedia.org/wiki/JSON) ricevuto.

L'immagine seguente mostra il tipico flusso di dati end-to-end per il progetto:

![Flusso di dati tra tre moduli](media/iot-hub-iot-edge-create-module/dataflow.png "Input: modulo BLE simulato; Processore: modulo convertitore; Output: modulo stampante")

## <a name="set-up-the-environment"></a>Configurare l'ambiente
Di seguito viene mostrato come configurare rapidamente l'ambiente per iniziare a scrivere il primo modulo convertitore BLE con JS.

### <a name="create-module-project"></a>Creare un progetto di modulo
1. Aprire la finestra della riga di comando ed eseguire `yo az-iot-gw-module`.
2. Seguire i passaggi visualizzati per completare l'inizializzazione del progetto del modulo.

### <a name="project-structure"></a>Struttura progetto
Un progetto di modulo JS è costituito dai componenti seguenti:

`modules` - I file di origine del modulo JS personalizzato. Sostituire i file `sensor.js` e `printer.js` predefiniti con i propri file del modulo.

`app.js` - Il file di voce per avviare l'istanza di Edge.

`gw.config.json` - Il file di configurazione per personalizzare i moduli da caricare in Edge.

`package.json` - Le informazioni sui metadati per il progetto del modulo.

`README.md` - La documentazione di base per il progetto del modulo.


### <a name="package-file"></a>File del pacchetto

Il file `package.json` dichiara tutte le informazioni sui metadati necessarie a un progetto di modulo, tra cui il nome, la versione, la voce, gli script, il runtime e le dipendenze di sviluppo.

Il frammento di codice seguente mostra come preparare la configurazione per il progetto di esempio del convertitore BLE.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>File di voce
Il file `app.js` definisce il modo per inizializzare l'istanza di Edge. In questo caso non sono necessarie modifiche.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Interfaccia del modulo
È possibile gestire il modulo Azure IoT Edge come elaboratore di dati il cui compito è di ricevere l'input, elaborarlo e produrre l'output.

L'input potrebbe essere costituito da dati dell'hardware (ad esempio un rilevatore di movimento), un messaggio da altri moduli o qualsiasi altra informazione (ad esempio un numero casuale generato periodicamente da un timer).

L'output è simile all'input. Può attivare il comportamento dell'hardware, ad esempio il lampeggiamento di un LED, generare un messaggio ad altri moduli o qualsiasi altra azione, ad esempio la stampa nella console.

I moduli comunicano reciprocamente usando l'oggetto `message`. Il **contenuto** di un oggetto `message` è una matrice di byte in grado di rappresentare qualsiasi tipo di dati che si preferisce. In `message` sono presenti anche **proprietà** che rappresentano semplicemente il mapping stringa-a-stringa. Si può pensare alle **proprietà** come alle intestazioni in una richiesta HTTP o ai metadati di un file.

Per sviluppare un modulo Azure IoT Edge in JS, è necessario creare un nuovo oggetto modulo che implementa i metodi `receive()` necessari. È anche possibile a questo punto implementare i metodi `create()` o `start()` o `destroy()` facoltativi. Il frammento di codice seguente mostra lo scaffolding dell'oggetto modulo JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Modulo convertitore
| Input                    | Processore                              | Output                 | File di origine            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Messaggio sui dati della temperatura | Analizza e crea un nuovo messaggio JSON | Messaggio JSON strutturato | `converter.js` |

Questo è un tipico modulo Azure IoT Edge. Accetta messaggi sulla temperatura da altri moduli, ad esempio un modulo hardware o in questo caso il modulo BLE simulato. Normalizza quindi il messaggio sulla temperatura in un messaggio JSON strutturato, includendo l'aggiunta dell'ID di messaggio, l'impostazione della proprietà che indica se è necessario attivare l'avviso di temperatura e così via.

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Modulo della stampante
| Input                          | Processore | Output                     | File di origine          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualsiasi messaggio da altri moduli | N/D       | Registra il messaggio nella console | `printer.js` |

Questo modulo semplice e di chiara interpretazione restituisce i messaggi ricevuti (proprietà, contenuto) alla finestra Terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Configurazione
Il passaggio finale prima di eseguire i moduli consiste nel configurare Azure IoT Edge e stabilire le connessioni tra i moduli.

Dal momento che Azure IoT Edge supporta caricatori di varie lingue, occorre prima dichiarare il caricatore `node` specifico, a cui si potrebbe fare riferimento in base al `name` nelle sezioni successive.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

Dopo avere dichiarato i caricatori, è necessario dichiarare anche i moduli. Come avviene nella dichiarazione dei caricatori, anche ai moduli è possibile fare riferimento tramite il relativo attributo `name`. Quando si dichiara un modulo, è necessario specificare il caricatore che il modulo deve usare (che dovrebbe essere quello che è stato definito prima) e il punto di ingresso (che dovrebbe essere il nome della classe normalizzata del modulo) per ogni modulo. Il modulo `simulated_device` è un modulo nativo che è incluso nel pacchetto di runtime principale di Azure IoT Edge. Includere `args` nel file JSON anche se è `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

Al termine della configurazione occorre stabilire le connessioni. Ogni connessione è espressa da `source` e `sink`. Entrambi devono fare riferimento a un modulo predefinito. Il messaggio di output del modulo `source` viene inoltrato all'input del modulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Esecuzione dei moduli
1. `npm install`
2. `npm start`

Se si intende terminare l'applicazione premere `<Enter>`.

> [!IMPORTANT]
> Non è consigliabile usare Ctrl + C per terminare l'applicazione IoT Edge in quanto questa azione può determinare un arresto anomalo del processo.

