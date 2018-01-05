---
title: Composizione di un modulo Azure IoT Edge | Microsoft Docs
description: Informazioni su come comporre i moduli Azure IoT Edge e in che modo riusarli
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f3bc2f14b182e502c651ff44ef49b88cd34e1f50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Informazioni su come usare, configurare e riusare i moduli IoT Edge - Anteprima

Azure IoT Edge consente di comporre più moduli IoT Edge prima di distribuirli nei dispositivi IoT Edge. Questo articolo illustra i concetti più importanti della composizione di più moduli IoT Edge prima della distribuzione. 

## <a name="the-deployment-manifest"></a>Manifesto della distribuzione
Il *manifesto della distribuzione* è un documento JSON che descrive:

* I moduli IoT Edge da distribuire, oltre alle relative opzioni di creazione e gestione;
* La configurazione dell'hub Edge, con la descrizione della procedura di flusso dei messaggi tra i moduli e tra i moduli e l'hub IoT;
* Facoltativamente, i valori da impostare nelle proprietà desiderate dei dispositivi gemelli dei moduli, per configurare le singole applicazioni dei moduli.

Nelle esercitazioni di Azure IoT Edge viene creato un manifesto della distribuzione seguendo una procedura guidata nel portale di Azure IoT Edge. È anche possibile applicare un manifesto della distribuzione a livello di codice usando REST o IoT Hub Service SDK. Per altre informazioni sulle distribuzioni IoT Edge, fare riferimento a [Deploy and monitor][lnk-deploy] (Distribuire e monitorare).

A livello generale, il manifesto della distribuzione configura le proprietà desiderate dei moduli IoT Edge distribuiti in un dispositivo IoT Edge. Due di questi moduli sono sempre presenti: l'agente Edge e l'hub Edge.

Il manifesto segue questa struttura:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
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

Un esempio di un manifesto della distribuzione è riportato alla fine di questa sezione.

> [!IMPORTANT]
> Tutti i dispositivi IoT Edge devono essere configurati con un manifesto della distribuzione. Un runtime IoT Edge appena installato segnala un codice di errore finché non verrà configurato con un manifesto valido. 

### <a name="specify-the-modules"></a>Specificare i moduli
Le proprietà desiderate del dispositivo gemello del modulo dell'agente Edge contengono i moduli desiderati, le opzioni di configurazione e gestione e i parametri di configurazione per l'agente Edge.

A livello generale, questa sezione del manifesto contiene i riferimenti alle immagini dei moduli e alle opzioni di gestione per i moduli di runtime IoT Edge (agente Edge e hub Edge) e i moduli specificati dall'utente.

Per una descrizione dettagliata di questa sezione del manifesto, fare riferimento alle [proprietà desiderate dell'agente Edge][lnk-edgeagent-desired].

> [!NOTE]
> Un manifesto della distribuzione contenente solo il runtime di IoT Edge (agente e hub) è valido.


### <a name="specify-the-routes"></a>Specificare le route
L'hub Edge consente di instradare in modo dichiarativo i messaggi tra i moduli e tra i moduli e l'hub IoT.

Le route hanno la sintassi seguente:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

L'*origine* può essere un elemento seguente qualsiasi:

| Sorgente | DESCRIZIONE |
| ------ | ----------- |
| `/*` | Tutti i messaggi da dispositivo a cloud da qualsiasi dispositivo o modulo |
| `/messages/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un dispositivo o un modulo con o senza output |
| `/messages/modules/*` | Qualsiasi messaggio da dispositivo a cloud inviato da un modulo con o senza output |
| `/messages/modules/{moduleId}/*` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} senza output |
| `/messages/modules/{moduleId}/outputs/*` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} con output |
| `/messages/modules/{moduleId}/outputs/{output}` | Qualsiasi messaggio da dispositivo a cloud inviato da {moduleId} con {output} |

La condizione può essere una condizione qualsiasi supportata dal [linguaggio di query dell'hub IoT] [ lnk-iothub-query] per le regole di routing dell'hub IoT.

Il sink può essere uno dei seguenti:

| Sink | DESCRIZIONE |
| ---- | ----------- |
| `$upstream` | Inviare il messaggio all'hub IoT |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Inviare il messaggio all'input `{input}` del modulo`{moduleId}` |

È importante notare che l'hub Edge fornisce garanzie di tipo At-Least-Once, ovvero i messaggi vengono archiviati in locale nel caso in cui una route non possa recapitare il messaggio al relativo sink, ad esempio se l'hub Edge non può connettersi all'hub IoT o il modulo di destinazione non è connesso.

L'hub Edge archivia i messaggi fino all'ora specificata nella proprietà `storeAndForwardConfiguration.timeToLiveSecs` delle proprietà desiderate dell'hub Edge.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Definizione delle proprietà desiderate del dispositivo gemello del modulo

Il manifesto della distribuzione consente di specificare le proprietà desiderate del dispositivo gemello di ogni modulo utente specificato nella sezione dell'agente Edge.

Quando nel manifesto di distribuzione vengono specificate le proprietà desiderate, queste sovrascrivono le proprietà desiderate attualmente specificate nel dispositivo gemello del modulo.

Se nel manifesto della distribuzione non si specificano le proprietà desiderate del dispositivo gemello di un modulo, l'hub IoT non modifica il dispositivo in alcun modo e sarà possibile impostare le proprietà desiderate a livello di codice.

### <a name="deployment-manifest-example"></a>Esempio di manifesto della distribuzione

Questo è un esempio di un documento JSON di manifesto della distribuzione.

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

## <a name="reference-edge-agent-module-twin"></a>Riferimento: dispositivo gemello del modulo dell'agente Edge

Il dispositivo gemello del modulo per l'agente Edge è denominato `$edgeAgent` e coordina le comunicazioni tra l'agente Edge in esecuzione su un dispositivo e l'hub IoT.
Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. Per altre informazioni su come distribuire i moduli nei dispositivi IoT Edge, vedere [Deployment and monitoring][lnk-deploy] (Distribuzione e monitoraggio).

### <a name="edge-agent-twin-desired-properties"></a>Proprietà desiderate del dispositivo gemello dell'agente Edge

| Proprietà | DESCRIZIONE | Obbligatoria |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| runtime.type | Deve essere "docker" | Sì |
| runtime.settings.minDockerVersion | Impostata sulla versione minima di Docker richiesta da questo manifesto della distribuzione | Sì |
| runtime.settings.loggingOptions | Un file JSON in formato stringa contenente le opzioni di registrazione per il contenitore dell'agente Edge. [Opzioni di registrazione di Docker][lnk-docker-logging-options] | No  |
| systemModules.edgeAgent.type | Deve essere "docker" | Sì |
| systemModules.edgeAgent.settings.image | URI dell'immagine dell'agente Edge. Attualmente, l'agente Edge non è in grado di aggiornarsi automaticamente. | Sì |
| systemModules.edgeAgent.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'agente Edge. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| systemModules.edgeAgent.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |
| systemModules.edgeHub.type | Deve essere "docker" | Sì |
| systemModules.edgeHub.status | Deve essere "running" | Sì |
| systemModules.edgeHub.restartPolicy | Deve essere "always" | Sì |
| systemModules.edgeHub.settings.image | URI dell'immagine dell'hub Edge. | Sì |
| systemModules.edgeHub.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore dell'hub Edge. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| systemModules.edgeHub.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |
| modules.{moduleId}.version | Una stringa definita dall'utente che rappresenta la versione di questo modulo. | Sì |
| modules.{moduleId}.type | Deve essere "docker" | Sì |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | Sì |
| modules.{moduleId}.settings.image | URI dell'immagine del modulo. | Sì |
| modules.{moduleId}.settings.createOptions | Un file JSON in formato stringa contenente le opzioni per la creazione del contenitore del modulo. [Opzioni di creazione di Docker][lnk-docker-create-options] | No  |
| modules.{moduleId}.configuration.id | ID della distribuzione che ha distribuito questo modulo. | Impostato dall'hub IoT quando viene applicato questo manifesto usando una distribuzione. Non fa parte di un manifesto della distribuzione. |

### <a name="edge-agent-twin-reported-properties"></a>Proprietà segnalate del dispositivo gemello dell'agente Edge

Le proprietà segnalate dell'agente Edge includono tre tipi di informazioni principali:

1. Lo stato dell'applicazione delle proprietà desiderate visualizzate per ultime;
2. Lo stato dei moduli attualmente in esecuzione nel dispositivo, come segnalato dall'agente Edge;
3. Una copia delle proprietà desiderate attualmente in esecuzione nel dispositivo.

Quest'ultima informazione è utile nel caso in cui le proprietà desiderate più recenti non vengano applicate correttamente dal runtime e il dispositivo esegua ancora un manifesto della distribuzione precedente.

> [!NOTE]
> Le proprietà segnalate dell'agente Edge sono utili in quanto possono essere sottoposte a query con il [linguaggio di query dell'hub IoT][lnk-iothub-query] per esaminare lo stato delle distribuzioni su larga scala. Per altre informazioni su come usare questa funzionalità, fare riferimento a [Distribuzioni][lnk-deploy].

La tabella seguente non include le informazioni copiate dalle proprietà desiderate.

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| lastDesiredVersion | Questo int si riferisce all'ultima versione delle proprietà desiderate elaborate dall'agente Edge. |
| lastDesiredStatus.code | Questo è il codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'agente Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `412` Invalid schema version, `417` the desired properties are empty, `500` Failed |
| lastDesiredStatus.description | Descrizione di testo dello stato |
| deviceHealth | `healthy` se lo stato di runtime di tutti i moduli è `running` o `stopped`, altrimenti `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` se lo stato di runtime di tutti i moduli impostati da {deploymentId} della distribuzione è `running` o `stopped`, altrimenti `unhealthy` |
| runtime.platform.OS | Segnalazione del sistema operativo in esecuzione nel dispositivo |
| runtime.platform.architecture | Segnalazione dell'architettura della CPU nel dispositivo |
| systemModules.edgeAgent.runtimeStatus | Stato segnalato dell'agente Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrizione di testo dello stato segnalato dell'agente Edge. |
| systemModules.edgeHub.runtimeStatus | Stato corrente dell'hub Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Descrizione di testo dello stato corrente dell'hub Edge se unhealthy. |
| systemModules.edgeHub.exitCode | Se è stata chiusa, il codice di uscita segnalato dal contenitore dell'hub Edge |
| systemModules.edgeHub.startTimeUtc | Ora dell'ultimo avvio dell'hub Edge |
| systemModules.edgeHub.lastExitTimeUtc | Ora dell'ultima chiusura dell'hub Edge |
| systemModules.edgeHub.lastRestartTimeUtc | Ora dell'ultimo riavvio dell'hub Edge |
| systemModules.edgeHub.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |
| modules.{moduleId}.runtimeStatus | Stato corrente del modulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Descrizione di testo dello stato corrente del modulo se unhealthy. |
| modules.{moduleId}.exitCode | Se è stata chiusa, il codice di uscita segnalato dal contenitore del modulo |
| modules.{moduleId}.startTimeUtc | Ora dell'ultimo avvio del modulo |
| modules.{moduleId}.lastExitTimeUtc | Ora dell'ultima chiusura del modulo |
| modules.{moduleId}.lastRestartTimeUtc | Ora dell'ultimo riavvio del modulo |
| modules.{moduleId}.restartCount | Numero di tentativi di riavvio del modulo nell'ambito dei criteri di riavvio. |

## <a name="reference-edge-hub-module-twin"></a>Riferimento: dispositivo gemello del modulo dell'hub Edge

Il dispositivo gemello del modulo per l'hub Edge è denominato `$edgeHub` e coordina le comunicazioni tra l'hub Edge in esecuzione su un dispositivo e l'hub IoT.
Le proprietà desiderate vengono impostate durante l'applicazione di un manifesto della distribuzione in un dispositivo specifico nell'ambito di una distribuzione di un singolo dispositivo o su larga scala. Per altre informazioni su come distribuire i moduli nei dispositivi IoT Edge, vedere [Distribuzioni][lnk-deploy].

### <a name="edge-hub-twin-desired-properties"></a>Proprietà desiderate del dispositivo gemello dell'hub Edge

| Proprietà | DESCRIZIONE | Obbligatoria nel manifesto della distribuzione |
| -------- | ----------- | -------- |
| schemaVersion | Deve essere "1.0" | Sì |
| routes.{routeName} | Stringa che rappresenta una route dell'hub Edge. | L'elemento `routes` può essere presente, ma vuoto. |
| storeAndForwardConfiguration.timeToLiveSecs | Durata di conservazione dei messaggi in secondi da parte dell'hub Edge in caso di endpoint di routing disconnessi, ad esempio disconnessi dall'hub IoT o dal modulo locale | Sì |

### <a name="edge-hub-twin-reported-properties"></a>Proprietà segnalate del dispositivo gemello dell'hub Edge

| Proprietà | DESCRIZIONE |
| -------- | ----------- |
| lastDesiredVersion | Questo int si riferisce all'ultima versione delle proprietà desiderate elaborate dall'hub Edge. |
| lastDesiredStatus.code | Questo è il codice di stato che fa riferimento alle ultime proprietà desiderate visualizzate dall'hub Edge. Valori consentiti: `200` Success, `400` Invalid configuration, `500` Failed |
| lastDesiredStatus.description | Descrizione di testo dello stato |
| clients.{device or module identity}.status | Stato di connettività del dispositivo o del modulo. Valori possibili: {"connected" \| "disconnected"}. Solo le identità del modulo possono essere in stato disconnected. I dispositivi downstream che si connettono all'hub Edge vengono visualizzati solo se lo stato è connected. |
| clients.{device or module identity}.lastConnectTime | Ultima ora di connessione del dispositivo o del modulo |
| clients.{device or module identity}.lastDisconnectTime | Ultima ora di disconnessione del dispositivo o del modulo |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i moduli IoT Edge, passare alla pagina [Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev] (Informazioni sui requisiti e sugli strumenti per sviluppare moduli IoT Edge).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
