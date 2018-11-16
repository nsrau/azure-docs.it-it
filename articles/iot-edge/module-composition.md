---
title: Composizione di un modulo Azure IoT Edge | Microsoft Docs
description: Informazioni su come un manifesto della distribuzione dichiara quali moduli distribuire, come distribuirli e come creare tra di loro route di messaggi.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3201e8509e7c63bb0d9b607d26292bd85e2b605d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569234"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Informazioni su come distribuire moduli e definire route in IoT Edge

Ogni dispositivo IoT Edge esegue almeno due moduli: $edgeAgent e $edgeHub, che costituiscono il runtime di IoT Edge. Oltre a questi moduli, qualsiasi dispositivo IoT Edge può eseguire più moduli per portare a termine un numero qualsiasi di processi. Quando si distribuiscono contemporaneamente tutti questi moduli in un dispositivo, è necessario adottare un approccio per dichiarare quali moduli devono essere inclusi e come devono interagire tra loro. 

Il *manifesto della distribuzione* è un documento JSON che descrive:

* La configurazione dell'agente di Edge, che include l'immagine del contenitore per ogni modulo, le credenziali per l'accesso ai registri contenitori privati e le istruzioni per creare e gestire ogni modulo.
* La configurazione dell'hub di Edge, nonché le modalità di flusso dei messaggi tra i moduli e con l'hub IoT.
* Facoltativamente, le proprietà desiderate dei dispositivi gemelli.

Tutti i dispositivi IoT Edge devono essere configurati con un manifesto della distribuzione. Un runtime IoT Edge appena installato segnala un codice di errore finché non verrà configurato con un manifesto valido. 

Nelle esercitazioni di Azure IoT Edge viene creato un manifesto della distribuzione seguendo una procedura guidata nel portale di Azure IoT Edge. È anche possibile applicare un manifesto della distribuzione a livello di codice usando REST o IoT Hub Service SDK. Per altre informazioni, vedere [Informazioni sulle distribuzioni IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Creare un manifesto di distribuzione

A livello generale, il manifesto della distribuzione configura le proprietà desiderate di un modulo gemello per i moduli IoT Edge distribuiti in un dispositivo IoT Edge. Due di questi moduli sono sempre presenti: `$edgeAgent` e `$edgeHub`.

È valido anche un manifesto di distribuzione contenente solo il runtime di IoT Edge (agente e hub).

Il manifesto segue questa struttura:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurare i moduli

È necessario indicare al runtime di IoT Edge come installare i moduli nella distribuzione. Le informazioni di configurazione e gestione di tutti i moduli vengono incluse nella proprietà desiderate di **$edgeAgent**. Queste informazioni includono anche i parametri di configurazione dell'agente di Edge. 

Per un elenco completo delle proprietà che possono o devono essere incluse, vedere [Properties of the Edge agent and Edge hub](module-edgeagent-edgehub.md) (Proprietà dell'agente di Edge e dell'hub di Edge).

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
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Dichiarare le route

L'hub Edge consente di instradare in modo dichiarativo i messaggi tra i moduli e tra i moduli e l'hub IoT. L'hub di Edge gestisce anche tutte le comunicazioni e le informazioni sulla route sono quindi incluse nelle proprietà desiderate di **$edgeHub**. È possibile che nella stessa distribuzione siano presenti più route.

Le route vengono dichiarate nelle proprietà desiderate di **$edgeHub** con la sintassi seguente:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Per ogni route è necessaria un'origine e un sink, mentre la condizione è un elemento facoltativo che è possibile usare per filtrare i messaggi. 


### <a name="source"></a>Sorgente
L'origine specifica da dove provengono i messaggi. Può essere uno dei valori seguenti:

| Sorgente | DESCRIZIONE |
| ------ | ----------- |
| `/*` | Tutti i messaggi da dispositivo a cloud da qualsiasi dispositivo o modulo |
| `/messages/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un dispositivo o un modulo con o senza output |
| `/messages/modules/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo con o senza output |
| `/messages/modules/{moduleId}/*` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} senza output |
| `/messages/modules/{moduleId}/outputs/*` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} con output |
| `/messages/modules/{moduleId}/outputs/{output}` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} con {output} |

### <a name="condition"></a>Condizione
La condizione è facoltativa in una dichiarazione di route. Se si intende passare tutti i messaggi dal sink all'origine, escludere interamente la clausola **WHERE**. In alternativa è possibile usare il [linguaggio di query di hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) per filtrare alcuni messaggi o tipi di messaggio che soddisfano la condizione.

I messaggi che passano tra i moduli in IoT Edge sono formattati come i messaggi che passano tra i dispositivi e l'hub IoT di Azure. Tutti i messaggi sono formattati come JSON e hanno i parametri **systemProperties**, **appProperties** e **body**. 

È possibile creare query in base a tutti e tre i parametri con la sintassi seguente: 

* Proprietà di sistema: `$<propertyName>` o `{$<propertyName>}`
* Proprietà dell'applicazione: `<propertyName>`
* Proprietà del corpo: `$body.<propertyName>` 

Per esempi su come creare query in base alle proprietà dei messaggi, vedere [Espressioni di query per route di messaggi da dispositivo a cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Un esempio specifico per IoT Edge riguarda il filtraggio dei messaggi inviati da un dispositivo foglia a un dispositivo gateway. I messaggi provenienti dai moduli contengono una proprietà di sistema denominata **connectionModuleId**. Se si intende eseguire il routing del messaggi direttamente da dispositivi foglia all'hub IoT, quindi, usare la route seguente per escludere i messaggi dei moduli:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink
Il sink definisce dove vengono inviati i messaggi. Può essere uno dei valori seguenti:

| Sink | DESCRIZIONE |
| ---- | ----------- |
| `$upstream` | Inviare il messaggio all'hub IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Inviare il messaggio all'input `{input}` del modulo`{moduleId}` |

IoT Edge offre garanzie di tipo at-least-once. L'hub di Edge archivia i messaggi in locale nel caso in cui una route non riesca a recapitare il messaggio al relativo sink. Ad esempio, se l'hub di Edge non riesce a connettersi all'hub IoT o il modulo di destinazione non è connesso.

L'hub di Edge archivia i messaggi fino all'ora specificata nella proprietà `storeAndForwardConfiguration.timeToLiveSecs` delle [proprietà desiderate dell'hub di Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definire o aggiornare le proprietà desiderate 

Il manifesto di distribuzione può specificare le proprietà desiderate per il modulo gemello di ogni modulo distribuito nel dispositivo IoT Edge. Quando nel manifesto di distribuzione vengono specificate le proprietà desiderate, queste sovrascrivono le proprietà desiderate attualmente specificate nel dispositivo gemello del modulo.

Se nel manifesto della distribuzione non si specificano le proprietà desiderate del dispositivo gemello di un modulo, l'hub IoT non modifica il dispositivo in alcun modo e sarà possibile impostare le proprietà desiderate a livello di codice.

Gli stessi meccanismi che consentono di modificare i dispositivi gemelli vengono usati per modificare i moduli gemelli. Per altre informazioni, vedere il [modulo per sviluppatori sui dispositivi gemelli](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Esempio di manifesto della distribuzione

Questo è un esempio di un documento JSON di manifesto della distribuzione.

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
                "password": "{password}",
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
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
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

* Per un elenco completo delle proprietà che possono o devono essere incluse in $edgeAgent e $edgeHub, vedere [Properties of the Edge agent and Edge hub](module-edgeagent-edgehub.md) (Proprietà dell'agente di Edge e dell'hub di Edge).

* Dopo aver appreso come usare i moduli IoT Edge, passare alla pagina [Informazioni sui requisiti e gli strumenti per sviluppare moduli di IoT Edge](module-development.md).
