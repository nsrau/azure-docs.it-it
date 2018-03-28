---
title: Composizione di un modulo Azure IoT Edge | Microsoft Docs
description: Informazioni su come comporre i moduli Azure IoT Edge e in che modo riusarli
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4b59a715919e38e68c3b7518932617e9950940e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Informazioni su come usare, configurare e riusare i moduli IoT Edge - Anteprima

Ogni dispositivo IoT Edge esegue almeno due moduli: $edgeAgent e $edgeHub, che costituiscono il runtime di IoT Edge. Oltre a questi due moduli standard, qualsiasi dispositivo IoT Edge può eseguire vari moduli per portare a termine qualsiasi numero di processi. Quando si distribuiscono contemporaneamente tutti questi moduli in un dispositivo, è necessario un modo per dichiarare quali moduli devono essere inclusi e come devono interagire tra loro. 

Il *manifesto della distribuzione* è un documento JSON che descrive:

* I moduli IoT Edge da distribuire, oltre alle relative opzioni di creazione e gestione.
* La configurazione dell'hub di Edge, nonché le modalità di flusso dei messaggi tra i moduli e con l'hub IoT.
* Facoltativamente, i valori da impostare nelle proprietà desiderate dei dispositivi gemelli dei moduli, per configurare le singole applicazioni dei moduli.

Tutti i dispositivi IoT Edge devono essere configurati con un manifesto della distribuzione. Un runtime IoT Edge appena installato segnala un codice di errore finché non verrà configurato con un manifesto valido. 

Nelle esercitazioni di Azure IoT Edge viene creato un manifesto della distribuzione seguendo una procedura guidata nel portale di Azure IoT Edge. È anche possibile applicare un manifesto della distribuzione a livello di codice usando REST o IoT Hub Service SDK. Per altre informazioni sulle distribuzioni IoT Edge, fare riferimento a [Deploy and monitor][lnk-deploy] (Distribuire e monitorare).

## <a name="create-a-deployment-manifest"></a>Creare un manifesto di distribuzione

A livello generale, il manifesto della distribuzione configura le proprietà desiderate di un modulo gemello per i moduli IoT Edge distribuiti in un dispositivo IoT Edge. Due di questi moduli sono sempre presenti: l'agente Edge e l'hub Edge.

È valido anche un manifesto di distribuzione contenente solo il runtime di IoT Edge (agente e hub).

Il manifesto segue questa struttura:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
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

Oltre a definire le proprietà desiderate dei moduli che si intende distribuire, è necessario indicare al runtime di IoT Edge come installarli. Le informazioni di configurazione e gestione di tutti i moduli vengono incluse nella proprietà desiderate di **$edgeAgent**. Queste informazioni includono anche i parametri di configurazione dell'agente di Edge. 

Per un elenco completo delle proprietà che possono o devono essere incluse, vedere [Properties of the Edge agent and Edge hub](module-edgeagent-edgehub.md) (Proprietà dell'agente di Edge e dell'hub di Edge).

Le proprietà di $edgeAgent seguono questa struttura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
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
La condizione è facoltativa in una dichiarazione di route. Se si intende passare tutti i messaggi dal sink all'origine, escludere interamente la clausola **WHERE**. In alternativa è possibile usare il [linguaggio di query di hub IoT][lnk-iothub-query] per filtrare alcuni messaggi o tipi di messaggio che soddisfano la condizione.

I messaggi di Azure IoT vengono formattati come JSON e hanno sempre almeno un parametro **body**. Ad esempio: 

```json
"message": {
    "body":{
        "ambient":{
            "temperature": 54.3421,
            "humidity": 25
        },
        "machine":{
            "status": "running",
            "temperature": 62.2214
        }
    },
    "appProperties":{
        ...
    }
}
```

Dato questo messaggio di esempio, è possibile definire varie condizioni, ad esempio:
* `WHERE $body.machine.status != "running"`
* `WHERE $body.ambient.temperature <= 60 AND $body.machine.temperature >= 60`

La condizione può essere usata anche per ordinare i tipi di messaggio, ad esempio in un gateway che intende instradare i messaggi provenienti da dispositivi foglia. I messaggi provenienti da moduli contengono una proprietà specifica denominata **connectionModuleId**. Se si intende eseguire il routing del messaggi direttamente da dispositivi foglia all'hub IoT, quindi, usare la route seguente per escludere i messaggi dei moduli:
* `FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream`

### <a name="sink"></a>Sink
Il sink definisce dove vengono inviati i messaggi. Può essere uno dei valori seguenti:

| Sink | DESCRIZIONE |
| ---- | ----------- |
| `$upstream` | Inviare il messaggio all'hub IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Inviare il messaggio all'input `{input}` del modulo`{moduleId}` |

È importante notare che l'hub di Edge fornisce garanzie di tipo At-Least-Once, ovvero i messaggi vengono archiviati in locale nel caso in cui una route non possa recapitare il messaggio al relativo sink, ad esempio se l'hub di Edge non può connettersi all'hub IoT o il modulo di destinazione non è connesso.

L'hub di Edge archivia i messaggi fino all'ora specificata nella proprietà `storeAndForwardConfiguration.timeToLiveSecs` delle [proprietà desiderate dell'hub di Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definire o aggiornare le proprietà desiderate 

Il manifesto di distribuzione può specificare le proprietà desiderate per il modulo gemello di ogni modulo distribuito nel dispositivo IoT Edge. Quando nel manifesto di distribuzione vengono specificate le proprietà desiderate, queste sovrascrivono le proprietà desiderate attualmente specificate nel dispositivo gemello del modulo.

Se nel manifesto della distribuzione non si specificano le proprietà desiderate del dispositivo gemello di un modulo, l'hub IoT non modifica il dispositivo in alcun modo e sarà possibile impostare le proprietà desiderate a livello di codice.

Gli stessi meccanismi che consentono di modificare i dispositivi gemelli vengono usati per modificare i moduli gemelli. Per altre informazioni, vedere la [guida per sviluppatori sui dispositivi gemelli](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins).   

## <a name="deployment-manifest-example"></a>Esempio di manifesto della distribuzione

Questo è un esempio di un documento JSON di manifesto della distribuzione.

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

* Dopo aver appreso come usare i moduli IoT Edge, passare alla pagina [Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev] (Informazioni sui requisiti e sugli strumenti per sviluppare moduli IoT Edge).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
