---
title: Distribuire route di & moduli con manifesti di distribuzione - Azure IoT EdgeDeploy module to routes with deployment manifests - Azure IoT Edge
description: Informazioni su come un manifesto della distribuzione dichiara quali moduli distribuire, come distribuirli e come creare tra di loro route di messaggi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4b90d8b6fe67de26c8e652e0dc5b62cc27023f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545621"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Informazioni su come distribuire moduli e definire route in IoT Edge

Ogni dispositivo IoT Edge esegue almeno due moduli: $edgeAgent e $edgeHub, che fanno parte del runtime di IoT Edge. Il dispositivo IoT Edge può eseguire più moduli aggiuntivi per un numero qualsiasi di processi. Usare un manifesto di distribuzione per indicare al dispositivo quali moduli installare e come configurarli per l'utilizzo insieme.

Il *manifesto della distribuzione* è un documento JSON che descrive:

* Il modulo gemello del modulo **agente IoT Edge,** che include tre componenti:
  * Immagine del contenitore per ogni modulo eseguito nel dispositivo.
  * Credenziali per accedere ai registri del contenitore privato che contengono immagini del modulo.
  * Istruzioni su come ogni modulo deve essere creato e gestito.
* Il modulo gemello dell'**agente IoT Edge**, che include le modalità di flusso dei messaggi tra i moduli e con l'hub IoT.
* Le proprietà desiderate di eventuali gemelli modulo aggiuntivi (opzionale).

Tutti i dispositivi IoT Edge devono essere configurati con un manifesto della distribuzione. Un runtime IoT Edge appena installato segnala un codice di errore finché non verrà configurato con un manifesto valido.

Nelle esercitazioni di Azure IoT Edge viene creato un manifesto della distribuzione seguendo una procedura guidata nel portale di Azure IoT Edge. È anche possibile applicare un manifesto della distribuzione a livello di codice usando REST o IoT Hub Service SDK. Per altre informazioni, vedere [Informazioni sulle distribuzioni IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Creare un manifesto di distribuzione

A livello generale, un manifesto della distribuzione è un elenco di moduli gemelli configurati con le relative proprietà desiderate. Un manifesto della distribuzione indica a un dispositivo IoT Edge (o a un gruppo di dispositivi) quali moduli installare e come configurarli. I manifesti della distribuzione includono le *proprietà desiderate* per ogni modulo gemello. I dispositivi IoT Edge riportano le *proprietà segnalate* per ogni modulo.

In ogni manifesto della distribuzione sono necessari due moduli: `$edgeAgent`, e `$edgeHub`. Questi moduli costituiscono parte del runtime di IoT Edge che gestisce il dispositivo IoT Edge e i moduli in esecuzione su di esso. Per altre informazioni su questi moduli, vedere [Comprendere il runtime di IoT Edge e la relativa architettura](iot-edge-runtime.md).

Oltre ai due moduli di runtime, è possibile aggiungere fino a 20 moduli personalizzati da eseguire su un dispositivo IoT Edge.

È valido anche un manifesto della distribuzione contenente solo il runtime di IoT Edge (edgeAgent and edgeHub).

I manifesti di distribuzione seguono questa struttura:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
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
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurare i moduli

È necessario indicare al runtime di IoT Edge come installare i moduli nella distribuzione. L'agente IoT Edge è il componente di runtime che gestisce l'installazione, gli aggiornamenti e i rapporti di stato di un dispositivo IoT Edge. Pertanto, il $edgeAgent modulo gemello contiene le informazioni di configurazione e gestione per tutti i moduli. Queste informazioni includono i parametri di configurazione per l'agente IoT Edge stesso.

Per un elenco completo delle proprietà che possono o devono essere incluse, vedere [Proprietà dell'agente Edge IoT e dell'hub IoT Edge](module-edgeagent-edgehub.md).

Le proprietà di $edgeAgent seguono questa struttura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
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
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Dichiarare le route

L'hub di IoT Edge gestisce la comunicazione tra i moduli, l'hub IoT e tutti i dispositivi foglia. Pertanto, il modulo gemello $edgeHub contiene una proprietà desiderata chiamata *routes* che dichiara come i messaggi vengono passati all'interno di una distribuzione. È possibile che nella stessa distribuzione siano presenti più route.

Le route vengono dichiarate nelle proprietà desiderate di **$edgeHub** con la sintassi seguente:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Per ogni route è necessaria un'origine e un sink, mentre la condizione è un elemento facoltativo che è possibile usare per filtrare i messaggi.

### <a name="source"></a>Source (Sorgente)

L'origine specifica da dove provengono i messaggi. IoT Edge può instradare i messaggi da moduli o dispositivi foglia.

Utilizzando gli SDK IoT, i moduli possono dichiarare code di output specifiche per i messaggi usando la classe ModuleClient.Using the IoT SDKs, modules can declare specific output queues for their messages using the ModuleClient class. Le code di output non sono necessarie, ma sono utili per la gestione di più route. I dispositivi foglia possono usare la classe DeviceClient degli SDK IoT per inviare messaggi ai dispositivi gateway IoT Edge nello stesso modo in cui inviano messaggi all'hub IoT. Per altre informazioni, vedere [Comprendere e usare gli SDK dell'hub IoT](../iot-hub/iot-hub-devguide-sdks.md)di Azure.For more information, see Understand and use Azure IoT Hub Sdk .

La proprietà di origine può essere uno dei valori seguenti:

| Source (Sorgente) | Descrizione |
| ------ | ----------- |
| `/*` | Tutte le notifiche da dispositivo a cloud o dei dispositivi gemelli le notifiche da qualsiasi dispositivo foglia o modulo |
| `/twinChangeNotifications` | Qualsiasi modifica gemella (proprietà segnalate) provenienti da qualsiasi dispositivo foglia o modulo |
| `/messages/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo tramite un output o un output o da un dispositivo foglia |
| `/messages/modules/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo con o senza output |
| `/messages/modules/<moduleId>/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con o senza output |
| `/messages/modules/<moduleId>/outputs/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con output |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo specifico con un output specifico |

### <a name="condition"></a>Condizione

La condizione è facoltativa in una dichiarazione di route. Se si desidera passare tutti i messaggi dall'origine al sink, è sufficiente omettere completamente la clausola **WHERE.** In alternativa è possibile usare il [linguaggio di query di hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) per filtrare alcuni messaggi o tipi di messaggio che soddisfano la condizione. Le route di IoT Edge non supportano i messaggi di filtro in base a tag o proprietà gemelli.

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

IoT Edge offre garanzie di tipo at-least-once. L'hub IoT Edge archivia i messaggi localmente nel caso in cui una route non possa recapitare il messaggio al sink. Ad esempio, se l'hub Edge IoT non riesce a connettersi all'hub IoT o se il modulo di destinazione non è connesso.

L'hub IoT Edge archivia i `storeAndForwardConfiguration.timeToLiveSecs` messaggi fino all'ora specificata nella proprietà delle [proprietà desiderate dell'hub IoT Edge](module-edgeagent-edgehub.md).

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
        "schemaVersion": "1.0",
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
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco completo delle proprietà che possono o devono essere incluse in $edgeAgent e $edgeHub, vedere [Proprietà dell'agente Edge IoT e dell'hub IoT Edge](module-edgeagent-edgehub.md).

* Dopo aver appreso come usare i moduli IoT Edge, passare alla pagina [Informazioni sui requisiti e gli strumenti per sviluppare moduli di IoT Edge](module-development.md).
