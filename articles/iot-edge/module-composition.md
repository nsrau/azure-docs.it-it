---
title: Distribuire moduli e route con i manifesti della distribuzione - Azure IoT Edge
description: Informazioni su come un manifesto della distribuzione dichiara quali moduli distribuire, come distribuirli e come creare tra di loro route di messaggi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f6c12b892e01aafd5beecdff14751481cf7fc96
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963398"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Informazioni su come distribuire moduli e definire route in IoT Edge

Ogni dispositivo IoT Edge esegue almeno due moduli: $edgeAgent e $edgeHub, che fanno parte del runtime di IoT Edge. Un dispositivo IoT Edge può eseguire più moduli aggiuntivi per qualsiasi numero di processi. Usare un manifesto della distribuzione per indicare a un dispositivo quali moduli installare e come configurarli perché possano interagire.

Il *manifesto della distribuzione* è un documento JSON che descrive:

* Il modulo gemello dell'**agente IoT Edge**, che include tre componenti:
  * Immagine del contenitore per ogni modulo in esecuzione nel dispositivo.
  * Credenziali per accedere ai registri di contenitori privati che contengono immagini del modulo.
  * Istruzioni per la creazione e la gestione di ogni modulo.
* Il modulo gemello dell'**agente IoT Edge**, che include le modalità di flusso dei messaggi tra i moduli e con l'hub IoT.
* Facoltativamente, le proprietà desiderate di qualsiasi altro modulo gemello.

Tutti i dispositivi IoT Edge devono essere configurati con un manifesto della distribuzione. Un runtime IoT Edge appena installato segnala un codice di errore finché non verrà configurato con un manifesto valido.

Nelle esercitazioni di Azure IoT Edge viene creato un manifesto della distribuzione seguendo una procedura guidata nel portale di Azure IoT Edge. È anche possibile applicare un manifesto della distribuzione a livello di codice usando REST o IoT Hub Service SDK. Per altre informazioni, vedere [Informazioni sulle distribuzioni IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Creare un manifesto di distribuzione

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le *proprietà desiderate* per ogni modulo gemello. I dispositivi IoT Edge riportano le *proprietà segnalate* per ogni modulo.

In ogni manifesto della distribuzione sono necessari due moduli: `$edgeAgent`, e `$edgeHub`. Questi moduli costituiscono parte del runtime di IoT Edge che gestisce il dispositivo IoT Edge e i moduli in esecuzione su di esso. Per altre informazioni su questi moduli, vedere [Comprendere il runtime di IoT Edge e la relativa architettura](iot-edge-runtime.md).

Oltre ai due moduli di runtime, è possibile aggiungere fino a 50 moduli personalizzati per l'esecuzione in un dispositivo IoT Edge.

È valido anche un manifesto della distribuzione contenente solo il runtime di IoT Edge (edgeAgent and edgeHub).

I manifesti di distribuzione seguono questa struttura:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
        // includes the routing information between modules, and to IoT Hub
      }
    },
    "module1": {  // optional
      "properties.desired": {
        // desired properties of module1
      }
    },
    "module2": {  // optional
      "properties.desired": {
        // desired properties of module2
      }
    }
  }
}
```

## <a name="configure-modules"></a>Configurare i moduli

È necessario indicare al runtime di IoT Edge come installare i moduli nella distribuzione. L'agente IoT Edge è il componente di runtime che gestisce l'installazione, gli aggiornamenti e i rapporti di stato di un dispositivo IoT Edge. Le informazioni di configurazione e gestione di tutti i moduli sono quindi disponibili nel modulo gemello $edgeAgent. Nelle informazioni sono inclusi anche i parametri di configurazione dell'agente IoT Edge.

Le proprietà di $edgeAgent seguono questa struttura:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            // configuration and management details
          },
          "edgeHub": {
            // configuration and management details
          }
        },
        "modules": {
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

La versione 1,1 dello schema dell'agente IoT Edge è stata rilasciata insieme a IoT Edge versione 1.0.10 e Abilita l'ordine di avvio del modulo. La versione dello schema 1,1 è consigliata per qualsiasi distribuzione di IoT Edge che esegue la versione 1.0.10 o successiva.

### <a name="module-configuration-and-management"></a>Configurazione e gestione dei moduli

L'elenco delle proprietà desiderate dell'agente IoT Edge è quello in cui si definiscono i moduli distribuiti in un dispositivo IoT Edge e il modo in cui devono essere configurati e gestiti.

Per un elenco completo delle proprietà desiderate che possono o devono essere incluse, vedere [proprietà dell'agente IOT Edge e dell'hub IOT Edge](module-edgeagent-edgehub.md).

Ad esempio:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Ogni modulo dispone di una proprietà **Settings** che contiene l' **immagine**del modulo, un indirizzo per l'immagine del contenitore in un registro contenitori e qualsiasi **createOptions** per configurare l'immagine all'avvio. Per ulteriori informazioni, vedere [come configurare le opzioni di creazione di contenitori per IOT Edge moduli](how-to-use-create-options.md).

Il modulo edgeHub e i moduli personalizzati hanno anche tre proprietà che indicano all'agente IoT Edge come gestirli:

* **Stato**: indica se il modulo deve essere in esecuzione o arrestato al momento della prima distribuzione. Obbligatorio.
* **RestartPolicy**: quando e se l'agente di IOT Edge deve riavviare il modulo in caso di arresto. Obbligatorio.
* **StartupOrder**: *introdotta in IOT Edge versione 1.0.10.* L'ordine con cui l'agente IoT Edge deve avviare i moduli al momento della distribuzione. L'ordine viene dichiarato con numeri interi, in cui viene avviato per primo un modulo dato un valore di avvio pari a 0, quindi i numeri più alti seguono. Il modulo edgeAgent non ha un valore di avvio perché viene sempre avviato per primo. facoltativo.

  L'agente di IoT Edge avvia i moduli in ordine di valore di avvio, ma non attende il completamento di ogni modulo prima di passare a quello successivo.

  L'ordine di avvio è utile se alcuni moduli dipendono da altri. È ad esempio possibile che si desideri avviare prima il modulo edgeHub in modo che sia pronto per il routing dei messaggi all'avvio degli altri moduli. In alternativa, è possibile avviare un modulo di archiviazione prima dei moduli che inviano dati. Tuttavia, è consigliabile progettare sempre i moduli per gestire gli errori di altri moduli. Si tratta della natura dei contenitori che possono essere arrestati e riavviati in qualsiasi momento e un numero qualsiasi di volte.

## <a name="declare-routes"></a>Dichiarare le route

L'hub di IoT Edge gestisce la comunicazione tra i moduli, l'hub IoT e tutti i dispositivi foglia. Pertanto, il modulo gemello $edgeHub contiene una proprietà desiderata chiamata *routes* che dichiara come i messaggi vengono passati all'interno di una distribuzione. È possibile che nella stessa distribuzione siano presenti più route.

Le route vengono dichiarate nelle proprietà desiderate di **$edgeHub** con la sintassi seguente:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

La versione 1,1 dello schema dell'hub IoT Edge è stata rilasciata insieme a IoT Edge versione 1.0.10 e Abilita la definizione delle priorità delle route e la durata (TTL). La versione dello schema 1,1 è consigliata per qualsiasi distribuzione di IoT Edge che esegue la versione 1.0.10 o successiva.

Ogni route richiede un' *origine* da cui provengono i messaggi e un *sink* in cui i messaggi vengono inviati. La *condizione* è una parte facoltativa che è possibile usare per filtrare i messaggi.

È possibile assegnare la *priorità* alle route per cui si desidera assicurarsi di elaborare prima i messaggi. Questa funzionalità è utile negli scenari in cui la connessione upstream è debole o limitata e si hanno dati critici che devono essere classificati in ordine di priorità su messaggi di telemetria standard.

### <a name="source"></a>Source (Sorgente)

L'origine specifica da dove provengono i messaggi. IoT Edge può indirizzare i messaggi da moduli o dispositivi foglia.

Usando gli IoT SDK, i moduli possono dichiarare code di output specifiche per i rispettivi messaggi usando la classe ModuleClient. Le code di output non sono necessarie, ma sono utili per la gestione di più route. I dispositivi foglia possono usare la classe DeviceClient degli IoT SDK per inviare messaggi ai dispositivi gateway IoT Edge nello stesso modo in cui li inviano all'hub IoT. Per altre informazioni, vedere [Comprendere e usare Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).

La proprietà di origine può essere uno dei valori seguenti:

| Source (Sorgente) | Descrizione |
| ------ | ----------- |
| `/*` | Tutte le notifiche da dispositivo a cloud o dei dispositivi gemelli le notifiche da qualsiasi dispositivo foglia o modulo |
| `/twinChangeNotifications` | Qualsiasi modifica gemella (proprietà segnalate) provenienti da qualsiasi dispositivo foglia o modulo |
| `/messages/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un dispositivo foglia con o senza output |
| `/messages/modules/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo con o senza output |
| `/messages/modules/<moduleId>/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con o senza output |
| `/messages/modules/<moduleId>/outputs/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con output |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con un output specifico |

### <a name="condition"></a>Condizione

La condizione è facoltativa in una dichiarazione di route. Se si intende passare tutti i messaggi dall'origine al sink, escludere interamente la clausola **WHERE**. In alternativa è possibile usare il [linguaggio di query di hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) per filtrare alcuni messaggi o tipi di messaggio che soddisfano la condizione. Le route di IoT Edge non supportano i messaggi di filtro in base a tag o proprietà gemelli.

I messaggi che passano tra i moduli in IoT Edge sono formattati come i messaggi che passano tra i dispositivi e l'hub IoT di Azure. Tutti i messaggi sono formattati come JSON e hanno i parametri **systemProperties**, **appProperties** e **body**.

È possibile compilare query in base a qualunque dei tre parametri con la sintassi seguente:

* Proprietà di sistema: `$<propertyName>` o `{$<propertyName>}`
* Proprietà dell'applicazione: `<propertyName>`
* Proprietà del corpo: `$body.<propertyName>`

Per esempi su come creare query in base alle proprietà dei messaggi, vedere [Espressioni di query per route di messaggi da dispositivo a cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Un esempio specifico per IoT Edge riguarda il filtraggio dei messaggi inviati da un dispositivo foglia a un dispositivo gateway. I messaggi provenienti dai moduli comprendono una proprietà di sistema denominata **connectionModuleId**. Se si intende eseguire il routing del messaggi direttamente da dispositivi foglia all'hub IoT, quindi, usare la route seguente per escludere i messaggi dei moduli:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

Il sink definisce dove vengono inviati i messaggi. Solo i moduli e l'hub IoT possono ricevere messaggi. I messaggi non possono essere indirizzati ad altri dispositivi. Non esistono opzioni con caratteri jolly nella proprietà sink.

La proprietà sink può essere uno dei valori seguenti:

| Sink | Descrizione |
| ---- | ----------- |
| `$upstream` | Inviare il messaggio all'hub IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Inviare il messaggio a un input specifico di un modulo specifico |

IoT Edge offre garanzie di tipo at-least-once. L'hub di IoT Edge archivia i messaggi in locale nel caso in cui una route non riesca a recapitare il messaggio al relativo sink. Ad esempio, se l'hub di IoT Edge non riesce a connettersi all'hub IoT o il modulo di destinazione non è connesso.

L'hub di IoT Edge archivia i messaggi fino all'ora specificata nella proprietà `storeAndForwardConfiguration.timeToLiveSecs` delle [proprietà desiderate dell'hub di IoT Edge](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Priorità e durata (TTL)

Le route possono essere dichiarate solo con una stringa che definisce la route o come oggetto che accetta una stringa di route, un numero intero di priorità e un valore di durata (TTL).

Opzione 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Opzione 2, introdotta in IoT Edge versione 1.0.10 con IoT Edge schema Hub versione 1,1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

I valori di **priorità** possono essere 0-9, inclusi, dove 0 è la priorità più alta. I messaggi vengono accodati in base ai relativi endpoint. Tutti i messaggi con priorità 0 destinati a un endpoint specifico verranno elaborati prima che vengano elaborati tutti i messaggi con priorità 1 destinati allo stesso endpoint e fino alla riga. Se più route per lo stesso endpoint hanno la stessa priorità, i messaggi verranno elaborati in base alla prima esecuzione. Se non viene specificata alcuna priorità, la route viene assegnata alla priorità più bassa.

La proprietà **timeToLiveSecs** eredita il valore da **storeAndForwardConfiguration** dell'hub IOT Edge, a meno che non sia impostato in modo esplicito. Il valore può essere qualsiasi numero intero positivo.

Per informazioni dettagliate sul modo in cui vengono gestite le code di priorità, vedere la pagina di riferimento relativa alla [priorità e alla durata della route](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md).

## <a name="define-or-update-desired-properties"></a>Definire o aggiornare le proprietà desiderate

Il manifesto di distribuzione specifica le proprietà desiderate per ogni modulo distribuito nel dispositivo IoT Edge. Le proprietà desiderate nel manifesto di distribuzione sovrascrivono le proprietà desiderate attualmente nel modulo gemello.

Se nel manifesto della distribuzione non si specificano le proprietà desiderate del dispositivo gemello di un modulo, l'hub IoT non modifica il dispositivo in alcun modo il modulo. In alternativa, è possibile impostare le proprietà desiderate a livello di codice.

Gli stessi meccanismi che consentono di modificare i dispositivi gemelli vengono usati per modificare i moduli gemelli. Per altre informazioni, vedere il [modulo per sviluppatori sui dispositivi gemelli](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Esempio di manifesto della distribuzione

L'esempio seguente mostra come viene visualizzato un documento del manifesto di distribuzione valido.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo delle proprietà che possono o devono essere incluse in $edgeAgent e $edgeHub, vedere [Proprietà dell'agente di IoT Edge e dell'hub di IoT Edge](module-edgeagent-edgehub.md).

* Dopo aver appreso come usare i moduli IoT Edge, passare alla pagina [Informazioni sui requisiti e gli strumenti per sviluppare moduli di IoT Edge](module-development.md).
